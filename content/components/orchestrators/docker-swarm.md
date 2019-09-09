---
title: Swarm orchestrator
---

The Swarm orchestrator supports the installation and configuration of a Docker Swarm cluster on an Ekara environment infrastructure.<!--more-->

{{% notice note %}}
When using the standard Ekara parent, this orchestrator is enabled and configured with the `ek-swarm` name.
{{% /notice %}}

## Example

The following Ekara descriptor will create 3 small EC2 instances in the "eu-west-1" region and setup Docker Swarm on it:

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

After creating a minimal infrastructure on AWS (see [AWS provider reference]({{<ref "../providers/aws.md">}}) for details), a `deploy` command with the descriptor above will:

* Ensure that the operating system is fully installed with necessary packages on all nodes.
* Ensure that Docker is installed in the latest version on all nodes.
* Ensure a Swarm cluster exists spanning all nodes.
* Ensure that the Swarm managers/workers are properly affected according to node role.

## Reference

### Docker parameters

The following Docker parameters are supported by the  provider (in the `params` element):

| Name           | Required | Default Value | Description                        |
| -------------- | -------- | ------------- | -----------------------------------|
| `daemon` |  False |  - | Everything under this parameter will be written as JSON daemon configuration. |
| `tls` |  False |  - | Options for TLS configuration of the daemon. |
| `remap` |  False | True | If true the containers will be isolated with a user namepsace. |

### Daemon configuration

The elements under `daemon` parameter will be written as the [Docker JSON configuration file](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file). 

The default daemon configuration is:

```
daemon:
  log-driver: json-file
  log-opts:
    max-size: "20m"
    max-file: "3"
  userland-proxy: true
  live-restore: false
  hosts:
    - "unix:///var/run/docker.sock"
    - "tcp://0.0.0.0:2376"    
```

Any value can be overridden.

### TLS configuration groups

The `tls` parameter controls the TLS configuration of the Docker daemon. This is the default configuration with explanations as comments:

```
tls:
  # If true TLS will be required, if false Docker daemon will accept unsecured connections.
  enabled: true
  # The path of the CA certificate nodes
  cacert: "/etc/docker/certs/ca.pem"
  # The path of the certificate nodes
  cert: "/etc/docker/certs/cert.pem"
  # The path of the key nodes
  key: "/etc/docker/certs/key.pem"
```

### Remap

If the `remap` parameter is set to `True` (its default value), containers will be isolated from the host using a [a user namespace](https://docs.docker.com/engine/security/userns-remap/). Enabling this improves security as it prevents UIDs in containers to have any privilege on the host.
