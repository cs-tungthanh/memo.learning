**Availability Zone**: 
- 1 AZ can include one or more **data center**
- It's recommend that we should deploy service on at least 2 AZ

**Region**: 
- 1 Region have at least 3 AZ
- Regions are independent with each other

**Edge Locations:**
- is a data center used to deliver content fast to your user
- Services operate at Edge: CDN, Web App Firewall (WAF), Route 53 (DNS Service)

## Keywords
- single-tenant hardware -> EC2 Dedicated
	- EC2 have tenancy type: default vs dedicated
		- **default**: **shared**-tenancy hardware within **multiple** accounts
		- **dedicated**: book physical machine to run instance **within an account.**
			- dedicated host: book a entire physical hardware
			- dedicated instance: instances might share on the same hardware
- Share AWS Resources - multi-accounts -> RAM - **Resource Shared Management**
	- share resource across accounts by creating a resource share
	- share between Organization Units (OU)
	- No incur additional cost

# User
1. IAM Group: can only contain IAM Users
2. IAM User
3. IAM Role
4. IAM Permission

# I. IAM
- IAM Role (also be an entity) is a way to give AWS entities permissions to do stuff on AWS
	- is an AWS entity that define a set of permissions
- IAM Roles cannot be assigned to IAM Users
	- User will assume Role

## EC2 Role
	- IAMReadOnlyAccess: allow EC2 instance to read whatever is in IAM

## IAM Security Tools
1. IAM Credentials Report (account-level)
> show in the admin account
- list all your account's user and the status of their vaious credential
2. IAM Access Advisor (user-level)
> show in each user
- shows the service permission granted to a user and when those services were last accessed
- used to revise your policies

## IAM Best Practices
- Don't use the root account except for AWS account setup
	- Use the root account only to create your first IAM User and a few account/service management tasks. For everyday tasks, use an IAM User.
- One physical user = One AWS user
	- If a friend want to use AWS, don't share credential, we must create a user for them
- Assign users to groups and assign permissions to groups -> to make sure that security is managed at group level + should create a strong password policy
- Enforce user the use of MFA
- Create and use Roles for giving permissions to AWS Services
- Must use access keys for CLI/SDK -> **Never share**
- Audit permissions of your account using Credential Report & IAM Access Advisor


# VPC - Virtual Private Cloud
> Key component: Subnets, Route table, Internet Gateway, NAT Gateway, Elastic IP Address

**Types of VPC Endpoints:** 3 types of VPC Endpoints: Interface Endpoint (PrivateLink), Gateway Endpoint, and Gateway Load Balancer Endpoint (PrivateLink).

- 1 VPC can attach resources on multi-AZ
- 1 VPC is bounded to 1 Region
- 1 AWS Account -> maximum 5 VPC
- 1 VPC -> create multi Subnets. Each Subnet will be on 1 AZ
Purpose: 
- Isolated Network env (dev|stg|prod)
- but if we want to isolate resource visibility, we must use multi account (user cannot see resource on diff env)
### Subnet
> 1 Subnet is bounded to 1 AZ
- Public Subnet: serve user
	- Public subnet will have VPC Route Table to **Internet Gateway** that means all resources in a public subnet can reach to the internet.
- Private Subnet: internal services
	- Private Subnet will have VPC Route Table to **NAT Gateway**
Example: 3-tier web app we usually create 3 subnets
- 1 public subnet: serve request from user
- 1 private subnet: application service
- 1 private subnet: Database

### ENI - Elastic Network Interface
It is a virtual network interface, providing network capabilities to EC2 instances. 
- Khi ta tạo 1 máy ảo (EC2,..) muốn máy này dùng đc trong VPC ta phải có 1 card mạng ảo 
- 1 EC2 can attach multi ENI
	- when we attach ENI to another EC2 3 Addresses will not change: IP Private, Elastic IP Address (EIP), MAC
	- EIP is static IPv4

