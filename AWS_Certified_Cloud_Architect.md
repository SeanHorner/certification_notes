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
  - All outbound traffis from the instance is allowed by default.
  - You can specify for the source address to either be an IP range, a single IP address, or another Security Group
  - Security Groups are **stateful** (if traffic is allowed inbound, it is also allowed outbound).
  - Any changes to a Security Group take effect immediately.
  - EC2 instances can belong to multiple security groups.
  - Security groups can contain multiple EC2 instances.
  - You cannot block specific IP addresses with a Security Group; they cannot have deny rules, only allow rules.
  - You can have up to 10,000 Security Groups per Region (default is 2,500).
  - You can have 60 inbound and 60 outbound rules per Security Group
  - Security Groups can be nested (i.e. a Security Group protecting some assets inside a larger Security Group containing even more assets).
  - You can have 16 Security Groups associated to an Elastic Network Interface (ENI) (default is 5).