# Compute Fundamentals

## Terminology

### Architecture Types

- Stateless
- Loosely Couples Architectures
- Fault Tolerant
- Availability Zones
- High Availability (5-9s)

### Data

- Data in rest
- Data in transit

### Scaling

- Vertical Scaling
- Horizontal Scaling

### Networking

- Network Perimeter
- VPC (Virtual Private Cloud)
- NACL (Network Access Control List)
- Network Perimeter
- OSI (Open Systems Interconnect) 7 Layers
  1. Physical
  1. Data
  1. Network
  1. Transport
  1. Session
  1. Presentation
  1. Application
- NAT (Network Address Translation)
- Routing Tables
- VPC (Virtual Private Cloud)
- SG (Security Group)
- BGP (Border Gateway Protocol)

### Process Types

- Synchronous
- Asynchronous

### Service Design

- Rest Services
- JSON
- Eventual Consistency

### Services

- SNS (Simple Notification Services)
- WAF (Web Application Firewall)
- Elastic Load Balancing
- SQS (Simple Queuing Services)

## EC2

### AMIs

Amazon machine images

#### Instance Type

- VCPU
- Memory
- Instance Storage
- Network Performance

#### Instance Taxonomy

- Instance Type
  - General Purpose
  - Compute Optimized
  - Memory Optimized
  - Accelerated Computing
  - Storage Optimized

#### Pricing Options

- Reserved Instances
- Scheduled Instances
- Spot Instances
- Capacity Reservations

#### Tenancy

- Shared
- Dedicated
  - Dedicated Instances
  - Dedicated Hosts

#### Storage Options

- Ephermal
- EBS

#### Security

- Security Group (Instance Firewall)
- Public / Private Key Pair

##### AWS Shared Security Model

Currently compliant across many security standards including PCI DSS, ISO and SOC. 3 main models exist each represent where AWS and customer responsibility boundaries are located.

