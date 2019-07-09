---
title: Environment descriptor
---

The Ekara descriptor is a YAML document fully describing an environment:

* The **platform** TO BE DONE,
* The **variables** TO BE DONE,
* The **templates** TO BE DONE,
* The **providers** where nodes will be provisioned,
* The **orchestrator** that will manage the cluster of nodes,
* The **nodes** constituting the environment,
* The **stacks** to deploy on the cluster,
* The **tasks** that can be run on nodes. 

{{% notice disclaimer %}}
Into the whole Ekara documentation you will figure that the **Environment descriptor**, finally used to build and deploy an environment, is the result of the aggregation of several yaml descriptors. In order to simplify the understanding of those several decriptors interaction we will refer to this first descriptor as the **main Descriptor**, others descriptors will ne named **component Descriptor**
{{% /notice %}}

## Location

The main descriptor is a YAML file, named by default `ekara.yaml`, placed at the root level of a source repository:

```plain
<root>
    |-- ekara.yaml
```

In opposition  to component descriptor the main descriptor is the only which can have a named different than `ekara.yaml`. In the case of a main descriptor named differently than `ekara.yaml` you would have to explicitly pass its name to Ekara commands which you invoke.

## Contents 

### General properties

The following properties define the basics of the environment:

```yaml
# Name of the environment (mandatory)
name: myEnvironment

# Optional qualifier of the environment (like dev, test, prod, ...)
qualifier: dev

# Optional description of the environment 
descriptor: This is my awesome ekara environment. 
```

These properties cannot be imported through a component descriptor and are only taken into account in the main descriptor (see below).

### Ekara platform

The `ekara` section controls the Ekara platform settings:

```yaml
ekara:
  # The base location used across the environment descriptor
  base: https://a.given.base.com

  # Specify the Ekara platform distribution to use
  distribution:
    repository: ekara-platform/distribution
    ref: v1.2.3
    
  # Declare additional components over ones already present in the distribution
  components:
    myComponent:
      repository: my-organization/my-component
      ref: v1.2.3
```