Allow them to communicate with other resources within a VPC
> Can create independently and attach to an EC2 instance
> Purpose: to create primary private IPv4, one or more secondary IPv4
- One Elastic IPv4 per private IPv4
- On public IPV4
- One or more Security Group
- A Mac Address
- Can move between EC2 in stances for failover purpose
- Elastic Network Interfaces (ENIs) **are bounded to a specific AZ**. You can not attach an ENI to an EC2 instance in a different AZ.

- Khi chuyển sang một máy chủ mới, một card mạng ảo sẽ vẫn duy trì:
	- Địa chỉ IP Private
	- Địa chỉ Elastic IP address
	- Địa chỉ MAC
- ![](../../assets/ENI-MoveIP.png)





## EBS - Elastic Block Storage - single instance application
> Is a network drive (not a physical drive) -> attach to an instances while they run
> EBS Volumes are a kind of network attached storage

You can attach a Volume to any EC2 Instance in the same Availability Zone, then you can detach without losing data, then attach to another instance, and so on. When you stop an Instance, you don't lose data stored on EBS Volumes -- this is why they are called _persistent storage_.

**Use case:** preserve root volume when instance is terminated.
- Allows your instance to persist data, even after their termination.
- One EBS can be ONLY mounted (Attached) to 1 instance at a time.
- Are bounded to a specific AZ
- It use the network to communicate -> there might be a bit of latency
- Have a provisioned capacity:
	- must specify size, IOPS(IO per second) in advance
**EBS multi-attach:** (only for io1, io2 type)
	- EBS Multi-Attach enables you to attach a single Provisioned IOPS SSD volume to **multiple instances** that are in the **same Availability Zone**

![](../../assets/EBS-ex1.png)
### EBS Snapshot
> To migrate an EBS volume across AZ
> 	- Take a snapshot
> 	- Restore the snapshot to another AZ
> 	- EBS backups use IO and you shouldn't run them while your app is handling a lot of traffic
- We can copy a EBS Snapshot to any regions
- Can create volume from a snapshot

## EFS - Elastic File System - distributed apps
> It is something like volume mounted to an instance
- **EFS** is a network file system (NFS) that allows you to mount the same file system to n of EC2 **instances** that are in different AZs.
	- You have a fleet of EC2 instances distributes across AZs that process a large data set. What do you recommend to make the same data to be accessible as an NFS drive to all of your EC2 instances? -> EFS


![](../assets/EBS_EFS_compare.png)

- EFS works with EC2 instance in **multi-AZ**
- HA, scalable, expensive (higher than EBS), pay per use
- Use case:
	- content management, web serving, data sharing, wordpress
	- share website file (static files,..)


## AMI - Amazon Machine Image
- It is actually a snapshot of an existing EC2 instance, AMI are a customization of an EC2 instance.
- We can create AMI from an EC2 instance to save the current state, setting, installed apps,... to an image and can launch it in the future.
- AMIs are built for a specific AWS Region - but you can copy the AMI to target Region and then launch EC2 instances.


**How we can speed up EC2 Instantiating Process?**
- EC2 Instance
	- Using AMI: Install OS deps, your app,... beforehand and launch EC2 instance from the AMI
	- Using EC2 user data: for dynamic configuration
	- Hybrid: AMI and user data
- RDS: restore from snapshot
- EBS: restore from snapshot - the disk will already be formatted and have data

# III. Load Balancer 
There're 2 type of Load balancer
- At Layer 4 (Transport) - using TCP, UDP to manage traffic
- At Layer 7 (App Layer) - make decision based on info in header
### Sticky session - Session affinity
> **Sticky session** known as **session persistence**, **session affinity**
> - is a method for Load Balancer 
> 	- to identify requests coming from the same client
> 	- to always those request to the same server
- "cookie" used for stickiness and it has an expiration data
- 2 types of cookies:
	- Application-based Cookies
		- Custom cookie: generate by the target 
		- Application cookie: Generated by the LB
	- Duration-based cookies
		- Generated by LB
