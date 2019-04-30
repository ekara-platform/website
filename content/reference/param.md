---
title: Parameters File
---

Some CLI commands accept the flag `-p, --param` to specify a **yaml** file used to pass parameters which will be interpreted at runtime to substitute variables into the environment descriptor.

The parameters file is used associated to a templating mechanism to modify the environment descriptor at runtime.

## Variables definition

The variables to substitute can be define anywhere into the environment descriptor using the following syntax:

```yaml
{{ .key }}
```

Or

```yaml
{{ .key1.key2.keyX }}
```

Multiple variables are allowed into a single line. 

Example:
```yaml
#descriptor
#....
db_driver: jdbc:{{ .db.name }}:thin:@{{ .db.name }}.{{ .db.project.phase.host }}:1522:{{ .db.project.phase.service }}
```

{{% notice warning %}}
The variable must start with a `.`
{{% /notice %}}


## Parameter File format

Each variable in the environment descriptor will be replace by the corresponding content provided in the parameters file.

This file can contain any valid **yaml** data.

The flow blocks will be use to match the variable keys:

In this example this parameter content will match the variable key **`{{ .a.b.c.d }}`**:


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
      my_secret_value: {{ .a.b.c.d }}
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

## Restriction


### Spaces

UTF8
Spaces
No complex structure allowed