General Model
![Shared Security Responsibility Model](https://d1.awsstatic.com/security-center/Shared_Responsibility_Model_V2.59d1eccec334b366627e9295b304202faf7b899b.jpg)

Infrastructure Services
![Shared Responsibility Model: Infrastructure Services](https://d1o2okarmduwny.cloudfront.net/wp-content/uploads/2015/11/SharedInfra.png)

Container Services
![Shared Responsibility Model: Container Services](https://d1o2okarmduwny.cloudfront.net/wp-content/uploads/2015/11/SharedContainer.png)

Abstract Services
![Shared Responsibility Model: Abstract Services](https://d1o2okarmduwny.cloudfront.net/wp-content/uploads/2015/11/SharedAbstract.png)

Abstract Image of Responsibilities Customer vs. AWS
![Infrastructure, container, abstract model](https://d1o2okarmduwny.cloudfront.net/wp-content/uploads/2015/11/SharedLevels.png)

#### Actions

- Rebooting an instance keeps the virtual machine on the same physical host.

#### Status Checks

- System Status Checks
  - AWS system check of underlying host. Issues normally resolved by a terminate followed by a relaunch.
- Instance Data checks
  - Host VM issues which will require the system administrator involvement

## ECS EC2 Container Service

- AWS Fargate
- Docker Containers

### ECS Cluster Launch

- Fargate Launch
- EC2 Launch

### Limitations

- Region Specific Clusters cannot span multiple regions but can span multiple availability zones.

## ECR - Elastic Container Registry

### Components

#### Registry

The ECR registry is the object that allows you to host and store your docker images in as well as create image repositories.

---

Default registry URL

[<https://[aws_account_id].dkr.ecr.[region].amazonaws.com]>]

---

##### ECR Security

Your AWS  account will have both read and write access by default to any images you create within the registry and any repositories.

Access to your registry and images can be controlled via IAM policies in addition to repository policies.

Before your docker client can access the registry, it needs to be authenticated as an AWS user via an **Authorization Token**.

###### Authorization Token

To begin the authorization process to communicate your docker client with your default registry, you can run the get-login command using the AWS CLI

---

aws ecr get-login --region [region] --no-include-email

---

The command produced above will produce a docker login command that can be issued to produce an authorization token that can be used for 12 hours.

---

docker login -u AWS -p [password] <https://[aws_account_id].dkr.ecr.[region].amazonaws.com>

---

#### Repository

These are objects within your registry that allow you to group together and secure different docker images. IAM policies can be applied to repositories that control access to individual repositories.

#### Repository Policy

The following IAM managed policies help your control access to ECR

- AmazonEC2ContainerRegistryFullAccess
- AmazonEC2ContainerRegistryPowerUser
- AmazonEC2ContainerRegistryReadOnly

Repository policies are resource-based policies

- You need to ensure you add a principal to the policy to determine who has access and what permissions they have.
- For an AWS user to gain access to the registry they will require access to ecr:GetAuthorizationToken API call.

## EKS - Elastic Container Service For Kubernetes

Kubernetes is a container orchestration tool used to manage containers.

EKS is a managed service allowing you to run Kubernetes across your AWS Infrastructure without having to provision and manage the the **control plane**. As an EKS user you are only responsible for provisioning and maintaining worker nodes.

In EKS, AWS is responsible for provisioning, scaling and managing the control plane and they do this by utilizing multiple availability zones for additional resilience.

### Control Plane

Schedules containers across nodes and tracks the state of these containers.

#### EKS Components

- kublet process
- Kubernetes Master

### Worker Nodes

Each node in your Kubernetes cluster is a worker node which has the following components installed

- Built using a specific AMI containing the following software components:
  - Docker Package
  - kubelet
  - AWS IAM Authenticator

Once the worker node is provisioned workers connect to EKS via an endpoint.

### Working with EKS

1. Create an EKS Service Role
   - AmazonEKServicePolicy
   - AmazonEKSClusterPolicy
1. Create an EKS Cluster VPC
   - Use the following [Cloud Formation EKS VPC Template](https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-02-11/amazon-eks-vpc-sample.yaml)
1. Install kubectl executable
1. Install IAM-Authenticator
1. Create EKS Cluster
    1. Configure kubectl for EKS
    1. Create a kubeconfig for EKS
    1. Provision and configure Worker Nodes
        1. Use the following [Cloud Formation Worker Nodes Template](https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-02-11/amazon-eks-nodegroup.yaml) to provision the worker nodes.
        1. Use the following [Configuration Map](https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-02-11/aws-auth-cm.yaml) (Kubernetes Concept - Role Based Access Control) to allow IAM users to interact with Kubernetes and RBAC.

## Elastic Beanstalk

AWS managed service that takes your uploaded code and automatically provisions and deploys the required resources within AWS to make the web application operational. Can include other EC2, auto scaling elastic load balancing and capacity provisioning. Responsibility is passed to AWS to deploy the infrastructure necessary to run your code. The service is itself free to use and will only be charged for the resources that are used.

### Core Components

#### Application

Application ia s logical container consisting of environments, versions and environment configurations.

##### Application Version

A very specific reference to a section of deployable code.  Typically will point to a S3 bucket location where code resides.

##### Environment

An environment refers to an application version that has been deployed on AWS resources, which are configured and provisioned by AWS Elastic Beanstalk. An environment refers to all AWS resources not just EC2 instances.

#### Platform

Platform is a culmination of components in which you build your application upon using Elastic Beanstalk.

### Workflow

#### Select Environment Tier

1. Web server environment
   1. Consists of typically
      1. Route 53
      1. Elastic Load Balancer
      1. Auto Scaling
      1. EC2
      1. Security Groups
1. Worked environment (SQS Queue)
   1. Consists of typically
      1. SQS Queue
      1. IAM Service Role
      1. Auto Scaling
      1. EC2

#### Select Environment Configurations

Collection of parameters and settings that dictate how an environment will its resources provisioned by Elastic Beanstalk and how these resources will behave.

#### Configuration Template

A template that provides the baseline for creating a new, unique environment configuration.

#### Steps

1. Create Application
1. Upload Version
1. Launch Environment
1. Manage Environment

##### Elastic Beanstalk Components

- Software
- Instances
- Capacity
- Load Balancer
- Rolling Updates and Deployments
- Security
- Monitoring
- Managed Updates
- Notifications
- Network
- Database

## Lambda

AWS Lambda is a serverless compute service that allows you to run your application  code without having to manage to manage EC2 instances.

### Cost

You only ever have to for compute power when Lambda is in use via Lambda Functions. AWS Lambda charges compute power per 100ms of use only when your code is running, in addition nto the number of times your code runs.

### Operations

1. Provide executable code
1. Configure Lambda functions to execute upon specific triggers from supported event sources.
1. Specific trigger is initiated, Lambda will run your code using on the required compute power as defined.
1. AWS records the compute time in Milliseconds and the quantity of Lambda functions run to ascertain the cost of the service.

### Components of AWS Lambda

1. Lambda function
1. Event Source (Producer of events) [<https://docs.aws.amazon.com/lambda/latest/dg/lambda-services.html##supported-event-source-s3]>
1. Trigger (Operation of the Event Source) that invokes your Lambda function.
1. Downstream Resources (Resources that are required during the execution of your Lambda Function)
1. Log Streams - Help to identify issues and troubleshoot issues with your Lambda function.

### Creating Lambda Functions

1. Selecting a Blueprint
1. Configure trigger
1. Configure function

## Batch-CF

Used to manage and run batch computing processing cluster.

### Batch-CF Components

1. Jobs - Unit of work
1. Job Definitions
1. Job Queues
1. Job Scheduler
1. Compute Environments
   1. ECS Clusters
   1. Managed Environments
   1. Un-managed Environments
