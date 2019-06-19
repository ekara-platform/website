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


To be done

- Structure creation lifecycle

    - First CLI/runtime

    - Then descriptor vars

    - Then upper component vars.


#### `.Vars` 

The `.Vars` section of the structure contains the accumulation of all the `Vars` defined in all descriptors and also the `param-file` given at runtime for example by the CLI or the running environment itself. See [**here**]({{< ref "descriptor.md#vars" >}}) the detail about how `Vars` are accumulated and eventually overwritten based on their precedence.

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

- Reference on the exposed interface documentation
- Explanation on how access to the collection content ( with examples for providers and node sets...)
 


## Template in descriptors

Both **environment** or **component** descriptors are automatically process as templates. 

### Content available in templated descriptors

Because descriptors are processed sequentially at runtime they can only access to the `.Vars` previously parsed and processed.

The example bellow shows how descriptors are sequentially processed and which `.Vars` are accessible by which descriptor. 
![Example image](/img/sequence.png)

- Dependency on the structure life cycle.

### Content not available in templated descriptors

## Template in components

When templates are defined into a component then this component will no be use as is but it will be previously duplicated in order to be templated.


[**test**]({{< ref "descriptor.md#templates" >}})


