---
title: Environment
weight: 200
---

To deploy a fully functional environment, the Ekara engine must first build an internal model of the environment. The model describes every aspect of the environment, including cloud provider(s) to use, machine node(s) to create or software stack(s) to deploy.

## Components

The environment model is built from one or more component(s):

* A component lives in a source repository which is fetched dynamically when necessary,
* If a component contains an `ekara.yaml` file, it will be parsed and contribute to the environment model.

{{% notice note %}}
Read on about [the component model]({{<ref "components.md" >}}) for details.
{{% /notice %}}

## Descriptor

The Ekara descriptor is a YAML file. By default it is named `ekara.yaml` and placed at the root level of a source repository:

```plain
<root>
    |-- ekara.yaml
```

{{% notice note %}}
Read on about [the descriptor syntax]({{<ref "descriptor.md" >}}) for details.
{{% /notice %}}

## Templating 

Ekara uses the Go [html/template](https://golang.org/pkg/html/template/) library for its template engine:

* Ekara descriptors are always templated,
* Arbitrary files in components can also be templated if needed.

{{% notice note %}}
Read on about [templating]({{<ref "templating.md" >}}) for details.
{{% /notice %}}
