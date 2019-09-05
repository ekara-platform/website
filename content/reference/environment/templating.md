---
title: Templating
---

Templating is a data-driven mechanism to generate a particular textual output. In Ekara, templating is done automatically:

* Descriptors are always templated,
* Arbitrary files in components can also be templated if needed.

{{% notice note %}}
Ekara uses the Go [text/template](https://golang.org/pkg/text/template) library as its template engine.
{{% /notice %}}

## Basics

Templates are executed by applying them to a data structure, provided by Ekara. The data structure defines the following top-level elements:

* `Vars`, which contains user-defined variables.
* `Model`, which contains functions to access a read-only view of the environment model.
* `Runtime`, which contains functions to access a read-only view of execution data.

The input text for a template is UTF-8-encoded text in any format. Actions (data evaluations or control structure) are delimited by `{{` and `}}`:

{{< highlight yaml >}}
name: {{ .Vars.myProject.name }}
{{< /highlight >}}

All text outside those delimiters is copied to the output unchanged. 

{{% notice tip %}}
Execution of the template walks the structure and sets the cursor, represented by a period `.` and called "dot", to the value at the current location in the structure as execution proceeds. For instance, in loops the dot become a reference to the current item. It can also be set explictly using the `with` action.
{{% /notice %}}

## Variables

The `.Vars` section of the structure contains the accumulation of:

* The external variables (for instance given by a var file from the command-line).
* The descriptor variables, given by the `vars` section.

Because the `.Vars` section is populated sequentially during the environment model aggregation, a particular descriptor can only be templated by:

* The external variables,
* The variables declared up in its inheritance chain:
  * In the parent(s)
  * In the components referenced from the parent(s)
* Its own variables.

## Meta-model

The `.Model` section of the structure contains functions to access a read-only view of the environment model currently worked on. It can be think of as the meta-model of the Ekara engine. 

{{% notice note %}}
The meta-model exported interfaces are documented at https://godoc.org/github.com/ekara-platform/model/tmodel.
{{% /notice %}}

{{% notice info %}}
Accessing the meta-model during the construction of the model itself is limited. Only `.Model.Name`, `.Model.Qualifier` and `.Model.QualifiedName` are accessible when templating descriptors.
{{% /notice %}}

## Templating additional files

The descriptor is always templated but other files of a component can templated too. You can declare files to be templates like this:

{{< highlight yaml >}}
templates:
  - docker-compose.yaml
  - config/**/*.cfg
{{< /highlight >}}

Template paths must be relative to the descriptor file (i.e. the component root). [Glob patterns](https://github.com/gobwas/glob) can be used to match multiple files in a single expression. 

{{% notice info %}}
When templating additional files, all variables are available and meta-model functions can be used without restriction.
{{% /notice %}}

## Example

Consider the following "variable file" passed from the command-line:

{{< highlight yaml >}}
# Parameters file from the CLI
app:
  a: cli_value
{{< /highlight >}}

A parent defining the following variables:

{{< highlight yaml >}}
# Variables in the parent descriptor
vars:
  app:
    b: parent_{{ .Vars.app.a }}
    c: parent_value
{{< /highlight >}}

And a main descriptor defining the following variables:

{{< highlight yaml >}}
# Variables in the main descriptor
name: myEnv
qualifier: dev
vars:
  app:
    d: main_{{ .Model.QualifiedName }}_{{ .Vars.app.b }}
{{< /highlight >}}

Final values in the main descriptor will be:

* `{{ .Vars.app.a }}` will evaluate to `cli_value`  
* `{{ .Vars.app.b }}` will evaluate to `parent_cli_value`  
* `{{ .Vars.app.c }}` will evaluate to `parent_value`
* `{{ .Vars.app.d }}` will evaluate to `main_myEnv_qualifier_parent_cli_value`  


