---
title: Command-line interface (CLI)
---

The Ekara command-line interface (CLI) can be used to create new environments or interact with existing ones. 

## General options

The following options are supported by all commands.

Proxy options:

* `--http-proxy <url>` HTTP proxy URL, can also be given by the `http_proxy` environment variable.
* `--https-proxy <url>` HTTPS proxy URL, can also be given by the `https_proxy` environment variable.
* `--no-proxy <list>` Comma-separated list of proxy exclusions, can also be given by the `no_proxy` environment variable.

## 

## Using tags or branches

Some CLI commands take as argument the environment descriptor URI:

Using a raw URI will result into fetching the `master branch` of the repository.

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
In the case of having both a `tag` and a `branch` with the same name, the Ekara installation process will look first for the `tag` and a then for the `branch`.
{{% /notice %}}



---------------------------------------------