## ELB - Elastic Load Balancer
> ELB is a managed Load Balancer
- Load Balances are servers 
- Forward traffic to multiple servers downstream.
- Seamlessly handle failures of downstream instances
- Do regular health checks to your instances.
- Provide SSL for your websites.
- Enforce stickiness with cookies
- HA across zones
- Separate public traffic from private traffic
- When enable **Cross-Zone Load balancing**, ELB will distribute traffic across all registered EC2 in all AZs

## 1. ALB Application Load balancer - Layer 7
-> Application Layer: Support for HTTP/HTTPS/Web Socket, Config SSL, 
- **Only support a static DNS name - Don't support Elastic IP**
- ALBs can route traffic to different Target Groups based on URL Path, Hostname, HTTP Headers, and Query Strings.
	- Don't route based on location
## 2. NLB - Network Load Balancer - Layer 4
-> Transport Layer: Forward TCP & UDP traffic to your instances, config SSL
- **Only Network Load Balancer provides both static DNS name and static IP.**
- High performance - million request/second
- Less latency ~100ms (ALB ~400ms)
- NLB has one static IP per AZ and supports assigning EIP - Elastic IP
## 3. GLB - Gateway Load Balancer - Layer 3 IP Packets
- Firewall, Intrusion Detection, Prevention systems, Payload manipulation,...
- Single entry/exit for all traffic
- It's going to be a Load balancer that distributes traffic to your virtual applications.
- Use GENEVE protocol on port 6081
- Target Groups
	- EC2
	- IP Addresses (must be private IPs)
	- 

## SNI - Server Name Indication
- solve problem when a single LB uses multiple SSL Certificates onto the web servers (to serve multiple websites)
- is a newer protocol that requires client to indicate hostname of the target server
- ![](../assets/SNI-example.png)

## Connection Draining - De-registration delay
- a connection is being drained 
- stop to send traffic to instance that is being drained (de-register)
- For example: the value of connection draining is 300 ms (default)
- There're 3 EC2, one of them is being removed so that before removing LB will move the traffic to the remaining instances and make it to in **draining state**.

## Questions
#### How to get Client IP if we're using LB?
- When using ALB to distribute traffic, the IP Adds you'll receive requests from will be ALB's private IP Adds.
	- -> to get Client's IP, ALB adds an additional header called `X-Forwared-For` contain client's IP address.
#### Auto-scaling group with custom policy?
Your boss asked you to scale your Auto Scaling Group based on the **number of requests per minute** your application makes to your database. What should you do?
-> Create **a CloudWatch custom metric** then create a CloudWatch Alarm on this metric to scale your ASG

# DNS
![](../assets/URL-DNS.png)
- TLD: Top level domain
- SLD: Second level domain
![](../assets/DNS-Howitwork.png)


## Route 53
- 100% HA (SLA), fully managed and Authoritative DNS
	- Authoritative = customer can update DNS Record
- is Domain Registrar
- Ability to check health of your resources
- 
## DNS Record
- Domain name
- Record Type
	- A: maps a hostname to IPv4
	- AAAA: maps a hostname to IPv6
	- CNAME: maps a hostname to another hostname (only for non-root domain)
	- Alias: maps a hostname to an AWS Resource (root and non-root)
		- example.com -> alb-123.us-east.elb.amazonaws.com
	- NS: Named server
- Value: Ip value
- Routing policy: how route 53 response to queries
	- Simple: 
		- route traffic to a single resource
		- can specify multi-values in the same record - it will be chosen randomly by client
	- weighted
		- we can route 30% traffic to IP 1 and 70% traffic to IP 2
		- Weighted Routing Policy allows you to redirect part of the traffic based on weight (e.g., percentage). It's a common use case to send part of traffic to a new version of your application.
	- Latency
		- redirect to the resource that has the least latency close to us
- TLS: time to live the amount of time the record cached at DNS Resolver.

