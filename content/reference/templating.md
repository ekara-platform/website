---
title: Templating
---


The templating is a data-driven mechanism allowing to generate textual output.

The textual output will be generated passing at runtime a [**data structure**](#data-structure) to a template file. To access the data structure in a template you just need to define annotations delimited by `{{` and `}}`.



{{% notice info %}}
Please refer to the [official Go template ](https://golang.org/pkg/text/template/) documentation for more information about annotations syntaxis. 
{{% /notice %}}

In Ekara we distinguish two kind of templates:


* [**Descriptors**](#template-in-descriptors): both **environment** or **component** descriptors are automatically process as templates.

* [**Components**](#template-in-components)Components: a component can specify files which will be processed as templates.


## Data structure

Into a template the top most variable of the data structure is access by `{{.}}`.

This means that all template annotations should start with a dot `.`

### Structure content


#### `.Vars` 

The `.Vars` section of the structure contains the accumulation of all the `vars` defined in all descriptors and also the `param-file` given at runtime for example by the CLI or the running environment itself. See [**here**]({{< ref "descriptor.md#vars" >}}) the detail about how `vars` are accumulated and eventually overwritten based on their precedence.

Example :

Having this `param-file` pased at runtime by the CLI:
```yaml
#parameters file from the CLI
a:
  b:
    c:
      d: cli_value1
```

And a descriptor defining some variables:
```yaml
#descriptor variables
vars:
  e: descriptor_value2
  f: descriptor_value3
```

Will let you use the following template annotations:
```
My templated content:
  Value 1: {{.Vars.a.b.c.d}}
  Value 2: {{.Vars.e}}
  Value 3: {{.Vars.f}}
```

To generate this textual output: 
```
My templated content:
  Value 1: cli_value1
  Value 2: descriptor_value2
  Value 3: descriptor_value3
```

#### `.Model`

The `.Model` section of the structure will contains the environment which will be deployed. 

{{% notice warning %}}
The `.Model` section of the structure is not yet implemented.
{{% /notice %}}

To be done

- Reference on the exposed interface documentation (https://godoc.org/github.com/ekara-platform/model/tmodel)

- Explanation on how access to the collection content ( with examples for providers and node sets...)
 

## Template in descriptors

Both **environment** or **component** descriptors are automatically process as templates. 

### Content available in templated descriptors

The `.Vars` section of the data structure is available as template values within descriptors.

Because the `.Vars` section of the data structure is populated sequentially based on the processing of all descriptors part of the environment a given descriptor can only access to:

* the content of the the `param-file` given at runtime for example by the CLI or the running environment itself
* the `vars` that it itself define 
* the `vars` of other descriptors previously parsed and processed.

The example bellow shows how descriptors are sequentially processed and which `vars` are accessible by which descriptor. 
![Example image](/img/sequence.png)

This example shows one environment descriptor and tree component descriptors which will be parsed and processed following the bubbles order: **1**, **2**, **3** and finally **4**. 

As previously said, because a descriptor can only use, the `param-file` content, its own `vars` and `vars` previously parsed and processed therefore each descriptor will have acces to:

- **1** the environment descriptor has access to *key1* and *key2*
- **2** the distribution descriptor has access to *key1*, *key2* and *key3*
- **3** the core-stack descriptor has access to *key1*, *key2*, *key3* and *key4*
- **4** the my_component descriptor has access to *key1*, *key2*, *key3*, *key4* and *key5*


Base on our example the templated **Environment descriptor** could be:
```yaml
ekara:
  base: someBase
  distribution:
    repository: ekara-platform/distribution
  components:
    my_component:
      repository: ekara-platform/my_component    
vars:
  key2: value2				
orchestrator:
  component: my_component
  params:
    param_key1: {{ .Vars.key1 }}
    param_key2: {{ .Vars.key2 }}
```    


### Content not available in templated descriptors

The `.Model` section of the data structure is **not** available as template value within descriptors.

This is due to the fact that the environment to deploy will be completely built only when the last descriptor part of the environment has been parsed.


## Template in components

When templates are defined into a component then this component will no be use as is but it will be previously duplicated in order to be templated.

![Example image](/img/template-component.png)



[**test**]({{< ref "descriptor.md#templates" >}})


