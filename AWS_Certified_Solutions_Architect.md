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
    - NodeJs
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