You have purchased a domain on **GoDaddy** and would like to use Route 53 as the DNS Service Provider. What should you do to make this work
- Create a Public Hosted Zone and update the 3rd party Registrar NS records
- Public Hosted Zones are meant to be used for people requesting your website through the Internet. Finally, NS records must be updated on the 3rd party Registrar.


# Storage Comparison
- Storage Gateway: hybrid cloud (on-premises <-Gateway-> AWS Cloud)
	- S3 & FSx File Gateway
		- low-latency access (caching at gateway)
	- Volume gateway
		- Cached volumes
			- S3 is primary data storage
		- Stored volumes
			- data is stored locally on your on-premise in your data center
			- data is asynchronously backed up to S3 as EBS snapshots.
	- Tap gateway
- Transfer Family: FTP, FTPS, SFTP interface on top of S3 and EFS
- DataSync: synchronize data from on-premises to AWS, AWS -> AWS
	- Ex: migrate from S3 -> EFS
	- AWS DataSync is an online data transfer service that simplifies, automates, and accelerates moving data between on-premises storage systems and AWS Storage services, as well as between AWS Storage services.
	- Support: S3, EFS, Amazon FSx for Windows File Server, Amazon CloudWatch, and AWS CloudTrail.
- SnowBall, SnowMoblie: **move** large amount of data to the cloud **physically**
	- **AWS Data Migration:** This service is generally used for smaller-scale data migrations and wouldn't be as efficient for transferring hundreds of Terabytes.
- Database: Mysql, Postgres,...



# CloudFont - CDN  - Layer 7 (app)
- Cache Service: improve performance for both cacheable content (image, video,...)
	- use global network of edge locations (data centers)
- is a global service
	- the cost of data out is vary for each edge location
	- we can reduce the number of edge to reduce cost
