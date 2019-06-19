---
title: Parameters File
---

Some CLI commands accept the flag `-p, --param` to specify a **yaml** file used to pass parameters which will be interpreted at runtime to substitute variables into the templates.


## Variables definition

The variables to substitute can be define anywhere into the environment or component descriptors using the following syntax:

```yaml
{{ .Vars.key }}
```

Or

```yaml
{{ .Vars.key1.key2.keyX }}
```

Multiple variables are allowed into a single line. 

Example:
```yaml
#descriptor
#....
db_driver: jdbc:{{ .Vars.db.name }}:thin:@{{ .Vars.db.name }}.{{ .Vars.db.project.phase.host }}:1522:{{ .Vars.db.project.phase.service }}
```

{{% notice warning %}}
The variable must start with a `.Vars`
{{% /notice %}}


## Parameter File format

Each variable in the environment descriptor will be replace by the corresponding content provided in the parameters file.

This file can contain any valid **yaml** data.

The flow blocks will be use to match the variable keys:

In this example this parameter content will match the variable key **`{{ .Vars.a.b.c.d }}`**:


```yaml
#parameters
a:
  b:
    c:
      d: my_value
```

Applying this parameters file on this descriptor


```yaml
#descriptor
testhook_post:
    component: distrib
    playbook: post-create.yml
    params:
      my_secret_value: {{ .Vars.a.b.c.d }}
```

Will produce :

```yaml
#descriptor, updated at runtime
testhook_post:
    component: distrib
    playbook: post-create.yml
    params:
      my_secret_value: my_value
```
