---
title: Command-line interface (CLI)
---

The Ekara command-line interface (CLI) can be used to create new environments or interact with existing ones. 

## Commands

The CLI tool accepts various commands. Learn about them [here]({{<ref "commands.md">}}).

## Common options

The following options are supported by all commands.

Proxy options:

* `--http-proxy <url>`: HTTP proxy URL (defaults to the `http_proxy` environment variable if defined).
* `--https-proxy <url>`: HTTPS proxy URL (defaults to the `https_proxy` environment variable if defined).
* `--no-proxy <list>`: Comma-separated list of proxy exclusions (defaults to the `no_proxy` environment variable if defined).

## Docker options

Commands that interact directly with a Docker daemon (like `deploy`) accept the following options:

* `--docker-cert <path>`: Location of the docker certificates (defaults to the `DOCKER_CERT_PATH` environment variable if defined). **!! TODO !!**
* `--docker-host <url>`: URL of the docker host (defaults to the `DOCKER_HOST` environment variable if defined). **!! TODO !!**
* `--installer-tag <tag>`: allow to specify the tag of the installer Docker image to be used (defaults to `latest` otherwise). **!! TODO !!**

## Repository URL (with tags and branches)

Some commands take a repository URL as argument. This section details the syntax of this argument. The most basic form is a complete
HTTP(S) repository URL:

```
# Using the master branch
$ ekara deploy http://github.com/ekara-platform/demo
```

To use a given `tag` or a `branch` of your repository you need to postfix the raw repository URI with `@tag_name` or `@branch_name`.


```
# Using a given tag of your repository
$ ekara deploy http://github.com/ekara-platform/demo@tag_name

# Using a given branch of your repository
$ ekara deploy http://github.com/ekara-platform/demo@branch_name
```

{{% notice note %}}
If you have the same name between a `tag` and a `branch`, the Ekara installation process will look first for the `tag` and a then for the `branch`.
{{% /notice %}}