Ekara is based on the notion of [**component**](#components). A component lives in a source repository and is fetched by Ekara dynamically at startup. 
A component can contain a descriptor, always named `ekara.yaml`, if present this componnent descriptor will be imported automatically and merge into the main descriptor.

#### Base

The base location is used to resolve short repository names for the descriptor only.

Example: 
```yaml
ekara:
  base: https://a.given.base.com

  distribution:
    repository: my-organization/distribution
    
  components:
    myComponent:
      repository: my-organization/my-component
```

Will result in using :

- The distribution :  https://a.given.base.com/my-organization/distribution 
- A component :  https://a.given.base.com/my-organization/my-component

{{% notice tip %}}
The base location is optional, if not provided then Ekara will automatically use `https://github.com` as base.
{{% /notice %}}


#### Distribution

The distribution is a special component that is automatically fetched and imported. 


{{% notice note %}}
The distribution is optional, if not provided then Ekara will automatically import and use its [own distribution](https://github.com/ekara-platform/distribution). If you want to use your own custom distribution you just need to reference it through its repository.
{{% /notice %}}

#### Components

Additional components are only declared in the `components` section but will only be fetched and imported upon effective use in the descriptor. 

A component declaration consists in:

* A `repository` attribute for the source repository holding component code,
* A `ref` attribute for the branch, tag or commit of the component to fetch,
* A  `auth` attribute only required if the source repository is private

If a component contains an `ekara.yaml` descriptor at its root, it will be automatically imported and merged into the main descriptor.


##### Repository

The `repository` attribute allows to specify the location of a component. 

Example: 
```yaml
components:
  aPublicComponent:
    # The repository path can be absolute, or implicitly prefixed with the base location 
    repository: https://github.com/myOrganisation/my-public-rep
```

The source control management repository wherein the component lives can be public or private.
In the case of being private then [**Authentication**](#authentication) should be provided.

{{% notice warning %}}
Currently `git` is the only source control management system supported for repositories.
{{% /notice %}}

##### Ref

The `ref` attibutes identifies the branch, tag or commit to fetch.

Example: 
```yaml
components:
  aPublicComponent:
    repository: https://github.com/myOrganisation/my-public-rep
    # The desired tag, branch or raw reference
    ref: aGivenBranch  
```

When the `ref` is provided for a component Ekara will first try to fetch the matching **tag** and then the **branch**.

{{% notice tip %}}
The `ref` is optional, if not provided then Ekara will automatically fetch the **master**.
{{% /notice %}}

##### Authentication

The `auth` attribute specifies the authentication information required to connect to a private repository.

Example: 
```yaml
ekara:
  components:
    aPublicComponent:
      repository: https://github.com/myOrganisation/my-public-rep
    aPrivateComponent:
      repository: https://github.com/myOrganisation/my-private-rep
      auth:
        method: basic
        user: yourUserName
        password: yourUserPassword
```

{{% notice warning %}}
Currently `basic` is the only authentication method supported.
{{% /notice %}}

{{% notice tip %}}
If you don’t want to have data such as user names and passwords hardcoded, and the visible into a descriptor, you can pass them through template values. Please refer to the [**templating reference**]({{< ref "templating.md" >}}) for more details.
{{% /notice %}}


### Vars

An environment descriptor can defines variables, these variables are intended to be used in templates.

Example: 
```yaml
vars:
  var1_key: descriptor_var1_values 
  var2_key: descriptor_var2_values 
```

#### Vars accumlation
The variables used in templates are the accumulation of all the `vars` defined in all descriptors

The example below shows an **Environment descriptor** and two **Component Descriptors** defining variables. 
![Example image](/img/precedence.png)

Based on this the variables usable into the templates will be: *key1*, *key2*, *key3* and *key4*

The accumulation can also take in account an optional additional [**parameters file**]({{< ref "param.md" >}}) , in our example `CLI parameters`, given at runtime by the user through the Ekara CLI.

Using this file, the variables usable into the templates will be: *key0*, *key1*, *key2*, *key3* and *key4*

#### Vars precedence

During the process of accumulation variables with a lower precedence will be overwritten by others having the same key with a higher precedence.

{{% notice tip %}}
The general rule defining precedence weight is: The farther you are from the **Environment descriptor** the lower is your precedence.
{{% /notice %}}

In our example the `component descriptor` has a lower precedence than the `distribution descriptor` which has itself a lower precedence than the `environment descriptor`, then : 

- The `distribution descriptor` will overwrite *key1*, *key2* and *key3* defined into the `component descriptor` 
- The `environment descriptor` will overwrite *key1* and *key3* defined into the `distribution descriptor` 
- A special behaviour of the additional parameters file `CLI parameters` is that it has the highest precedence. This means that it will overwrite *key1* defined into the `environment descriptor` 

In brief in our example the overall result the `vars` accumulation avaible into the templates will then be:

```go
  key0 = value0.from.cli
  key1 = value1.from.cli
  key2 = val2_distribution
  key3 = val3_descriptor
  key4 = val4_comp1
```
 

### Templates

An environment descriptor can defines templates. 

A template is a file who lives in the same source repository than the one holding the descriptor, it can be of any type. A template is identified by its relative path within the repository.

Example of component with a `docker-compose` file which should be templated before being deployed: 

This is the `docker-compose` where the templating must be applied:
```yaml
version: '3.7'

services:
  vizualizer:
    image: dockersamples/visualizer
    ports:
    # The following line will be updated by the templating mechanism
    - "{{ .Vars.app.visualizer.port }}:8080"
```

This is how the component will specifies, into its own escriptor, that one of its file must be templated:
```yaml
templates:
  - "docker-compose.yml"
```

{{% notice warning %}}
For performance reasons the templating mechanism will be applied only to the listed files, then if several files should be templated they all must be specified.
{{% /notice %}}

{{% notice tip %}}
Template relative paths can be specified using [glob patterns](https://github.com/gobwas/glob). 
{{% /notice %}}

Please refer to the [**templating reference**]({{< ref "templating.md" >}}) for more details about the templating mechanism.

### Providers

The `providers` section declares the cloud providers used by the environment:

```yaml
providers:
  aws:    
    # Name of the provider component 
    component: ek-aws
    # Provider default parameters
    params: 
      region: eu-west-1
```

### Orchestrator

The `orchestrator` section declares the Docker orchestrator that will be used: 

```yaml
orchestrator:
  # Name of the orchestrator component
  component: ek-swarm
  # Orchestrator default parameters 
  params:
    someSwarmParam: someValue
  # Docker default parameters
  docker:
    someDockerParam: someValue
```

### Node sets

The `nodes` section declares the resources on which the environment will run, grouped by node sets:

```yaml
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
      name: aws
      # Provider parameters for this node set (overriding default provider parameters)
      params:
        region: 'eu-west-2'
    orchestrator:
      # Orchestrator parameters for this node set (overriding default orchestrator parameters)
      params:
        someSwarmParam: otherValue
      # Docker parameters for this node set (overriding default docker parameters)
      docker:
        someDockerParam: otherValue
```

TODO Add the special node set `*`

### Software stacks

The `stacks` section declares the software stacks that will be deployed in the environment:

A software stack can be deployed using a `docker-compose.yml` or a playbook name `install.yml`


```yaml
stacks:
  monitoring:
    # Component containing docker-compose or the playbook
    component: prometheus-stack
```

When a software stack is located into the same repository than the descriptor where it's used then it can ommit the component into its definition

```yaml
stacks:
  myStack:
```
 
Or if you prefer a clearer way to specify that the stack shares the descriptor location you can also use :

```yaml
stacks:
  myStack:
    # _ means that the component holding the stack is the one exposing the descriptor
    component: _
```

The component referenced must contain:
 
* A file recognized by the orchestrator to be a software stack (like a `docker-compose.yml` file for Docker Swarm),
* And/or an `install.yml` playbook for custom deployment.

{{% notice note %}}
If both a `docker-compose.yml` file and an `install.yml` playbook are present into the component then the `install.yml` playbook will be used and the `docker-compose.yml` will be ignored.
{{% /notice %}}


### Tasks

The `tasks` section declares the tasks that can be executed in the environment:

```yaml
tasks:
  task1:
    # The component containing the task
    component: myComponent
    # The location of the playbook to execute
    task: task1.yaml
    # The parameters to be passed to the playbook
    params:
      param1: value1
    # If specified the task will be executed automatically at the specified schedule    
    cron: 0 * * * *
```

### Hooks

To be done...

