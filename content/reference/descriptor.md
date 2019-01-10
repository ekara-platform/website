---
title: Environment descriptor
---

The Ekara descriptor is a YAML document fully describing an environment:

* The **nodes** constituting the environment,
* The **providers** where nodes will be provisioned,
* The **orchestrator** that will manage the cluster of nodes,
* The **stacks** to deploy on the cluster,
* The **tasks** that can be run. 

## Location

The default descriptor is a YAML file, named `ekara.yaml`, placed at the root level of a source repository:

```plain
<root>
    |-- ekara.yaml
```

Additional descriptor files can be created but they must be imported explicitly from the default one. 

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

These properties cannot be imported and are only taken into account in the main descriptor (see below).

### Imports

The `imports` section is an ordered list of other files to import: 

```yaml
imports:
  - otherDescriptor1.yaml
  - otherDescriptor2.yaml
```

Each import has priority over the descriptors it imports itself. The first descriptor of the import chain has the highest priority.

### Ekara platform

The `ekara` section controls the Ekara platform settings:

```yaml
ekara:
  # The base location used to resolve short repository names for the descriptor only
  base: https://github.com

  # Specify the Ekara platform distribution to use
  distribution:
    repository: ekara-platform/distribution
    ref: v1.2.3
    
  # Declare additional components over ones already present in the distribution
  components:
    myComponent:
      repository: my-organization/my-component
      ref: v1.2.3
      imports:
        - additionalImport.yaml
```

Ekara is based on the notion of **component**. A component lives in a source repository and is fetched by Ekara dynamically
at startup. A component can contain a default descriptor that will be imported automatically if present but also additional
descriptors that can be explicitly imported.

The distribution is a special component that is automatically fetched and imported. Additional components are only declared
in the `components` section but will only be fetched and imported upon effective use in the descriptor. 

A component declaration consists in:

* A `repository` attribute for the source repository holding component code,
* A `ref` attribute for the branch, tag or commit of the component to fetch,
* An `imports` attribute for the list of descriptors inside the component to explicitly import. If a component contains a
default descriptor (named `ekara.yaml`) at its root, it is automatically imported.

### Providers

The `providers` section declares the cloud providers used by the environment:

```yaml
providers:
  aws:    
    # Name of the provider component 
    component: aws-provider
    # Provider default parameters
    params: 
      region: eu-west-1
```

### Orchestrator

The `orchestrator` section declares the Docker orchestrator that will be used: 

```yaml
orchestrator:
  # Name of the orchestrator component
  component: swarm-orchestrator
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

### Software stacks

The `stacks` section declares the software stacks that will be deployed in the environment:

```yaml
stacks:
  monitoring:
    # Component containing the software stack
    component: prometheus-stack
```

The component referenced must contain:
 
* A file recognized by the orchestrator to be a software stack (like a `docker-compose.yaml` file for Docker Swarm),
* And/or an `install.yaml` playbook for custom deployment.

If the component containing the environment main descriptor is also a valid software stack it will be automatically deployed.

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