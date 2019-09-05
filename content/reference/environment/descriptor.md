---
title: Descriptor syntax
---

The Ekara descriptor is a YAML document consisting of the following top-level sections:

* The [**general properties**](#general-properties), defining basic information of the environment.
* The [**ekara**](#ekara) section, defining the environment base and components.
* The [**vars**](#variables) section, declaring variables that can be used throughout all descriptors and templated files.
* The [**templates**](#templates) section, declaring files relative to the descriptor that needs templating.
* The [**providers**](#providers) section, declaring infrastructure providers capable of creating nodes.
* The [**orchestrator**](#orchestrator) section, declaring the Docker orchestrator to use.
* The [**nodes**](#nodes) section, declaring the physical nodes of the environment.
* The [**stacks**](#stacks) section, declaring the Docker stacks to deploy on the cluster.
* The [**tasks**](#tasks) section, declaring playbooks that can be executed on a particular event. 

## General properties

The following properties define the basic information of the environment:

{{< highlight yaml >}}
# Name of the environment (mandatory)
name: myEnvironment

# Optional qualifier of the environment (like dev, test, prod, ...)
qualifier: dev

# Optional description of the environment 
descriptor: This is my awesome ekara environment. 
{{< /highlight >}}

## Ekara

The `ekara` section controls the Ekara platform settings:

{{< highlight yaml >}}
ekara:
  # The base location for short-form repositories
  base: https://github.com

  # Parent component to inherit from
  parent:
    repository: ekara-platform/parent
    ref: v1
    
  # Additional components to be imported
  components:
    myComponent:
      repository: my-organization/my-component
      ref: v2
{{< /highlight >}}

{{% notice note %}}
Read more about [how components are discovered and parsed]({{< ref "components.md" >}}).
{{% /notice %}}

### Base

The base location is used to resolve short repository names for the descriptor only:

{{< highlight yaml >}}
ekara:
  base: https://a.given.base.com
  parent:
    repository: my-organization/parent
  components:
    myComponent:
      repository: my-organization/my-component
{{< /highlight >}}

The declaration above will result in the following resolved URLs:

* The parent: https://a.given.base.com/my-organization/distribution 
* The component: https://a.given.base.com/my-organization/my-component

{{% notice tip %}}
When no base is specified, Ekara will use `https://github.com` as default.
{{% /notice %}}

### Parent

The parent is a component from which everything will be inherited in the environment. A parent can also have a parent itself.

### Components

Additional components can be declared in the `components` section but will only be fetched and imported upon effective use in the descriptor. 

## Variables

An environment descriptor can define variables, which can then be used in templating:

{{< highlight yaml >}}
vars:
  var1_key: descriptor_var1_values 
  var2_key: descriptor_var2_values 
{{< /highlight >}}

A variable can only be used in templating if it already has been declared in:

* The current descriptor,
* A parent descriptor above,
* A component referenced from a parent descritor above,
* The command-line given parameters file.

{{% notice tip %}}
Variable precedence follow the [component precedence rules]({{< ref "components.md" >}}).
{{% /notice %}}

## Templates

Although the Ekara descriptor itself is always templated, if you want to template other files of the component you must declare them in the following section:

{{< highlight yaml >}}
templates:
  - docker-compose.yaml
  - config/**/*.conf
{{< /highlight >}}

Template paths must be relative to the descriptor file (i.e. the component root). [Glob patterns](https://github.com/gobwas/glob) can be used to match multiple files in a single expression.

{{% notice note %}}
Read more about [**templating**]({{< ref "templating.md" >}}) for more details.
{{% /notice %}}

## Providers

The `providers` section declares the cloud providers that can be used for creating node sets:

{{< highlight yaml >}}
providers:
  ek-aws:    
    # Name of the provider component
    component: ek-aws
    # Provider parameters applicable to all node sets (unless overridden)
    params: 
      region: eu-west-1
{{< /highlight >}}

## Orchestrator

The `orchestrator` section declares the container orchestrator that will be installed: 

{{< highlight yaml >}}
orchestrator:
  # Name of the orchestrator component
  component: ek-swarm
  # Orchestrator parameters applicable to all node sets
  params:
    someParam: someValue
  # Docker parameters applicable to all node sets
  docker:
    someParam: someValue
{{< /highlight >}}

{{% notice tip %}}
While the `params` section is used to configure the orchestrator, the `docker` section is used for Docker itself.
{{% /notice %}}

## Nodes

The `nodes` section declares the machines on which the environment will run, grouped by node sets:

{{< highlight yaml >}}
nodes:
  managers:
    # Number of identical nodes in this node set
    instances: 3
    # Node labels for selection purposes
    labels:    
      label1: value1
      label2: value2 
    # Provider for node provisioning
    provider: 
      # Name of a provider declared in the providers section
      name: ek-aws
      # Provider parameters for this node set (overriding general provider parameters)
      params:
        someAwsParam: someValue
    orchestrator:
      # Orchestrator parameters for this node set (overriding general orchestrator parameters)
      params:
        someSwarmParam: someValue
      # Docker parameters for this node set (overriding general docker parameters)
      docker:
        someDockerParam: someValue
{{< /highlight >}}

{{% notice tip %}}
A special node set named `*` can be declared for parameters common to all nodesets.
{{% /notice %}}

## Stacks

The `stacks` section declares the software stacks that will be deployed in the environment:

{{< highlight yaml >}}
stacks:
  myStack:
    # Component containing the software stack
    component: some-stack
{{< /highlight >}}

The component referenced must contain:
 
* A file recognized by the orchestrator to be a software stack (like a `docker-compose.yaml` file for Docker Swarm),
* And/or a `deploy.yaml` playbook for custom deployment.

## Tasks

The `tasks` section declares the tasks that can be executed in the environment:

{{< highlight yaml >}}
tasks:
  myTask:
    # The component containing the task
    component: myComponent
    # The location of the playbook to execute
    task: task1.yaml
    # The parameters to be passed to the playbook
    params:
      param1: value1
    # If specified the task will be executed automatically according to the specified schedule    
    cron: 0 * * * *
{{< /highlight >}}