- DDOS Protection 
- We can perform passing **CloudFont Invalidation** to force remove cache (file/*, *,...)
- Use CloudFont Geo Restriction to restrict ip by country
- ![](CF-Origins.png)

![](CF_Example.png)

- Because the Edge Location is public so we need create a security group to route traffic to EC2 (it make EC2 become public)
![](CF_ALB_EC2.png)

## Reduce latency
![](AWS_Accelerator_vs_CF.png)
### OAC - Origin Access Control
- OAC prevents direct, public access to your S3 bucket. Users can only access your content through the designated CloudFront distribution.

You have a static website hosted on an S3 bucket. You have created a CloudFront Distribution that points to your S3 bucket to better serve your requests and improve performance. After a while, you noticed that users can still access your website directly from the S3 bucket. You want to enforce users to access the website only through CloudFront. How would you achieve that?    
- Configure your CloudFront Distribution and create an Origin Access Control (OAC), then update your S3 Bucket Policy to only accept requests from your CloudFront Distribution.

# AWS DataSync

![](aws-datasync.png)


# CloudTrail
- **CloudTrail Console** to view the last 90 days of recorded API activity. For events older than 90 days, use Athena to analyze CloudTrail logs stored in S3.
- **CloudTrail Insights** help AWS users identify and respond to unusual activity associated with API calls and API error rates by continuously analyzing CloudTrail management events.
# AWS Config
> **access**, **audit**, and **evaluate** the configuration of **AWS resource**

- To **evaluate compliance** of your resource's configuration
- **AWS Config Remediations** allows you to remediate **non-compliant** resource that are evaluated by AWS Config Rule.
- you want to be notified when someone change service config(EC2, LB,..) -> use **AWS Config notification**.

# CloudWatch
- **CloudWatch Metric Stream** allows to you to send metrics in near realtime to S3 (through Kinesis Data Firehose) and other third party service (Spunk,...)
- **CloudWatch Contributor Insights** to analyze log data and create time series that display contributor data
	- we can use IpAddress as a key to identity Contributor for abnormal detection: (which ip is spamming to the server)
# CloudWatch - CloudTrail - AWS Config
## CloudWatch
- Performance monitoring (metrics, CPU, Memory,...) + Dashboard
- Event & Alerting
- Log Aggregation, Analytis
## CloudTrail

- Record API calls made within your account by everyone
- Can define trails for specific Resource
- Global Service
## AWS Config
- Record configuration changes
- Evaluate resource against compliance rules
- Get timeline of changes and compliance

## For Elastic Load balancer
### CloudWatch
- Monitoring incoming connection metric
- Visualize error codes 
- Make dashboard to get an idea of your LB performance
### CloudTrail
- Track who made any changes with your LB with API Calls
### AWS Config
- Track SG Rules 
- Track configuration changes 
- Ensure an SSL cert is always assigned to the LB (compliance)


How would you **monitor your EC2 instance memory** usage in CloudWatch?
- -> Use the **Unified CloudWatch Agent** to push memory usage as a **custom metric** to **CloudWatch**

I'm going to do a deep dive in all of those over time
It's up to you as SA to really understand trade-offs for doing and why you're doing things and cost implication of what you're doing


# Blue/Green Deployment

- "blue" -> current version
- "green" -> previous version


**Use AWS Global Accelerator to distribute a portion of traffic to a particular deployment***
- Global Accelerator is a network layer service that directs traffic to optimal endpoints over AWS Global network
- if we using DNS it may not fit the usecase that requires fast and controlled transition of the traffic
	- Some client resolves cache DNS for long period
	- so if we use DNS to route traffic we. don't know how long it will take before all users receives update IP address when you update record
With **AWS Global Accelerator**, you can shift traffic gradually or all at once between blue and green environment and vice-versa without being subject to DNS caching on client devices and internet resolvers, traffic dials and endpoint weights changes are effective within seconds.

AWS Global Accelerator and Amazon CloudFront are separate services that use the AWS global network and its edge locations around the world. Amazon CloudFront improves performance for both cacheable content (such as images and videos) and dynamic content (such as API acceleration and dynamic site delivery). AWS Global Accelerator improves performance for a wide range of applications over TCP or UDP by proxying packets at the edge to applications running in one or more AWS Regions.

AWS Global Accelerator is a good fit for non-HTTP use cases, such as gaming (UDP), IoT (MQTT), or Voice over IP, as well as for HTTP use cases that specifically require static IP addresses or deterministic, fast regional failover. Both services integrate with AWS Shield for DDoS protection.

# AWS Site to Site VPN - S2S
- **S2S VPN** is a connection between **On-Premise** and **VPC** network
- **Direct Connect (DX)** - is a dedicated private connection from our remote and on-premise network to the VPC
	- Dedicated connection is a private, physical connection that is established **directly** between on-premise and AWS through a **Direct Connection Location**.
	- **Direct Connect Location** - is a physical locations available around the world where DX is available and accessible
- We also need to create a **Virtual Private Gateway** in our VPC to allow the communication between AWS Direct Connect and VPC.
	- **Virtual private gateway (VGW)**: a concentrator on AWS side of the connection that attaches to VPC, allowing the resources to access to the S2S VPN connection
		- It serves as a connection point between **VPC** and **on-premise network**.
		- It will allow us to access the private resources (EC2...)
		- For the public resources (S3, Glacier) we can connect directly to DX without **VGW** using public IP Address.
- **Direct Connect Gateway** (multiple VPCs in diff regions but in the same account)
	- connect on-premise data center to multiple VPCs.
- **Transit gateway (TGW)**: a hub that connects multiple VPCs and routes traffic between them
	- is an alternative for **VPC Peering**
	- Data transfer between VPC and TGW incurs transit fee
- **Customer Gateway (CGW**): an AWS resource created within our AWS account that points to the customer gateway device configured in our on-premises network.
- **Customer gateway device:** A physical device or software on the on-premises network.

![](../assets/S2S-VPN-Connection.png)