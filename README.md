# Setting up a vpc on ansible

In this repo, the host will be localhost because most of the EC2 cloud modules run on a managed host which talks to the EC2 API to make changes.

It will have a vars/info.yml file for the variables that set credentials needed to access EC2

## Required variables

You will need an info.yml file in a folder vars which will hold the variables

```YAML
aws_id: <aws access key>
aws_key: <aws secret key>
aws_region: <youre region>
ssh_keyname: <your ssh keyname>
remote_user: <hostname>
```

## VPC

- Load the `aws_id`, `aws_key` and `aws_region` from vars/info.yml
- `name` and `cidr_block` are required parameters for the VPC
- tags can be used
- if `tenancy` is `default`, new instances in the VPC will run on shared hardware by default. if you use `dedicated`, new instances will run on single-tenant hardware by default.
- The results of the task are stored in the variable `ansibleVPC`. This includes the resource ID of the VPC created (in `ansibleVPC['vpc']['id']`).

## Internet gateway

- Attch an internet gateway to the newly created VPC
- The vpc_id parameter is required which will be the VPC created and this is done by using it as a a variable
- `state` controls wether the IGW should be present or absent from the VPC.
- Have the save igw id as a variable so that it can be reused.

## Subnet

- Use `ec2_vpc_subnet` module to add a subnet to an existing VPC
- `vpc_id` must be specified
- Set the state to present to specify that the subnet should exist.
- Specify the CIDR block
- use `map_public` to assign instances a public IP address by default.

## Route table

- use `ec2_vpc_route_table` module to create a routing table. It can also manage routes in the table and associate them with an IGW's ID.
- use `subnets` for all the subnets that will use this route table
- routes will be a list of routes

## Security group

use `ec2_group`

the paramaters will be:
- `name` - the name of the security group
- `region` - the region of the group
- `rules` - that define firewall inbound roules to enforce.

# Procisioning an EC2 instance

## Find and existing AMI

- Find an AMI that can build an amazon EC2
- IDS of an AMI can vary from region to region
- Use the `ec2_ami_info` module to find the AMI
- `filters` dictionary filters the list of AMIs returned by the module.
- You can store the value of the AMI id as a variable