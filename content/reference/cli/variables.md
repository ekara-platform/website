---
title: External variables
---

CLI commands working with a descriptor accept the `-p, --param`, which specifies the YAML to use as a source for external variables. Those variables can then be used in [templating]({{<ref "../environment/templating.md" >}}). Templating is always applied to descriptors and can be optionally applied to arbitrary files in components.

## Variables

Variables can come from:

* An external source like the file described here,
* A `vars` section in that can be specified in each descriptor file.

## File format

The only requirement is that the file must be a valid YAML file. Data can be structured as required.

Consider this trivial example:

```
app:
  visualizer:
    port: 8080
```

And this descriptor:

```
name: myEnv
providers:
  ek-aws:
    params:
      securityGroups:
        app:
          rules:
            - proto: tcp
              ports:
                - {{ .Vars.app.visualizer.port }} 
              cidr_ip: 0.0.0.0/0
              rule_desc: allow all on port {{ .Vars.app.visualizer.port }} for the swarm visualizer 

# ...              
```

The following effective descriptor will be produced:

Will produce :

```
name: myEnv
providers:
  ek-aws:
    params:
      securityGroups:
        app:
          rules:
            - proto: tcp
              ports:
                - 8080
              cidr_ip: 0.0.0.0/0
              rule_desc: allow all on port 8080 for the swarm visualizer 

# ... 
```
