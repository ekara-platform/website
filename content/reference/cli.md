---
title: Command-line interface (CLI)
---

The Ekara command-line interface (CLI) can be used to create new environments or interact with existing ones. 

## Commands

* **check** Validate an existing environment descriptor. 
* **deploy** Deploy a new environment (validate descriptor, provision nodes, install orchestrator and deploy stacks).
* **dump** Dump an existing environment descriptor. 
* **help** Display a list of the available commands. 
* **version** Display the Ekara version.

{{% notice tip %}}
You can use the `-h or --help` option on any command to get further help.  
{{% /notice %}}

## Check

This command validates an environment descriptor:

```
ekara check <descriptor-repository-url> [flags]
```

{{% notice note %}}
A running and properly configured Docker daemon is required to run this command. Docker environment will be picked up
if any. You can also specify docker options directly on the command-line.
{{% /notice %}}

Arguments:

* `descriptor-repository-url`: the URL of the repository where the environment descriptor is located.

Main options:

* `-d --descriptor <descriptor-file-name>` The name of the environment descriptor, if missing we will look for the defaulted one: `ekara.yaml`.
* `-p --param <param-file>` Location of the parameters file that will be substitutable in the descriptor.

Docker options: 

* `-c --cert <path>` Location of the docker certificates (optional, can be substituted by an environment variable) 
* `-H --host <url>` URL of the docker host(optional, can be substituted by an environment variable)


## Deploy

This command deploys a new environment based on the specified environment descriptor:

The deployment will:

* Validate the environment descriptor.
* Provision the nodes.
* Install the orchestrator. 
* Deploy the stacks.

```
ekara deploy <descriptor-repository-url> [flags]
```

{{% notice note %}}
A running and properly configured Docker daemon is required to run this command. Docker environment will be picked up
if any. You can also specify docker options directly on the command-line.
{{% /notice %}}

Arguments:

* `descriptor-repository-url`: the URL of the repository where the environment descriptor is located.

Main options:

* `-d --descriptor <descriptor-file-name>` The name of the environment descriptor, if missing we will look for the defaulted `ekara.yaml`.
* `-p --param <param-file>` Location of the parameters file that will be substitutable in the descriptor.
* `-l --logs [installer-log]` Allows to turn on the installer logs. By default the logs will be written in a file namned `installer.log`, the file name can be controlled using `-L --log-file`. 
* `-L --log-file [installer-log_file]` The log file name.
* `--public-ssh <path>` Path to the public SSH key to be used for remote node access (if none given a key will be generated).
* `--private-ssh <path>` Path to the private SSH key to be used for remote node access (if none given a key will be generated).

Docker options: 

* `-c --cert <path>` Location of the docker certificates (optional, can be substituted by the `DOCKER_CERT_PATH` environment variable) 
* `-H --host <url>` URL of the docker host(optional, can be substituted by the `DOCKER_HOST` environment variable)

Proxy options:

* `--http-proxy <url>` HTTP proxy URL, can also be given by the `http_proxy` environment variable.
* `--https-proxy <url>` HTTPS proxy URL, can also be given by the `https_proxy` environment variable.
* `--no-proxy <list>` Comma-separated list of proxy exclusions, can also be given by the `no_proxy` environment variable.

Basic example:

```
$ ekara deploy http://github.com/ekara-platform/demo
```

Parameter files Custom Docker daemon example with logs:

```
$ ekara deploy http://github.com/ekara-platform/demo --cert ./cert_location --host tcp://192.168.99.100:2376 --logs
```


******

## Dump

This command dumps the environment descriptor:

The dumped descriptor will be resolved. This means that all imported partial descriptors, and all inherited content, comming from a component or from a distribution, will be merge into a final descritor before being dumped.  

```
ekara dump <descriptor-repository-url> [flags]
```

{{% notice note %}}
A running and properly configured Docker daemon is required to run this command. Docker environment will be picked up
if any. You can also specify docker options directly on the command-line.
{{% /notice %}}

Arguments:

* `descriptor-repository-url`: the URL of the repository where the environment descriptor is located.

Main options:

* `-d --descriptor <descriptor-file-name>` The name of the environment descriptor, if missing we will look for the defaulted `ekara.yaml`.
* `-p --param <param-file>` Location of the parameters file that will be substitutable in the descriptor.

Docker options: 

* `-c --cert <path>` Location of the docker certificates (optional, can be substituted by an environment variable) 
* `-H --host <url>` URL of the docker host(optional, can be substituted by an environment variable)

## Help

This command displays a list of the available commands: or the details of a specific command:

```
ekara help
``` 

## Version


This command displays the versions of the command-line executable and the installer:

```
ekara version
``` 

---------------------------------------------










