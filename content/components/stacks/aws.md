---
title: Ekara core
---

The Ekara core stack contains the essential components to allows the Ekara platform to work.<!--more-->

The following components are installed:

* Hashicorp Consul which provides distributed servicesfor the platform: persistence, service discovery, coordination, ...
* The Ekara API which allows to manage an environment from outside.
* Other Ekara services like auto-deploy.

{{% notice note %}}
When using the standard Ekara parent, this stack is always deployed on all manager nodes.
{{% /notice %}}

## Example

The following Ekara descriptor provisions 3 small EC2 instances in the "eu-west-1" region:

```
name: myEnvironment

ekara:
  parent:
    repository: ekara-platform/parent
    ref: 1.0.0

nodes:
  app:
    instances: 3
    provider:
      name: ek-aws
      params:
        instances:
          instance_type: "t2.micro"  
```

After creating a minimal infrastructure on [AWS]({{<ref "../providers/aws.md">}}) for details) and deploying a [Docker Swarm cluster]({{<ref "../orchestrators/docker-swarm.md">}}) on it,a `deploy` command with the descriptor above will deploy the core stack on all manager nodes.

## Reference

No parameters are accepted.
