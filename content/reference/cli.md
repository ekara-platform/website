---
title: Command-line interface (CLI)
---

The Ekara command-line interface (CLI) can be used to create new environments or interact with existing ones. 

## Commands

* **create** Create a new environment (check descriptor, provision nodes, install orchestrator and deploy stacks).
* **help** Display a list of the available commands. 
* **version** Display the Ekara version.

{{% notice tip %}}
You can use the `--help` option on any command to get further help.  
{{% /notice %}}

## Create

This command creates a new environment based on the specified environment descriptor:

```
ekara create <repositoryUrl> [opts]
```

{{% notice note %}}
A running and properly configured Docker daemon is required to run this command. Docker environment will be picked up
if any. You can also specify docker options directly on the command-line.
{{% /notice %}}

Arguments:

* `repositoryUrl`: the URL of the repository where the environment descriptor is located.

Main options:

* `--params <paramfile>` Location of the parameters file that will be substitutable in the descriptor.
* `--logs [logfile]` Outputs the installer logs into the `installer.log` file. The optional logfile parameter controls the logfile name. 
* `--descriptor <name>` Name of the environment descriptor file to use (defaults to `ekara.yaml`).
* `--public-ssh <path>` Path to the public SSH key to be used for remote node access (if none given a key will be generated).
* `--private-ssh <path>` Path to the private SSH key to be used for remote node access (if none given a key will be generated).

Docker options: 

* `--cert <path>` Location of the docker certificates (optional, can be substituted by an environment variable) 
* `--host <url>` URL of the docker host(optional, can be substituted by an environment variable)

Proxy options:

* `--http-proxy <url>` HTTP proxy URL, can also be given by the `http_proxy` environment variable.
* `--https-proxy <url>` HTTPS proxy URL, can also be given by the `https_proxy` environment variable.
* `--no-proxy <list>` Comma-separated list of proxy exclusions, can also be given by the `no_proxy` environment variable.

Basic example:

```
$ ekara create http://github.com/ekara-platform/demo
```

Parameter files Custom Docker daemon example with logs:

```
$ ekara create http://github.com/ekara-platform/demo --cert ./cert_location --host tcp://192.168.99.100:2376 --output
```

## Help

This command displays a list of the available commands or the details of a specific command:

```
ekara help [command]
``` 

Arguments:

* `command`: if specified, displays the details of this particular command.

## Version

This command displays the versions of the command-line executable, the installer, the engine and the model:

```
ekara version
``` 

