# unqork tech assignment

For reference, the assignment is at the bottom of this readme

## In this repo
- Ansible config that provisions infrastructure in AWS in order to stand up an EKS cluster and node group
- Ansible template definition of kubernetes pod / service for tomcat server

In total, this repo handles provisioning the following: (`playbooks/core_infa/provision_eks_infra.yml`)
- New VPC in us-east-1 region of AWS
- 2 subnets in different availability zones for that VPC
- Internet gateway, routing table for subnets
- iam role / security group for eks cluster
- eks cluster
- eks node group 

After all provisioning is complete, `k8s_templates/tomcat.yml` defines a kubernetes tomcat app, which is deployed via `playbooks/k8s/deploy_tomcat.yml`, which creates:
- pod running docker tomcat:9.0 container
- k8s lb service to serve traffic to the pod

Other steps not represented in this repo:
- setup jumpbox in ec2 to run ansible from
- configure ansible to manange aws infra
- attach kubectl to eks instance once it's running (can't use `deploy_tomcat.yml` without this step)

In addition, for extra credit:
- stand up ec2 instance in the same vpc for Rancher
- install basic containerized version of Rancher onto ec2 instance
- attach existing EKS cluster to rancher instace

## Context

A lot of the decisions that I made during the course of working on this assignment come down to the implied time constraint. 
- Most of the "setup" steps like auth and configuring kubectl are not automated
- The Rancher deployment used is just the containerized "try it out" version of Rancher; in the real world I'd probably encourage a greenfield deployment of Rancher via helm onto it's own dedicated Kubernetes cluster, as suggested in their docs.
- There are a TON of opportunities to make the code used more abstract and to conform to ansible best practices for project structure. -- in fact, I basically completely forgo the standard ansible best practice project structure, becuase a lot of it just doesn't make sense when the `only` thing you're doing is provisioning infra. 

That being said, I'm curious about the decision to have the candidate (me) use Ansible to provision eks and configure kubernetes resources, when provisioning infrastructure is, in my opinion, far from the best tool for the job. So much of Ansible's functionality is based around the concept of existing inventory, which is where the majority of Ansible "best practices" arose. dynamic inventory management, ansible roles, and the ability to leverage tags on existing infrastructure to determine roles and variable values via group vars makes Ansible ideal for configuration of existing infrastructure, but it's pretty terrible at creating that infracture. 


It's inherently difficult to make ansible playbooks that manage infrastructure idempotent, and none of the functionality built into the language that enables abstraction (roles, group vars, environments) work for infrastructure that doesn't exist yet. 

While resources exist in Ansible to manage EKS, there's really no way at all to manage eks node clusters via ansible, which is why I decided to leverage cloudFormation for just that part of this exercise. 

In the real world, I'd strongly push for Terraform (or similar) to manage the state of infrastructure and handoff to Ansible for management of configuration of that infrastructure, because that is where Ansible shines. 


## assignment
###Summary:  
This assignment should only take 1-2 hours at most. The assignment is meant to show your ability to learn Ansible, and to demonstrate your knowledge of Kubernetes.

###Task:
Create an EKS cluster with Ansible and use Ansible to deploy a pod running any docker container you choose. If you want to make assumptions such as Security Groups or Role Permissions that is fine, just state any assumptions you make.

###Extra credit: 
Stand up rancher, attach the cluster to it, and set up a role/rbac. Attach screenshots of the cluster and RBAC configuration. Not required and goes beyond the 1-2 hours. It shows you in the direction you are going.Again, please don't hesitate to reach out to Brandon, Jason, or myself if you have any questions!  


