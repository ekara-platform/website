---
title: AWS provider
---

The AWS provider supports the creation and configuraton of Amazon Web-Services EC2 infrastructure to deploy Ekara environments.<!--more-->

It can create and configure the following EC2 items:

* Key pairs,
* Security groups,
* Placement groups,
* Instances,
* Elastic Block Storage (EBS) volumes.

In addition it will also attach, format and mount EBS volumes on instances.

{{% notice note %}}
When using the standard Ekara parent, this provider is configured with the `ek-aws` name.
{{% /notice %}}

## Example

The following Ekara descriptor provisions 3 small EC2 instances in the "eu-west-1" region:

```
name: myEnvironment

ekara:
  parent:
    repository: ekara-platform/parent
    ref: 1.0.0

nodes:
  app:
    instances: 3
    labels:
      label1: value1
    provider:
      name: ek-aws
      params:
        securityGroups:
          app:
            rules:
              - proto: tcp
                ports:
                  - 8080
                cidr_ip: 0.0.0.0/0
                rule_desc: allow all on port 8080 for the application
        instances:
          instance_type: "t2.micro"
        volumes:
          docker:
            ebs:
              volume_size: 10
              delete_on_termination: true
              device_name: /dev/xvdf
            fs:
              mount_path: /var/lib/docker
```

On the infrastructure side, a `deploy` command with the descriptor above will:

* Ensure that one security group named `app` for the application exists, opening port 8080 to all IP addresses for TCP.
* Ensure that 3 instances of type `t2.micro` exist, based on the default AMI (Ubuntu minimal 18.04 LTS).
* Ensure that a 10 Go EBS volume named `docker` exist, formatted with the default filesystem (ext4) and mounted in `/var/lib/docker`.
* Ensure that the instances and the volume are tagged with `ek_label_label1=value1`.

Beyond the items described above, inheriting the Ekara parent will also add some security groups for platform operation. 

## Reference

The following parameters are supported by the AWS provider (in the `params` element):

| Name           | Required | Default Value | Description                        |
| -------------- | -------- | ------------- | -----------------------------------|
| `placementGroup` |  False |  - | Parameters that will be passed to the Ansible `ec2_placement_group` module. |
| `securityGroups` |  False |  - | A map defining the security groups to create. Key: placement group name. Value: parameters passed to the Ansible `ec2_group` module. |
| `instances` |  True | - | Parameters that will be passed to the Ansible `ec2` module to create instance(s). |
| `volumes` | False  | - | A map defining the volume(s) to create and attach. Key: volume name. Value: EBS parameters and filesystem parameters. |

### Placement group

The `placementGroup` parameter correspond to the parameters of the [Ansible ec2_placement_group module](https://docs.ansible.com/ansible/latest/modules/ec2_placement_group_module.html). 

The following parameters are set by default and **can** be overridden:

```
name: "<environment_name>_<environment_qualifier>_pg"
```

The following parameters are forcibly set and **cannot** be overriden:

```
region: "<provider_region>"
state: "present"
```

### Security groups

The `securityGroups` parameter is a map in which:

* The key is the security group name
* The value correspond to the parameters of the [Ansible ec2_group module](https://docs.ansible.com/ansible/latest/modules/ec2_group_module.html).

The following parameters are set by default and **can** be overridden:

```
name: "<environment_name>_<environment_qualifier>_<security_group_name>"
description: "Security group <security_group_name> for <environment_name>_<environment_qualifier>"
```

The following parameters are forcibly set and **cannot** be overriden:

```
region: "<provider_region>"
state: "present"
```

### Instances

The `instances` parameter correspond to the parameters of the [Ansible ec2 module](https://docs.ansible.com/ansible/latest/modules/ec2_module.html). 

The following parameters are set by default and **can** be overridden:

```
instance_tags:
    Name: "<environment_name> (<environment_qualifier>) - <nodeset_name>"
```

The following parameters are forcibly set and **cannot** be overriden:

```
key_name: "ek_<environment_name>_<environment_qualifier>",
region: "<provider_region>",
wait: true,
exact_count: <nodeset_instance_count>,
count_tag:
  ek_nodeset_id: "<environment_name>_<environment_qualifier>_<nodeset_name>"
instance_tags:
  ek_env_id: "<environment_name>_<environment_qualifier>",
  ek_nodeset_id: "<environment_name>_<environment_qualifier>_<nodeset_name>",
  ek_nodeset_name: "<nodeset_name>"
```

If necessary, the name of the placement group is automatically injected into the instance settings as:

```
placement_group: "<placement_group_name>"
```

If necessary, the identifiers of the security group(s) are injected as:

```
group_id: [ "<sg1_id>", "<sg2_id>", ... ]
```

If necessary, the node set labels are injected as:

```
instance_tags:
  ek_label_<label_name>: "<label_value>"
  ...
```

### Volumes

Elements under the `volumes` parameter correspond to a map in which:

* The key is the volume name
* The value is a map containing the following elements:
  * `ebs` which correspond to the parameters of the [Ansible ec2_vol module](https://docs.ansible.com/ansible/latest/modules/ec2_vol_module.html).
  * `fs` which correspond to the parameters of the [ekara-platform/ansible-role-filesystem role](https://github.com/ekara-platform/ansible-role-filesystem).

The following parameters are set by default and **can** be overridden:

```
ebs:
  volume_type: "gp2"
```

The following parameters are forcibly set and **cannot** be overriden:

```
ebs:
  state: "present"
```
