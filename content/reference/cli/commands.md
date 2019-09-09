---
title: Commands
---

List of available commands:

* [**check**](#check): validates an existing environment descriptor. 
* [**deploy**](#deploy) deploys a new environment (validate descriptor, create infrastructure, install OS + orchestrator and deploy stacks).
* [**dump**](#dump): dumps the effective environment descriptor (fully aggregated and templated).
* [**help**](#help): displays a list of the available commands or display help on a particular command.
* [**version**](#version): displays the Ekara version.

{{% notice tip %}}
You can use the `-h or --help` option on any command to get further help.  
{{% /notice %}}

## Check

This command fetch and validates an environment model:

```
$ ekara check <repository-url> [options]
```

Arguments:

* `repository-url`: the URL of the repository where the main environment descriptor is located.

Options:

* `-u --user <user-name>`: the user to use for authenticated repositories.
* `-p --password <user-password>`: the password to use for authenticated repositories.
* `-v --vars <vars-file>`: location of the external variables file that will be used for templating.

{{% notice note %}}
This is a command requiring a Docker daemon. It also accepts [Docker options]({{<ref "_index.md#docker-options">}}).
{{% /notice %}}

## Deploy

This command deploys a new environment or updates it if it already exists. It goes through the following steps:

1. Validation of the environment descriptor (same as the [check](#check) command),
2. Creation of the infrastructure,
3. Installation and configuration of the OS + orchestrator,
4. Deploy the software stacks.

```
$ ekara deploy <repository-url> [options]
```

Arguments:

* `repository-url`: the URL of the repository where the main environment descriptor is located.

Main options:

* `-u --user <user-name>`: the user to use for authenticated repositories.
* `-p --password <user-password>`: the password to use for authenticated repositories.
* `-v --vars <vars-file>`: location of the external variables file that will be used for templating.
* `-l --logs`: turn on the installer logs on the console (otherwise logs are only written in a file named `installer.log`)
* `--public-key <path>` Path to the public key to be used for remote node access (if none given a key will be generated and written in output directory).
* `--private-key <path>` Path to the private key key to be used for remote node access (if none given a key will be generated and written in output directory).

Example with parameter file and logs:

```
$ ekara deploy http://github.com/ekara-platform/demo -v vars.yaml -l
```

Two subcommands are available, taking the same arguments and options:

* `create-only`: this will stop the process at the end of step 2 (creation of the infrastructure).
* `install-only`: this will stop the process at the end of step 3 (installation of the OS + orchestrator).

## Dump

This command dumps the effective environment descriptor (fully aggregated and templated):

```
$ ekara dump <repository-url> [flags]
```

Arguments:

* `repository-url`: the URL of the repository where the main environment descriptor is located.

Main options:

* `-u --user <user-name>`: the user to use for authenticated repositories.
* `-p --password <user-password>`: the password to use for authenticated repositories.
* `-v --vars <vars-file>`: location of the external variables file that will be used for templating.

{{% notice note %}}
This is a command requiring a Docker daemon. It also accepts [Docker options]({{<ref "_index.md#docker-options">}}).
{{% /notice %}}

## Help

This command displays a list of the available commands:

```
$ ekara help [command]
``` 

Arguments:

* `command`: if specified, displays help on this particular command.

## Version

This command displays the versions of the command-line executable:

```
$ ekara version
``` 

{{% notice note %}}
The command-line executable always use the `latest` tag of the installer Docker image. You can force a tag with the `--installer-tag` [option]({{<ref "_index.md#docker-options">}}).
{{% /notice %}}