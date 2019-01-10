---
title: AWS EC2 provider
---

The AWS EC2 provider enables you to provision Amazon Web-Services EC2 instances as environment nodes.<!--more-->
When using the standard Ekara distribution, this provider is available as `aws` component.

{{% notice note %}}
The component source can be found at https://github.com/ekara-platform/aws-provider.  
{{% /notice %}}

## Example

The following Ekara descriptor provisions 3 small EC2 Ubuntu instances in the "eu-west-1" region and installs 
Docker Swarm on it:

```yaml
name: my-aws-test

ekara:
  distribution:
    repository: ekara-platform/distribution
    ref: 1.0.0

providers:
  aws:
    component: aws

orchestrator:
  component: swarm

nodes:
  main:
    instances: 3
    provider:
      name: aws
      params:
        instance_type: "t2.micro"
        ami_id: "ami-f90a4880"
        region: "eu-west-1"
```

## Parameters

### Instance parameters

All variables which can be overridden as in table below (under **params** tag).

| Name           | Required | Default Value | Description                        |
| -------------- | -------- | ------------- | -----------------------------------|
| `instance_type` |  True |   | Instance type to use for the instance, see http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html |
| `ami_id` |  True |  | AMI ID to use for the instance |
| `vpc_id` |   |  | VPC ID to use for the security groups |
| `vpc_subnet_id` |   |  | vpc_subnet ID to use for the instance |
| `assign_public_ip` |   | True | when provisioning within vpc, assign a public IP address. Boto library must be 2.13.0+ |
| `security_groups` |  |  | To define new security groups, see EC2 parameters for Security Groups |
| `security_groups_ids` |  |  | List of existing security groups ids |
| `instance_tags` |  |  | List of tags to add for the instance |


### Security groups parameters 

| Name           | Required | Default Value | Description                        |
| -------------- | -------- | ------------- | -----------------------------------|
| `name` |  True |   | Security group's name |
| `rules` |  True |  | See https://docs.ansible.com/ansible/latest/modules/ec2_group_module.html |


### Volume parameters

| Name           | Required | Default Value | Description                        |
| -------------- | -------- | ------------- | -----------------------------------|
| `name` |  True |   | Mount point's name |
| `device_name` |  | `params.device_name` | Machine's device name to mount |
| `params` |  True |  | See https://docs.ansible.com/ansible/latest/modules/ec2_vol_module.html |

### Placement group parameters 

| Name           | Required | Default Value | Description                        |
| -------------- | -------- | ------------- | -----------------------------------|
| `name` |  True |   | Placement Group's name |
| `strategy` |  |  | See https://docs.ansible.com/ansible/latest/modules/ec2_placement_group_module.html |