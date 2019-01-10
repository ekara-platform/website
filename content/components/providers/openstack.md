---
title: OpenStack provider
---

The OpenStack provider enables you to provision OpenStack stacks as environment nodes.<!--more-->
When using the standard Ekara distribution, this provider is available as `openstack` component.

{{% notice note %}}
The component source can be found at https://github.com/ekara-platform/openstack-provider.  
{{% /notice %}}

## Example

The following Ekara descriptor provisions 3 stacks and installs Docker Swarm on it:

```yaml
name: my-openstack-test

ekara:
  distribution:
    repository: ekara-platform/distribution
    ref: 1.0.0

providers:
  openstack:
    component: openstack

orchestrator:
  component: swarm

nodes:
  main:
    instances: 3
    provider:
      name: openstack
      params:
        config:
          auth:
            auth_url: 'https://openstack.mycompany.com:5000/v3/'
            username: {{ .user_name }}
            password: {{ .user_password }}
            user_domain_name: ldap
            project_domain_name: Default
            project_name: MyProject
          identity_api_version: 3
```

## Parameters

To be done...
