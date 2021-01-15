# AWS Certified Solutions Architect Notes

## S3 - Simple Storage Service
  - Object-based storage. Store virtually unlimited amounts of data without worrying about the underlying storage infrastructure.
  - S3 replicates data across at least 3 AZs to ensure 99.99% availability and 11 9's of durability.
  - Objects contain your data (they're like files).
  - Objects can be size anywhere from 0 bytes up to 5 terabytes.
  - Buckets contain objects. Buckets can also contain folders which can in turn contain objects.
  - Buckets are unique across all AWS accounts, like a domain name.
  - When you upload a file to S3 successfully you'll recieve an HTTP 200 code.
  - Versioning 
    - Pervious versions of an object are kept when you upload an object with the same name so that you can access the older version if desired.
    - Once you delete an object the previous version is restored. 
    - It cannot be turned off once turned on, but it can be suspended on a bucket (new uploads will replace previous versions).
  - Lifecycle Management
    - Set up rules for when to send Bucket items to Archive(Glacier)/delete them.
  - Presigned URLs
    - Using the SDK or CLI to create URL links that act as temporary passwords to an S3 Bucket or Object.
    - Called with "aws s3 presign"
    - Default timeout is 1 hour, but can be specified with the "--expires-in" option.
    - Useful for giving website users a short-lived link to download a file.
  - MFA delete
    - Requires versioning to be on.
    - Requires an MFA code to delete objects from the bucket.
    - Only the root user can delete objects from the bucket.
  - All new buckets are private by default.
  - Logging can be turned on to track operations done on a bucket.
  - Access control is configured using Bucket Policies and Access Control Lists (ACLs)
    - Bucket Policies are JSON documents that describe how buckets should behave. S3 offers a policy builder in the sub-menu.
    - ACLs are the legacy method (although not deprecated) where you grant access to objects and buckets with simple actions.
  - Security in Transit: Uploading files is done over SSL for security encryption while travling to the server.
  - SSE stands for server-side encryption. S3 has 3 options for SSE:
    - SSE-AES:  S3 handles the encryption key, using the AES-256 algorithm.
    - SSE-KMS:  Envelope encryption using AWS Key Management System (KMS) and user manages the keys.
    - SSE-C:    Customer provided key (user manages the keys).
  - Client-Side Encryption: You encrypt your own files before uploading them to S3.
  - Cross Region Replication (CRR): allows you to replicate files across regions for greater durability.
    - Must have versioning turned on in both the source and destination buckets.
    - CRR can replicate into a bucket in another AWS account (for example, updating a client's files).
  - Transfer Acceleration: provide faster and secure uploads from anywhere in the world. 
    - Data is uploaded via a distinct URL to an Edge Location. 
    - Data is then sent to the S3 bucket via AWS's backbone network.
  - Storage Classes
    - Standard: Fast, 99.99% availability, 11 9's durability. Replicated across at least 3 AZs.
    - Intelligent Tiering: Moves your data among the tiers as best fits your access patterns.
    - Standard Infrequently-Accessed (IA): Accessed once a month or less (retrival fees make it less cheap than standard if accessed more than twice a month).
    - Glacier:
    - Glacier Deep Archive: Cheapest storage class. Access can take up to 12 hours.



## Snow Series
  - Physical transfer of large-scale data from physical datacenters to AWS.
  - Transfering 100 Terabytes of data over a 1 GBPS internet connection can take > 100 days, Snow series can take less than a week.
  - Transfering 100 Terabytes over high-speed internet can cost thousands of dollars, Snow series can save ~80% of that.
  - Snowball and Snowball Edge are ruggidized containers that hold storage devices.
    - Snowball comes in two sizes: 50 TB (42TB usable) and 80 TB (72 TB usable).
    - Snowball Edge comes in two sizes: 100 TB (83 TB usable) and 100 TB Clustered (45 TB per node).
    - Snowball Edge also has compute power and can therefore undertake local processing and edge-compute workloads.
    - Snowball Edge Clusters can be 5 or 10 devices groups.
    - Snowball Edge has three device configuration options:
      - storage optimized (24 vCPUs)
      - compute optimized (54 vCPUs)
      - GPU optimized (54 vCPUs)
  - Snowmobile is a 45-foot long shipping container pulled by a semi-trailer truck.
    - Comes in one size: 100 PB.
  - You can import or export data with the Snow series, to or from S3 or Glacier.


## Virtual Private Cloud (VPC)

### Core Components
  - Internet Gateway (IGW)
  - Virtual Private Gateway (VPN Gateway)
  - Routing Tables
  - Network Access Control Lists (NACLs)
  - Security Groups (SG)
  - Public Subnets
  - Private Subnets
  - NAT Gateway
  - Customer Gateway
  - VPC EndPoints
  - VPC Peering
### Key Features
  - VPCs are Region Specific, they do not span regions.
  - You can create up to 5 VPCs per region.
  - Every region, by default, gives you one VPC (it's necessary to set up EC2s).
  - You can have 200 subnets per VPC
  - You can use IPv4 CIDR Block and IPv6 CIDR Block for addresses in the VPC.
  - Some VPCs items cost nothing to implement:
    - VPCs, Route Tables, NACLs, Internet Gateways, Security Groups, Subnets, VPC Peering
  - Some pieces do cost to implement:
    - NAT Gateway, VPC Endpoints, VPN Gateway, Customer Gateways
  - DNS Hostnames (if your instances have domain name addresses).
### Default VPC
  - AWS always sets up a default VPC in every region were you activate any services so you can immediately deploy instances.
  - It has a size /16 IPv4 CIDR Block.
  - It has a size /20 default subnet in each Availability Zone.
  - It creates an Internet Gateway to connect your VPC to the internet.
  - It creates a default security group.
  - Creates a default NACL.
  - Associates the default DHCP options set in your AWS account.
  - Automatically creates a main route table.
### Default IP
  - The address 0.0.0.0/0 is known as the default IP address and it represents all possible IP addresses.
  - When 0.0.0.0/0 is in the Internet Gateway's Route Table, it will allow all traffic to the internet.
  - When 0.0.0.0/0 is in the security group's inbound rules, it will allow all traffic from anywhere on the internet.
### VPC Peering
  - Allows the direct connection of traffic from one VPC to another ("making them peers").
  - Peerage is not transitive (If VPC A is a peer of B, and B is a peer of C, that doesn't mean A is a peer of C).
  - Peering uses a star configuration: 1 central VPC, 4 axial VPCs.
  - There cannot be overlapping CIDR blocks (ip addresses have to be unique).
### Route Tables
  - Route tables are used to direct network traffic to the appropriate endpoints.
  - Each subnet must be associated with a Route Table.
  - Subnets can only be associated with one Route Table at a time, but Route Tables can be associated with many subnets.
### Internet Gateway (IGW)
  - The IGW is your VPC's portal to accessing the wide Internet.
  - IGW's perform two functions:
    - 1: They provide targets for internet addressed traffic from within your VPC.
    - 2: They perform network access translation (NAT) for publicly avaialble instances.
  - To route out to the internet you must add the IGW-id as a target with 0.0.0.0/0 as the destination in your Route Table.
### Bastion/Jumpbox
  - Bastions are EC2s located in a public subnet that can be used to SSH or RCP into EC2s protected inside a private subnet.
  - Bastions are also known as Jumpboxes since you jump from one box to another.
  - System Manager's Session Manager replaces the need for Bastions.
  - Note NAT Gateways are for outbound traffic only and should never be used as Bastions.
### Direct Connect
  - Direct Connect provides a direct connection between on-premises locations (offices and data centers) to the AWS backbone.
  - It comes as a Lower Bandwidth (50M - 500M) and Higher Bandwidth (1 GB or 10 GB) speeds.
  - It provides a cheaper and more consistent (i.e. reliable and secure) connection than connecting through the public internet.
### VPC Endpoints
  - VPC Endpoints allow you to privately connect a VPC to other AWS services (i.e. traffic stays inside of AWS).
  - Instances do not need a public IP address to communicate with other AWS resources.
  - They are horizontally-scaled, redundant, and highly available VPC component.
  - Eliminates the need for an Internet Gateway, NAT device, VPN connection, or AWS Direct Connection.
  - Comes in two varieties:
    - 1: Interface Endpoints
    - 2: Gateway Endpoints
### Interface Endpoints
  - Interface Endpoints are Elastic Network Interfaces (ENI) with a private IP address which serve as an entry point for traffic.
  - Powered by AWS PrivateLink.
  - Supports the following services:
      - API Gateway, CloudFormation, CloudWatch, Kinesis, SageMaker, Codebuild, AWS Config, EC2 API, ELB API, AWS KMS, Secrets Manager, Security Token Service, Service Catalog, SNS, SQS,Systems Manager, Marketplace Partner Services,Endpoint Services in other AWS accounts.
  - Pricing is: $0.01/hr pr VPC Endpoint per AZ; $0.01/GB data processed.
### Gateway Endpoints
  - A Gateway Endpoint is a gateway that is a target for a specific route in your Route Table.
  - Only supports Amazon S3 and DynamoDB.
  - VPC Gateway Endpoints are free.
### VPC Flow Logs
  - Allow you to capture IP traffic information in and out of network interfaces inside of your VPC.
  - They are set up within the VPC options.
  - They have three levels of specificity:
    - 1: Entire VPC
    - 2: Specific Subnets
    - 3: Specific Network Interfaces
  - Higher levels cascade to lower ones (e.g. level 1 logs the same as level 2 and 3 and more and level 2 logs the same as level 3).
  - You cannot change the configuration of a flow log after it's created.
  - VPC Flow Logs cannot be taged like other AWS resources.
  - You cannot enable flow logs for VPCs which are peered with your VPC unless it is in the same account.
  - VPC Flow Logs can be delivered to an S3 or CloudWatch Logs.
  - VPC Flow Logs contains the source and destination **IP addresses** (not hostnames).
  - Some instance traffic is not monitored:
    - Instance traffic generated by contacting the AWS DNS servers
    - Windows lisence activation traffic from instances
    - Traffic to and from the instance metadata address (169.254.169.254)
    - DHCP Traffic
    - Any traffic to the reserved IP address of the default VPC router
### Network Access Control List (NACL)
  - VPCs are automatically given a default NACL which allows **all** outbound and inbound traffic.
  - Each subnet within a VPC must be associated with a NACL.
  - Subnets can only be associated with 1 NACL at a time. Associating a subnet with a new NACL will break the previous association.
  - If no NACL is not explicitly associated with a subnet, the subnet will automatically be associated with the default NACL.
  - NACLs have inbound and outbound rules (just like Security Groups) but NACLs can either allow or deny traffic (whereas SGs can only have rules that allow traffic).
  - NACLs are **stateless**, meaning any traffic allowed in is also allowed out.
  - By default, when first created NACLs are set to deny all traffic.
  - NACLs contain a numbered list of rules that get evaluated in order from lowest to highest, the first rule pertinent to the specific traffic is applied.
  - If you need to blacklist a single IP address, you could do so via the NACL (SGs cannot deny traffic).
### Security Groups
  - Security Groups act as a firewall at the instance level.
  - Unless allowed specifically, all inbound traffic is blocked by default.
  - All outbound traffic from the instance is allowed by default.
  - You can specify for the source address to either be an IP range, a single IP address, or another Security Group.
  - Security Groups are **stateful** (if traffic is allowed inbound, it is also allowed outbound; no need for the SG to recheck it, it remembers the packet).
  - Any changes to a Security Group take effect immediately.
  - EC2 instances can belong to multiple security groups.
  - Security groups can contain multiple EC2 instances.
  - You cannot block specific IP addresses with a Security Group; they cannot have deny rules, only allow rules.
  - You can have up to 10,000 Security Groups per Region (default is 2,500).
  - You can have 60 inbound and 60 outbound rules per Security Group.
  - Security Groups can be nested (i.e. a Security Group protecting some assets inside a larger Security Group containing even more assets).
  - You can have 16 Security Groups associated to an Elastic Network Interface (ENI) (default is 5).
### Network Address Translation (NAT) Instances and Gateways
  - When creating a NAT instance, you **must disable source and destination checks** on the instance.
  - NAT instances **must exist in a publin subnet**.
  - You must have a route out of the private subnet to the NAT instance.
  - The size of a NAT instance determines how much traffic it can handle.
  - High availability can be achieved using Autoscaling Groups, multiple subnets in different AZs, and automate failover between them using a script
  - NAT Gateways are redundant inside an Availability Zone (can survive the failure of an EC2 instance).
  - You can only have 1 NAT Gateway inside 1 Availability Zone (cannot span AZs).
  - Starts at 5 Gbps and scales all the way up to 45 Gbps.
  - NAT Gateways are the preferred setup for enterprise systems.
  - There is no requirement to patch NAT Gateways, and there is no need to disable Source/Destination checks for the NAT Gateway (as there is with Nat Instances).
  - NAT Gateways are automatically assigned a public IP address.
  - Route Tables for the NAT Gateway **must** be updated.
  - Resources in multiple AZs sharing a Gateway will lose internet access if the Gateway goes down, unless you create a Gateway in each AZ and configure route tables accordingly.
 
## Identity Access Managment (IAM)
  - IAM is used to manage access to unsers and resources.
  - IAM is a free service and a universal system (i.e. it is applied to all regions at the same time).
  - A root account is the account initially created when AWS is set up (full administrator).
  - New IAM accounts have no permissions by default until granted.
  - New users get assigned an Access Key Id and Secret Key wehn first created if you give them programmatic access.
  - Access Keys are only used for CLI and SDK (cannot be used to access the management console).
  - Access keys are only shown once, when created. If lost they must be deleted/recreated again.
  - Always set up Multi-Factor Authentication (MFA) on root accounts.
  - Users must enable MFA on their own (since they need to set up the MFA device), meaning the Administrator cannot turn MFA on for any user. To enforce usage of MFA on subordiante accounts, the administrator can require MFA to be active to access services, de facto requiring MFA on subordinate accounts.
  - IAM allows your set password policies to set minimum password requirements or require password rotation.
  - **IAM Identities** are Users, Groups, and Roles.
  - **IAM Users** are end users who log into the console or interact with AWS resources programatically.
  - **IAM Groups** allow you to group your users so they all share permissions needed for their teams/departments (e.g. Admins, DevOps, Auditors)
  - **IAM Roles** associate permissions to a Role that can be assigned to a specific user or group when needed.
  - **IAM Policies** are JSON documents which grant permissions for a specific user, group, or role to access services. Policies are attached to IAM identities (Users, Groups, Roles).
  - **Managed Policies** are generic policies provided by AWS and cannot be edited.
  - **Customer Managed Policies** are bespoke policies created by the administrator which can be edited.
  - **Inline Policies** are policies which are one-offs directly attached to a user, they cannot be attached to multiple users.

## Cognito
  - Cognito is a decentralized managed authentication system. When you need to easily add authentication to your mobile and desktop apps, think Cognito.
  - **User Pools** are user directories which allow users to authenticate their identities using OAuth to IpD such as Facebook, Google, and Amazon to connect to web applications.
    - Cognito User Pool is itself an IpD.
  - User Pools use **JSON Web Tokens (JWTs)** to persist authentication.
  - **Identity Pools** provide temporary AWS credentials to access services (e.g. S3, DynamoDB).
  - **Cognito Sync** can sync user data and preferences across devices with one line of code (powered by SNS).
  - **Web Identity Federation** exchanges identity and security information between an identity provider (IdP) and an application.
  - **Identity Provider (IdP)** is a trusted provider of your user identity that lets you use authenticate to access other services (e.g. Facebook, Twitter, Google, Amazon).
  - **OIDC** is a type of Identity Provider which uses OAuth
  - **SAML** is a type of Identity Provider which is used with Single Sign On (SSO).

## AWS Command Line Interface (CLI)
  - CLI stands for command line interface
  - SDK stands for Software Development Kit
  - The AWS CLI lets you interact with AWS from anywhere by using a command line.
  - The AWS SDK is a set of API libraries that let you integrate AWS services into your applications.
  - **Programmatic Access** must be enabled per user via the IAM console to use the CLI or SDK.
  - **aws configure** command is used to setup you AWS credentials for the CLI.
  - The CLI is installed via Python script.
  - Credentials get stored in a plain text file (whenever possible use roles instead of AWS credentials).
  - The SDK is avaialble for the following programming languages:
    - C++
    - Go
    - Java
    - Javascript
    - .NET
    - Node.js
    - PHP
    - Python
    - Ruby

## Domain Name System (DNS)
  - Internet Protocol Version 4 (IPv4) - 32-bit address space, composed of 4 8-bit numbers seperated by dots.
    - e.g. 52.216.8.34
    - MIN: 0.0.0.0
    - MAX: 254.254.254.254
    - has a limit to the total number of possible addresses (255^4 = 4,228,250,625 => less than 1 address per person on the planet).
  - Internet Protocol Version 6 (IPv6) - 128-bit address space, composed of 8 16-bit hexadecimal numbers seperated by colons.
    - e.g. 2001:0db8:85a3:0000:0000:8a2e:0370:7334
    - MIN: 0000:0000:0000:0000:0000:0000:0000:0000
    - MAX: ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff
    - has virtually unlimited addresses ((16^4)^8 = 3.403x10^38 => more than 48.6x10^28 per person on the planet).
  - **Top-Level Domain** - example**.com**, the last part of a domain name.
  - **Second-Level Domain** - example**.co**.uk, the second to last part of a domain name (if it exists, note example.com doesn't have one).
  - **Domain Registrar** is a third party company who you register domains through
  - **Name Server** is the server(s) which conatin the DNS records for a domain.
  - **Start of Authority (SOA)** is the first record held in the DNS and it contains information about the DNS zone and associated DNS records.
  - **A-Record** the DNS record which lets you convert a domain name into an IP address.
  - **CNAME Record** the DNS record which lets you convert a domain name into another domain name.
    - CNAME stands for Canonical Name
  - **Time to Live (TTL)** is the time that a DNS record will be cached for (i.e. how long a local machine remembers that www.example.com == 8.214.23.213).
    - Lower TTL time means that changes will propogate faster, but it will mean more traffic to the DNS server for conversion.
  
## Route 53
  -  Route 53 is a DNS provider where users can register and manage domains and create record sets. (Think GoDaddy or NameCheap)
  - Simple Routing - default routing policy, multiple addresses result in a random endpoint selection.
  - Weighted Routing - Split traffic based on different "weights" (i.e. ratios or percentages) assigned to different endpoints.
  - Latency-Based Routing - Split up traffic based on region, for the lowest possible latency for users.
  - Failover Routing - Primary site in one location, secondary data recovery site in another (changes to the secondary after primary fails a health check).
  - Geolocation Routing - Route traffic based on the geographic location of a requests origin.
  - Geo-proximity Routing - Route traffic based on geographic location using 'Bias' values (needs Route 53 Traffic Flow).
  - Multi-value Answer Routing - Return multiple values in response to DNS queries. (using health checks)
  - Traffic Flow - Visual editor, for chaining routing policies, can version policy records for easy rollback.
  - AWS Alias Record - AWS' smart DNS record, detects changed IPs for AWS resources and adjusts automatically.
  - Route 52 Resolver - Lets you regionally route DNS queries between your VPCs and your network in hybrid environments.
  - Health checks can be created to monitor and automatically over endpoints. You can have health checks monitor other health checks.

## Elastic Compute Cloud (EC2)

### EC2 Basics and Deployments
  - Configure your EC2 by choosing your **OS, Storage, Memory, Network Throughput**.
  - Launch and SSH into your server within minutes.
  - EC2 comes in variety Instance Types specialized for different roles:
    - **General Purpose**: Balance of compute, memeory, and networking resources
      - A1, T3, T3a, T2, M5, M5a, M4
    - **Compute Optimized**: Ideal for compute bound applications that benefit from high performance processors
      - C5, C5n, C4
    - **Memory Optimized**: Fast performance for workloads that process large data sets in memory
      - R5, R5a, X1e, X1, High Memory, z1d
    - **Accelerated Optimized**: Hardware accelerators, or co-processors
      - P3, P2, G3, F1
    - **Storage Optimized**: High, sequential read and write access to very large data sets on local storage.
      - I3, I3en, D2, H1
  - Instance sizes generally double in price and key attributes.
  - **Placement Groups** let you choose the logical placement of your instances to optimize for communication, performance, or durability. Placement groups are free.
    - Cluster
      - packs instances close together inside an AZ
      - low-latency network performance for tightly-coupled node-to-node communication
      - well-suited for High Performance Computing (HPC) applications
      - Clusters cannot be multi-AZ
    - Partition
      - spreads instances across logical partitions
      - each partition does not share the underlying hardware with other partitions (rack per partition)
      - well-suited for large, distributed and replicated workloads (Hadoop, Cassandra, Kafka)
    - Spread
      - Each instance is placed on a different rack
      - When critical instances should be kept separate from each other
      - You can spread a max of 7 instances
      - Spreads can be  multi-AZ
  - **User Data** is a script that will be automatically run when launching an EC2 instance, like a .bashrc for the image.
  - **MetaData** a file of metadata about the current instance. You access this metadata via a local endpoint when SSH'd into the EC2 instance.
    - e.g. curl http://169.254.169.254/latest/meta-data would retireve the instance metadata at that ip address, which would include instance type, current ip address, etc.
  - **Instance Profiles** is a container for an IAM role that you can use to pass role information to an EC2 instance when the instance starts.

### EC2 Pricing
  - EC2 has four pricing models: **On-Demand**, **Spot**, **Reserved Instances (RI)**, and **Dedicated**
  - **On-Demand** (least commitment)
    - Low cost and flexible
    - Only pay per hour
    - Use case: short-term, spiky, unpredictable workloads, first time apps
    - Ideal when your workloads cannot be interrupted
  - **Reserved Instances** (up to 75% off, best long-term value)
    - Use case: steady state or predicatable usage
    - Can resell unused reserved instances (Reserved Instance Marketplace)
    - Reduced pricing is based on Term x class Offering x Payment Option
    - **Payment Terms**: 1 year or 3 year
    - **Payment Options**: All Upfront, Partial Upfront, and No Upfront
    - **Class Offerings**
      - **Standard** Up to 75% reduced pricing compared to on-demand. Cannot change RI Attributes.
      - **Convertible** Up to 54% reduced pricing compared to on-demand. Allows you to change RI Attributes if greate or equal in value.
      - **Scheduled** You reserve instances for specific time periods e.g. once a week for a few hours. Savings vary.
  - **Spot Pricing**
    - Up to 90% off (biggest savings)
    - Request spare computing capacity in the AZ
    - Flexible start and end times
    - Use case: can handle interruptions (servers randomly stopping and starting)
    - Use case: for non-critical background jobs
    - Instances can be terminated by AWS **at any time**
    - If your instance is terminated by AWS, you do not get charged for a partial hour of usage, only fully completed hours.
    - If you terminate an instance you will still be charged for any partial hours that it ran.
  - **Dedicated Hosting**
    - Most expensive option
    - Dedicated physical hardware. 
      - Think of other instance options (shared hardware) as an apartment building, same building but separated by walls (virtual isolation)
      - Dedicated hosting is like having your own house, you are the only one in the "building"
    - Can be on-demnad or reserved (up to 70% for reserved)
    - Use case: when you need a guarantee of isolated hardware (e.g. governmental requirements, absolute data security)

### Amazon Machine Image (AMI)
  - AMI provides the information required to launch an instance.
  - AMIs are region specific, if you need to use an AMI in another region you can copy an AMI into the destination region via Copy AMI in the Actions menu.
  - You can create an AMI from an existing EC2 instance that's **running or stopped**.
  - **Community AMI** are free AMIs maintained by the community.
  - **AWS Marketplace** free or paid subscription MAIs maintained by vendors.
  - AMIs have an AMI ID. The same AMI (e.g. Amazon Linux 2) will vary in both AMI ID and options (e.g. Architecture options in different regions).
  - An AMI holds the following information:
    - A template for the root volume for the instance (EBS Snapshot or Instance Store template), e.g. an operating  system, an application server, and applications.
    - Launch permissions that control which AWS accounts can use the AMI to launch instances.
    - A block device mapping that specifies the volumes to attach to the instance when it's launched.

### Auto Scaling Groups (ASG)
  - An ASG is a collection of EC2 instances grouped for scaling and management.
  - Scaling Out is what we call it when servers are added.
  - Scaling In is what we call it when servers are removed.
  - Three auto scaling triggers: Capacity Settings, Health Check Replacements, Scaling Policies
    - Capacity Settings
      - The size of an Auto Scaling Group is based on Min, Max, and Desired Capacity variables.
      - **Min** is how many EC2 instances should be running at a minimum.
      - **Max** is how many EC2 instances allowed to be running at a maximum.
      - **Desired Capacity** is how many EC2 instances should ideally be running at any given time.
      - ASG will always automatically launch the minimum number of instances at start up, at least.
    - Health Check Replacements
      - EC2 Health Check Type: ASG will perform health checks on the EC2 instances to determine if there are any **hardware or software issues**. If there are it will kill the malfunctioning instance and spin up a new instance to replace it.
      - ELB Health Check Type: ASG will perform a health check based on the ELB health check. ELB can perform health checks by pinging an HHTP(S) endpoint in the instance and check the response against an expected response. If the instance doesn't perform as expected (i.e. gives a different response than expected) then ASG will terminate and replace the instance.
    - Scaling Policies
      - Target Tracking Scaling Policy: Maintains a specific metric at a target value (e.g. if average CPU utilization exceeds 70%, spin up a new instance).
      - Simple Scaling Policy: Scales whenever an alram is breached. Not recommended, this is a legacy policy method.
      - Scaling Policies with Steps: Scales when an alarm is breached, can excalate based on alarm value changing.
  - ELB Integration: ELBs can be integrated with ASG to get richer health checks. They are divided into Classic Load Balancers (CLBs) which can be linked directly, or Application Load Balancers (ALBs) and Network Load Balancers (NLB) which must be linked through defining a target group.
  - Launch Configurations: Essentially like setting up an instance to be launched, but it saves the options to launch needed EC2s when needed.
  - When an ASG launches a new instance it uses a Launch Configuration which holds teh configuration values for that new instance (e.g. AMI, InstanceType, Role).
  - Launch Configurations cannot be edited and must be cloned or a new one created to make changes.
  - Launch Configurations must be manually updated by editing the Auto Scaling settings.

## Elastic Load Balancer (ELB)
  - There are three Elastic Load Balancers: **Network**, **Application**, and **Classic** Load Balancers
  - An ELB must have **at least two** Availability Zones.
  - ELBs **cannot go cross-region**. You must create one per Region.
  - ALBs have **Listeners**, **Rules**, and **Target Groups** to route traffic.
  - NLBs use **Listeners** and **Target Groups** to route traffic.
  - CLBs use **Listeners** and EC2 instances are **directly registered** as targets to a CLB.
  - ALB is for HTTP(S) traffic and as the name implies it is good for Web Applications.
  - NLB is for TCP/UDP is good for high network throughput (e.g. Video Games).
  - Use X-Forwarded-For (XFF) to get original IP of incoming traffic passing through the ELB.
  - You can attach Amazon Certification Manager SSL to any of the ELBs for SSL.
  - ALB has advanced Request Routing rules where you can route based on subdomain header, path, and other HTTP(S) information.
  - Sticky Sessions can be enabled for CLB or ALB and sessions are remembered via Cookie.

## Elastic File System (EFS)
  - EFS supports the Network File System version 4 (NFSv4) protocol.
  - You pay per GB of storage per month ( ($/GB)/month ).
  - Volumes can scale to petabyte size storage.
  - Volumes will shrink and grow to meet current data stored (they are elastic).
  - Can support thousands of concurrent connections over NFS.
  - Your data is stored across multiple AZs within a region.
  - Can mount a single EFS to multiple EC2 instances (as long as they are all in the same VPC).
  - Creates Mount Poimts in all your VPC subnets so you can mount from anywhere within your VPC.
  - Provides Read After Write Consistency.

## Elastic Block Store (EBS)
  - EBS is a virtual hard disk. Snapshots are a point-in-time copy of that disk.
  - Volumes exist on EBS. Snapshots exist on S3.
  - Snapshots ar incremental, only changes made since the last snapshot are moved to S3.
  - Initial Snapshots of an EC2 instance will take longer to create than subsequent Snapshots.
  - If taking a Snapshot of a root Volume, the EC2 instance should be stopped before Snapshotting.
  - Although you can take Snapshots while the instance is still running.
  - You can create AMIs from Volumes, or from Snapshots.
  - **EBS Volumes** are a durable, block-level storage device that you can attach to a single EC2 instance.
  - **EBS Volumes** can be modified on the fly, e.g. storage type or volume size.
  - Volumes always exist in the same AZ as the EC2 instance.
  - **Instance Store Volumes** are a temporary storage type located on disks that are physically attached to a host machine.
  - **Instance Store Volumes** are ephemeral and cannot be stopped. If the host fails then you lose your data.
  - EBS Backed Instances can be stopped and you will not lose any data.
  - By default root volumes are deleted on termination of the EC2.
  - **EBS Volumes** can have termination protection (don't delete the volume on termination).
  - Snapshots or restored encrypted volumes will also be encrypted.
  - You cannot share a snapshot if it has been encrypted.
  - Unencrypted snapshots can be shared with other AWS accounts or made public.

## CloudFront
  - CloudFront is a Content Distribution Network (CDN) that helps websites load faster by serving cached content to nearby locations.
  - CloudFront distributes cached copy at **Edge Locations**.
  - Edge Locations aren't just not read-only, you can write to them (i.e. PUT objects).
  - Time To Live (TTL) is a variable that defines how long until the cache expires (refreshes data).
  - When you invalidate your cache, you are forcing it to immediately expire (refreshes cached data).
  - Refreshing the cache **costs money because of transfer costs** to update Edge Locations.
  - **Origin** is the address of where the original copies of your files reside (e.g. S3, EC2, ELB, Route53).
  - **Distribution** defines a collection of Edge Locations and behaviour on how they should handle your cached content.
  - **Distributions** has two types: **Web Distirbution** (for static website content) and **RTMP** (for streaming media).
  - **Origin Identity Access (OAI)** is used to access private S3 buckets.
  - Access to cached content can be protected via **Signed URLs** or **Signed Cookies**.
  - **Lambda@Edge** allows you to pass each request through a Lambda to change the behaviour of the response.

## Relational Database Service (RDS)
  - RDS is the AWS Solution for relational databases.
  - RDS Instances are managed by AWS, you cannot SSH into the VM running the database.
  - There are 6 relational database options currently available on AWS, Aurora, MySQL, MariaDB, Postres, Oracle, Microsoft SQL Server.
  - Multi-AZ is an option you can turn on which makes an exact copy of your database in another AZ that is only standby.
  - For Multi-AZ AWS automatically synchronizes changes in the database over to the standby slave will be promoted to master.
  - Read-Replicas allow you to run **multiple copies** of your database, these copies allow more read traffic by sharing the load with the primary, but writes go exclusively to the primary and are then disseminated to the replicas.
  - Read-Replicas use Asynchronous replication, i.e. they are not perfectly in-sync with the primary, the writes are replicated when the primary has a free moment to disseminate them.
  - You must have automatic backups enabled to use Read Replicas.

## Aurora Database
  - When you need a **fully-managed** Postgres or MySQL database that needs to scale, automatic backups, high availability, and fault tolerance *think* Aurora.
  - Aurora can run MySQL or Postgres database engines.
  - Aurora MySQL is 5 times faster over regular MySQL.
  - Aurora Postgres is 3 times faster over regular Postgres.
  - Aurora is 1/10th the cost over its competitors with similar performance and availability options.
  - Aurora replicates **6 copies** for your database across **3 Availability Zones**.
  - Aurora is allowed up to **15 Aurora Replicas**
  - An Aurora database can span multiple regions via **Aurora Global Database**.
  - *Aurora Serverless** allows you to stop and start Aurora and scale automatically while keeping costs low.
  - Aurora Serverless is ideal for new projects or projects with infrequent database usage.

## Redshift
  - Redshift is an AWS managed, petabyte-scale data warehousing solution leveraging columnar storage efficiencies.
  - Redshift's most common use case is Business Intelligence.
  - A Data Wharehouse is built to store large quantities of historical data and enable fast, complex SQL-like queries across all of the data using an Online Analytical Processing (OLAP).
  - Compared to databases which are most useful for single source transactions, like adding items to a shopping list, data warehouses are best used for generating large-scale reports about production and usage combining data from multiple sources.
  - Data can be loaded from S3, EMR, DynamoDB, or multiple data sources on remote hosts.
  - Comes in two Configurations: Single Node and Multi-Node.
    - **Single Node** comes in sizes of 160 GB and is a good way to begin using Redshift.
    - **Multi-Node** launches a cluster of nodes with one **Leader Node**, which manages client connections and receiving queries, and up to 128 **Compute Nodes**, which store data and perform query tasks. You set a minimum and maximum number of compute nodes desired.
  - Nodes come in two types: **Dense Compute (dc)**, which are best for high performance loads, and **Dense Storage (ds)**, which is best for holding large amounts of data.
  - Redshift uses a Massively Parallel Processing (MPP) algorithm to distribute queries and data across all available nodes/clusters.
  - Redshift uses **multiple compression techniques** to achieve significant compression relative to traditional relational data stores.
  - When loading data to an empty table, data is **sampled** and the best compression **scheme is selected automatically**.
  - Redshift usage is billed by the total number of hours run across all nodes, 1 unit per node per hour, not including the leader node.
    - So, for example, if you had one leader node and one compute node running, you would only be charged for compute node hours.
  - **Security**: In-transit security provided by using SSL; at-rest encryption with AES-256 which can be applied using either KMS or CloudHSM.
  - **Availability**: Redshift is Single-AZ. To run Multi-AZ you have to spin up clusters in each desired AZ with cloned data.
  - Redshift can asynchronously back up your snapshot to another Region by using S3, which can be used to run parallel/back-up clusters.
  - Redshift attempts to backup 3 copies of your data: the original, spread on the compute nodes, and on S3.
  - Backup Retention is by default set to 1 day but can be increased to a maximum of 35 days.

## DynamoDB
  - A key-value and document database (NoSQL) which can guarantee **consistent reads and writes** at any scale.
    - NoSQL is a database which is neither relational nor does it use SQL to query the data for results.
  - Features:
    - Fully-managed
    - Multiregion
    - Multimaster
    - Durable
    - Built-in Security
    - Backup and Restore
    - In-memory caching
  - Provides: Eventually Consistent Reads (default) or Strongly Consistent Reads
    - Eventually Consistent Reads: When copies are being updated, it is possible to read from a copy before it has had an update applied, leading to inconsistency, but reads are pretty much immediate. Consistency should happen between copies within a second.
    - Strongly Consistent Reads: When updates are queued, data cannot be read, causing higher latency (slower reads), but consistency is guaranteed. All copies should get consistent within a second.
  - All data is stored on SSD storage and is spread across 3 different regions.
  - Terminology:
    - Primary Key = Partition Key (unique) and Sort Key
    - Row = Item
    - Field = Attribute

## CloudFormation
  - When being asked to **automate** the provisioning of resources *think* CloudFormation.
  - When Infrastructure as Code (IaC) is mentioned *think* CloudFormation.
  - CloudFormation files can be written in either JSON or YAML.
  - When CloudFormation encounters an error it will rollback with **ROLLBACK_IN_PROGRESS**.
  - CloudFormation templates larger than 51,200 bytes (0.05 MB) are too large to upload directly and must therefore be imported into CloudFormation via an S3 bucket.
  - **NestedStacks** helps you break up your CloudFormation template into smaller, reusable templates that can be composed into larger templates.
  - **At least one resource** under resources must be definied for a CloudFormation template **to be valid**.
  - **MetaData** is extra information about your template.
  - **Description** is a description of what the template is supposed to do.
  - **Parameters** is how you get user inputs into templates.
  - **Transforms** apply macros (like applying a mod which changes the anatomy of the template to be custom).
  - **Outputs** are values you can use to import into other stacks.
  - **Mappings** are used to map keys to values, just like a lookup table.
  - **Resources** define the resources you want to provision (i.e. what kind of server or service you're requesting), **at least one resource is required** for a template to be valid (otherwise you're not requesting anything!).
  - **Conditions** are logical tests to determine whether resources are created or properties are assigned.

## CloudWatch
  - CloudWatch is a collection of monitoring services for logging, reacting, and visualizing log data.
  - EC2 monitors at 5 minute intervals by default and at 1 minute intervals in Detailed Monitoring.
  - Most other services monitor at 1 minute intervals 
  - Monitoring tools include:
    - **CloudWatch Logs**
      - any custom log data, such as memory usage, rails logs, Nginx logs
      - CloudWatch Logs is used to monitor, store, and access your log files.
      - A **Log Group** is a collection of logs. Log files must belong to a Log Group.
      - A Log in a Log Group is called a **Log Stream**.
      - By default, logs are kept indefinitely and never expire.
    - **CloudWatch Metrics**
      - metrics that are based off of logs (e.g. memory usage)
      - Represents a time-ordered set of data points as a variable monitor.
    - CloudWatch comes with many *predefined** metrics (e.g. CPUUtilization, DiskReadOps, DiskWriteOps, DiskReadBytes, DiskWriteBytes, NetworkIn, NetworkOut, NetworkPacketsIn,   NetworkPacketsOut).
      - Custom metrics can be created using the AWS CLI or SDK.
      - High resolution metrics (metrics measured <1 min) you must use custom metrics to define them. They can be down to 1 second intervals.
    - **CloudWatch Events**
      - Trigger an event based on a condition or on a schedule (e.g. every hour take a snapshot of the server).
      - **Event Source** how an event is triggered (e.g. schedule or event pattern).
      - Schedule is like a serverless **Cron tab**.
      - **Targets** what action you want triggered.
    - **CloudWatch Alarms**
      - Triggers a notification based on metrics which breach a defined threshold.
      - **Type** can be either *static* (setting a threshold limit) or *anomaly detection* (define a band of normal values, alarm if the metric goes to high or low).
      - **Condition** for static thresholds determines if you want an alarm when the metric is greater than, greater than or equal to, less than, or less than or equal to the threshold.
      - **Threshold** the actual metric value you want to set as the threshold.
    - **CloudWatch Dashboards**
      - Create custom dashboards and visualizations from CloudWatch metrics.
    - **CloudWatch Agent**
      - A script that can be installed on an EC2 via the Systems Manager Run Command to get more metrics from an EC2.
        - e.g. memory utilization, disk swap utilization, disk space utilization, page file utilization, log collection

## CloudTrail
  - Logs API calls between AWS services. For when you need to know *who to blame*.
  - CloudTrail is a service that enables **governance**, **compliance**, **operational auditing**, and **risk auditing** of your AWS accounts.
  - CloudTrail helps easily identify which users and accounts made a call to AWS by providing:
    - the source IP address from **where** the call was made
    - the EventTime **when** the call happened
    - the User or UserAgent **who** made the call
    - and **what** Region, Resource, and Action were called
  - CloudTrail automatically logs events by default for the **last 90 days** via **Event History**
  - If you need more than 90 days you need to create a **Trail** which is output to S3 and will not have a GUI like Event History does (instead you'd use AWS Athena to search the Trail log).
  - To ensure logs have not been tampered with you need to turn on the **Log File Validation** option.
  - CloudTrail logs can be encrypted using the **AWS KMS** service.
  - CloudTrail logs can be streamed to CloudWatch logs.
  - CloudTrail logs two kinds of events: **Management Events** and **Data Events**.
    - **Management Events** log management operations performed (e.g. AttachRolePolicy)
    - **Data Events** log data operations for resources such as S3 and Lambda (e.g. GetObject, DeleteObject, and PutObject).
    - Data events are **disabled** by default when creating a trail, since they are common occurences that could increase the cost of logging.
  - Trail logs in S3 can be analyzed using Athena.

## AWS Lambda
  - Lambda allows users to run code without provisioning or managing servers, servers to run code are automatically started when triggered and stop once done.
  - You only pay for the compute time you use, there is no charge when code is not running.
  - Lambda is: **cheap**, **serverless**, and **scales automatically**
  - Lambda natively supports 7 runtime languages: Ruby, Python, Java, Go, Powershell, Node.js, and C#
  - It is also possible to create your own **custom runtime** environments for languages not listed above.
  - Lambda is a good fit for short running tasks where you don't need to customize the OS environment. If you need long running tasks or custom OS environments then consider using **Fargate**.
  - Use cases: Lambda is commonly used to **glue different services together** so the use cases are endless.
    - E.g. Processing thumbnails: A web-service where users upload a picture, it's sent to an S3 bucket, that triggers a Lambda function that reduces the size of the image to thumbnail proportions, then stores that processed image in a thumbnails bucket (for easier listing in a gallery).
    - E.g. Contact Mail Form: A user fills out a contact form on a website, that form is connected to an API Gateway Endpoint which, when something is submitted, triggers a Lambda function which process the contact form and files the data into a record_submission DynamoDB table and uses SNS to notify help desk that a form has been submitted.
  - Lambdas can be invoked via the AWS SDK or triggered from other AWS services, such as:
    - API Gateway
    - AWS IoT
    - Alexa Skills Kit
    - ALB
    - CloudFront
    - CloudWatch Events
    - CodeCommit
    - Kinesis
    - S3
    - SNS
    - etc.
  - Lambdas can also be invoked from partner event sources through Amazon EventBridge, such as: Datadog, PagerDuty, Segment, SignalFx, SugarCRM, Whispir, Zendesk, etc.
  - **Pricing**
    - You pay per invocation (the **duration** and the amount of **memory** used) rounded to the nearest 100 milliseconds and based on the number of requests.
    - The first **1 million requests** per month are free, $0.20 per additional million requests after.
    - The first **400,00 GB seconds** free per month, $0.0000166667 per GB second after.
    - Example: 128MB of memory x 30 million invocations per month x 200ms run time per execution = **$5.83**
  - **Defaults and Limits**
    - By default, you can have 1000 Lambdas running concurrently (although you can have that increased by asking AWS Support)
    - /tmp directory on the limit can contain up to **500MB**
    - By default, Lambdas run in No VPC, you can set them inside of your VPC but then the Lambda will lose internet access.
    - You can set the timeout for a Lambda function, with a maximum of **15 minutes**
    - Memory can be set between **128MB** (the default) and **3008MB** (the maximum).
  - A **Cold Start** refers to an initial triggering of a Lambda function where it is necessary to initiate a server and copy over the Lambda code, which can cause a delay in the user experience.
  - A **Warm Server** referes to the state of an already running and processing server that happens for any following calls to your Lambda function after a Cold Start has happened.

## Simple Queue Service (SQS)
  - Fully managed **queuing service** that enables you to decouple and scale microservices, distributed systems, and serverless applications.
  - A messaging system is used to provide asynchronous communication and decouple processes via messages/events from a sender to a receiver (producer to consumer).
  - Difference between queuing and streaming:
    - Queuing services generally delete messages once they are consumed, consumers have to pull messages, they **require pulls** (no pushes), and they are **not for real-time** processing.
      - Examples: SQS, Sidekiq, RabbitMQ
    - Streaming services generally have it so that mulitple consumers can react to events, events live in streams for long periods of time, and they are used for **real-time** processing.
      - Examples: Kinesis, Kafka, NATS
  - SQS is for **application integration**
  - **Limits and Retention**
    - Message size can be between **1 byte** and **256 KB**
    - There is an Extended Client Library for Java that allows messages larger than 256 KB (up to **2 GB**) by storing the message as an S3 object.
    - Default message retention is **4 days**, but can be set between **1 minute** and **14 days**.
  - **Queue Types**
    - **Standard Queue** 
      - Allows nearly-unlimited numbers of events per second.
      - Guarantees that a message will be delivered **at least once**, meaning it could be delivered more than once.
      - Provides **best-effort ordering** that tries to ensure a message is delivered in the same order that it was sent in.
    - **First In, First Out (FIFO) Queue**
      - Supports multiple ordered message groups winthin a single queue.
      - Limited to 300 transactions per second.
      - Other than these limits, FIFO queues have all the same capabilities as Standard Queues
  - **Visibility Timeout**
    - The amount of time after one consumer has picked up the message from the queue before other consumers can see it again, to help ensure that two consumers aren't  working on the same message redundantly.
    - If a consumer finishes processing an event before the **visibility timeout** ends, then it is deleted from the queue, but if it takes too long, the event will return to the queue where  another consumer can pick it up, resulting in a "double delivery".
    - Visibility timeout is set by default at **30 seconds** although it can be changed to anywhere between **0 seconds** to **12 hours**.
  - **Polling**
    - **Short Polling** is the default setting and it returns messages immediately, even if the message queue is empty.
    - **Long Polling** waits until a message **arrives in the queue** or the **long poll timeout expires**
    - Long polling makes it inexpensive to retrieve messages from your queue as soon as the messages are available.
    - **In most use cases you will want to use long polling**.
    - Set long polling within the SDK by using the .withWaitTimeSeconds() method on a RecieveMessageRequest() call.

## Simple Notification Service (SNS)
  - SNS allows you to **subscribe** and **send notifications** via text message, email, webhooks, lambdas, SQS, and mobile notifications.
  - SNS is for **Application Integration**. It allows decoupled services and apps to communicate with each other.
  - SNS is a highly available, durable, secure, fully managed pub/sub messaging service that enables you to **decouple** microservices, distributed systems, and serverless applications.
  - SNS uses a pub/sub model to decouple publishers and subscribers through an Event Bus that groups messages into Topic channels.
  - Messages are immediately **pushed** to subcribers, so they do not need to pull Topics for messages.
  - **Topics** 
    - allow users to group multiple subscriptions together.
    - are able to deliver to multiple protocols at once (e.g. email, text message, HTTP(S)), protocol format is chosen by the subscriber.
    - Topics can be encrypted via KMS
  - **Subscriptions**
    - Each subscription can only be for one topic and one protocol.
    - Available protocols include:
      - **HTTP and HTTPS** create webhooks into your web-applications
      - **Email** good for internal email notifications (only supports plain text)
      - **Email-JSON** sends you a JSON via email
      - **Amazon SQS** places the SNS message into an SQS queue
      - **AWS Lambda** triggers a Lambda function
      - **SMS** sends a text message
      - **Platform application endpoints** are mobile push notifications (e.g. Apple, Google, Microsoft Baidu)
  - **Application as Subscriber** is a paradigm where the Platform application endpoint method is used to push events to applications.

## ElastiCache
  - Managed **caching** service which either runs Redis or Memcached
  - Caching is the process of storing data in a cache and a cache is a **temporary storage** area. Caches are optimized for fast retrieval with the trade off that data is not durable.
  - **In-Memory Data Store** is when data is stored in memory (i.e. RAM) which makes it extremely fast to retrieve/access but also risks data loss in the case of a fault.
  - Elasticache can run either Redis or Memcached, two of the most popular open source caching engines.
  - Frequently identical queries (i.e. queries you run often) are stored in the cache.
  - ElastiCache is only accessible to resources operating wthin the same VPC to ensure low latency.
  - **Caching Comparison**
    - **Memcached** is generally prefered for caching HTML fragments. Memcahce is a simple key/value stores. The trade of to it being simple is that it is *very fast*.
    - **Redis** can perform many different kinds of operations on your data and has richer data types. It's very good for leaderboards, geospatial data, or keeping track of unread notification data. It's very fast but *arguably* not as fast as Memcached.

## Terminology: High Availability (HA)
  - High Availability describes a system that is designed to remain available by avoiding failing due to internal faults.
  - What could cause a service to become unavailable?
    - When an AZ becomes unavailable (e.g. data center physically flooded)
      - We should run our instances in Multi-AZ, an **Elastic Load Balancer** can route traffic to operational AZs.
    - When a Region becomes unavaialbe (e.g. meteor strike [highly unlikely])
      - We should run instances in another region. We can route traffic to another Region via **Route53**.
    - When a web-application becomes unresponsive (e.g. too much traffic)
      - We should use **Auto Scaling Groups** to increase the amount of instances to meet the demand of traffic.
    - When an instance becomes unavailable (e.g. instance failure)
      - We should use **Auto Scaling Groups** to ensure a minimum number of instances are running and have the **ELB** route traffic to healthy instances.
    - When a web application becomes unresponsive due to distance between geographic locations
      - We should use **CloudFront** to cache static content for faster delivery in nearby regions. We can also run our instances in nearby regions and route traffic using a geolocation policy in **Route53**

## Terminology: Scale Up vs Scale Out
  - **Scale Up** (vertical scaling): Increasing the size of instances
    - Simpler to manage
    - Lower availability (if a single instance fails, the service becomes unavailable)
  - **Scale Out** (horizontal scaling): Adding more of the same
    - More complexity to manage
    - Higher availability (if a single instance fails, it doesn't take the whole service down)
  - Generally you want to **scale out** and **then up** to balance complexity vs availability.

## Elastic Beanstalk
  - Quickly **deploy and manage** web-apps on AWS without worrying about infrastructure.
  - **Elastic Beanstalk** handles the deployment, from capacity provisioning, load balancing, auto-scaling to application health monitoring.
  - 'The Heroku of AWS'
  - It costs nothing to us Elastic Beanstalk (just pay for the reources it provisions, e.g. RDS, ELB, EC2).
  - Not recommended for "production" applications (this advice is aimed at enterprise, large companies).
  - You can choose from the following preconfigured platforms: Java, .NET, PHP, Node.Js, Python, Ruby, Go, and Docker
  - Elastic Beanstalk is powered by CloudFormation, it creates a template that sets up:
    - Elastic Load Balancer
    - Auto Scaling Groups
    - RDS Database
    - EC2 Instance preconfigured (or custom) platforms
    - Monitoring (CloudWatch, SNS)
    - In-Place and Blue/GReen deployment methodologies
    - Security (rotates passwords)
  - Elastic Beanstalk can run **Dockerized** environments

## API Gateway
  - Fully managed service to create, publish, maintain, monitor, and **secure APIs** to your cloud environment at **any scale**.
  - Create APIs that act as a **front door** for applications to **access data**, **business logic**, or **functionality from back-end services**.
  - API Gateway handles all the tasks involved in accepting and processing **up to hundreds of thousands of concurrent API calls**, including traffic management, authorization, and monitoring.
  - API Gateway throttles API endpoints at **10,000** requests per second (can increase via service requests through AWS support)
  - **Key Features**
    - Allows you to track and control any usage of an API, such as throttling requests to help prevent attacks.
    - Expose HTTPS endpoints to define a **RESTful API**
    - Highly scalable (**happens automatically**) and is cost effective
    - Send each API endpoint to a different target
    - Maintain **multiple versions** of your API
  - **Configuration**
    - **Resources**
      - When you create an API you need to also create multiple resources
      - Resources are the URLs you define (e.g. /projects)
      - Resources can have child resources (e.g. /projects/-id-/edit)
        - NOTE: the -id- above is a place holder variable
    - **Methods**
      - You need to define methods on resources (e.g. GET, POST, DELETE)
      - Methods have a many-to-one relationship to resources
      - Methods allow you to make API calls that resource URL with that protocol
      - When you create an API method you need to choose the Integration type (most common is Lambda)
      - You can choose the behavior both as the method request comes in and goes out
      - You can require authorization to your API via AWS Cognito or a custom Lambda
    - **Stages**
      - In order to use your API you need to Deploy it to Stages
      - Stages are versions of your API
      - Often you will have stages for different levels of the deployment process: Development, QA, Beta/Testing, Production
    - **Invoke URL**
      - For each stage AWS provides you an Invoke URL, although it is possible to use a custom domain
      - This is where you'll send your API calls
    - **Deploy API**
      - Every time you finalize changes to your API that you would like to publish you must use the Deploy API action
    - **Caching**
      - API Caching can be enabled to cache your endpoint's response to API calls
      - When enabled, it caches responses from the API Gateway for a specified Time-to-Live (TTL) period
      - When enabled, the Gateway responds by looking up the response from the cache (instead of making a request to the endpoint). This reduces the number of endpoint calls (saving money) and improves latency
    - **Cross-Origin Resource Sharing (CORS)**
      - This is a way that a server at the other end (not client code in the browser) can relax a same-origin policy.
      - Allows restricted resources (i.e. fonts) on a webpage to be requested from a **different domain than the initial resource** that it came from
      - Should always be enabled if using JavaScript/AJAX that uses multiple domains with an API gateway
      - CORS is **always** enforced by the client
      - Can be enabled on all or individual endpoints
  - **Same Origin Policy** is a concept in the application security model where a web browser permits scripts contained in a first webpage to access data in a second webpage.
    - Same Origin Policies are used to help prevent Cross-Site Scripting (XSS) attacks.
    - They only work if both webpages have the same origin.
    - They are enforced at the web browser level.
    - They ignore tools such as Postman or Curl.
  
## Kinesis
  - Fully managed solution for **collecting**, **processing**, and **analyzing streaming data** in the cloud (e.g. stock prices, game data, social network data, click stream data).
  - When you need **"real time"** think Kinesis.
  - Four different kinds of Kinesis Streams: **Data Streams**, **Firehose Delivery Streams**, **Video Streams**, **Data Analytics**
  - **Kinesis Data Streams**
    - Producers send data to the stream where it's processed in shards and sent out to consumers.
    - You pay per running shard.
    - You can have multiple consumers which you must manually configure/code.
    - Data can be persisted from **24 hours (default)** to **168 hours (max)** before it disappears from the stream.
  - **Kinesis Firehose Delivery Stream**
    - You choose **one consumer** from a predefined list (it focuses the stream, like a firehose).
    - Data **immediately disappears** once it is consumed (there is **no data retention**).
    - You can convert incoming data to a few other file formats, compress, and secure the data.
    - You pay dependent on how much data is ingested (very cheap compared to other options).
  - **Kinesis Video Streams**
    - Securely retain video and audio encoded data.
    - Ingest video and audio encoded data from various devices and or service.
    - Output video data to ML or video processing services.
  - **Kinesis Data Analytics**
    - You can specify a Firhose or Data Stream as an input and as an output.
    - Data is run through a **custom SQL query** you provide and the result is sent to the output stream.
    - This allows for real-time analytics of your data.
  - Kinesis Producer Library (KPL) is a Java library to write data to a stream.
  - You can write data to a stream using the AWS SDK, but KPL is more efficient.
  
## Storage Gateway
  - Connects an on-premises software appliance with cloud-based storage (hybrid storage solution).
  - Securely store your data to the AWS Cloud for **scalable** and **cost effective** storage.
  - Software appliance is available as a **virtual machine (VM) image**.
  - Supports both **VMware ESXi** and **Microsoft Hyper-V**.
  - Once installed and activated, you can use the **AWS Console** to create your gateway.
  - There are three types of gateways: **File Gateway**, **Volume Gateway**, and **Tape Gateway**.
  - **File Gateway**
    - Uses NFS or SMB to treat S3 like NAS storage.
    - Stores your files in S3 with a local cache for low-latency access to your most recently used data.
    - Ownership, permissions, and timestamps are all stored within **S3 metadata** of the object that holds the file.
    - Once the file is transferred, it can be managed as a **native S3 object**.
    - **Bucket Policies**, **Versioning**, **Lifecycle Management**, and **Cross-Region Replication** apply directly to files stored in your bucket.
  - **Volume Gateway**
    - Presents your applications with disk volumes using the **Internet Small Computer Systems Interface (iSCSI)** block protocol.
    - Asynchronously backs up copies of your hard disk drives in S3 as **point-in-time EBS snapshots**.
    - **Snapshots** are incremental backups that caputer only changed blocks in the volume.
    - All stored snapshots are also **compressed** to help minimize storage charges.
    - Comes in two variants: **Stored Volumes** and **Cached Volumes**
    - **Stored Volumes**
      - Primary data is **stored locally** while **asynchronously backing up** that data to AWS.
      - On-premise applications still have low-latency access to their full datasets and a durable backup is kept on AWS.
      - Create storage volumes and **mount them as iSCSI devices** from your on-premises server.
      - Any data written to stored volumes is **stored on your on-presmises** storage hardware, while snapshots are backed up to S3.
      - Stored Volumes can be between **1GB and 16TB** in size.
    - **Cached Volumes**
      - Uses **S3 to store your primary data storage** while **retaining frequently accessed data locally** in your storage gateway.
      - Minimizes need to scale up your on-premises storage infrastructure (to keep copies of all of the data), but still provides low-latency access (as though still on-premises) to the most frequently used data.
      - Create storage volumes up to 32TB in size and attach them as iSCSI devices from your on-premises servers.
      - Cached Volumes can be between **1GB - 32GB** in size.
  - **Tape Gateway**
    - Backs up tape data as **virtual tape cartridges** in a **Virtual Tape Library (VTL)** in S3 Glacier storage.
    - The VTL interace lets you use existing tape-based backup application infrastructure.
    - Each tape gateway is **pre-configured with a media changer and tape drives** which are presented to your existing client backup applications as iSCSI devices.
    - Supported by **NetBackup**, **Backup Exec**, and **Veeam**.
