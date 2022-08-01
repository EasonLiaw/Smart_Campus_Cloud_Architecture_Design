# Smart_Campus_Cloud_Architecture_Design

Scalability is one of the well-known problem spaces in web hosting for universities. Traditionally, web applications are deployed using a three-tier web application model that consists of presentation, application and database layers. Prior to the existence of cloud solutions, scalability was provided by adding additional hosts onto these layers with some form of built-in performance, failover and availability features like load balancers and firewalls for security. Eventually, there will be web application hosting issues like infrastructure challenges and architectural issues for dealing with server availability within a traditional web-hosting environment for universities. Therefore, cloud service providers like AWS help universities to provide a seamless transition from traditional web-hosting to a cloud based web-hosting environment. 

Implementing an AWS cloud solution is a cost-effective alternative to having servers with very large capacities to handle workload peaks. Dealing with unexpected traffic spikes is one of the major issues that traditional web-hosting environments have to deal with, as time is required to research and survey for suitable servers to purchase with huge upfront investment cost. Thus, web applications hosted by AWS can leverage on-demand or reserved provisioning of additional servers within a few minutes, so that the university can constantly adjust capacity and costs to actual traffic behaviours with minimal downtime. Automatic scaling approach is one of the most useful features that a cloud solution provides, which a traditional web-hosting environment could not provide.

---

## AWS Cloud Architecture Solution
The following diagram below provides an overview about the proposed solution for hosting web applications for a university by leveraging the AWS Cloud computing infrastructure. Brief descriptions about the proposed AWS resources from the diagram are also included in this section.

![image](https://user-images.githubusercontent.com/34255556/182160602-b6e331be-03db-4e18-a2bc-71ca61fdb30f.png)

1.	<b>DNS Service with Amazon Route 53</b> – A DNS service that simplifies domain management for university web applications

2.	<b>Edge Caching with Amazon CloudFront</b> – Caching service that caches high volume content to decrease latency to customers (i.e. Students, lecturers and business teams)

3.	<b>Edge Security for CloudFront with AWS WAF (Web Application Firewall)</b> – Security service that filters malicious traffic from accessing web applications, including cross-site scripting and SQL injection via customer-defined rules

4.	<b>Edge Security for CloudFront with ACM (AWS Certificate Manager)</b> – Provision, manage and deploy both public and private SSL/TLS certificates on establishing identity of websites

5.	<b>DDoS Protection with AWS Shield</b> – Security service that protects university cloud infrastructure against most common network and transport layer DDoS attacks automatically

6.	<b>Cloud monitoring with CloudWatch</b> – Monitoring service that provides data and actionable insights to optimize resource utilization and respond to system-wide performance changes

7.	<b>Notifications to cloud operations team with Amazon SNS (Simple Notification Service)</b> – Publish-subscribe messaging service for communication between multiple web applications and between application and individuals (i.e. cloud operations team)

8.	<b>Identity and Access Management to AWS resources with AWS IAM</b> – Management service for managing access to AWS services and resources securely through permission settings for allowing and denying access.

9.	<b>Static Storage with Amazon S3 (Simple Storage Service)</b> – Storage service for http-based object storage and static assets like images and lecture video recordings

10.	<b>Load balancing with Elastic Load Balancing</b> – Spreading workloads of web and application servers across multiple availability zones within a region, together with Auto-Scaling groups that auto-adjust capacity of servers for predictable performance at the lowest possible cost

11.	<b>Firewalls with security groups</b> – Defines security for every web server and application server with a stateful, host-level firewall that controls inbound and outbound traffic.

12.	<b>Load reduction from database servers with Amazon ElastiCache</b> – Caching service with Redis to remove workload from database servers and lower latency for frequent data requests

13.	<b>Fully-managed database with Amazon RDS (Relational Database Service)</b> – Creates highly available, multi-AZ database architecture with MYSQL database engine.

14.	<b>File sharing system for web and application servers with Amazon EFS (Elastic File System)</b> – Shared file system that automatically scales in size without need for managing or provisioning the file system

15.	<b>Operational auditing service with Amazon CloudTrail</b> - Auditing service for compliance that allows continuous monitoring of AWS user account activity and detecting unusual API usage of AWS cloud services.

---

## Key Components of AWS Cloud Architecture Solution (Detailed Summary)
### Network Management
From the architecture diagram, a VPC (virtual private cloud) can be configured as a virtual data center in the cloud. The purpose of doing so is that the university could have complete control over their own virtual networking environment, including a selection of their own private IP address range, creation of subnets for web servers, application servers and database servers and configuration of route tables and network gateways.

NAT (Network Address Translation) gateways are introduced that are deployed across multiple availability zones since there are private subnets running EC2 instances from the architecture diagram. NAT gateway maps these private IP addresses assigned to the public IP address on the way out, while preventing incoming internet traffic into ec2-instances running in private subnets. Using these NAT gateways allow for operating system and security patch updates on EC2 instances that are running in private subnets.

To allow for two-way communication between EC2-instances and the internet, IGW (Internet gateways) are introduced, which are configured in route tables for public subnets.

### Content Delivery
Media formats such as video recordings, presentations, live streaming data, audio, notes, picture and other forms of documentation will need to be easily accessed by the university’s stakeholders and employees. Aside from easy access through an internet connection, low latency and high throughput (the ability and speed of transferring data) is a must, especially when dealing with students, lecturers and staff of multiple backgrounds,  geographic locations and various degrees of internet access.

Instead of replicating the entire university web infrastructure across the globe, the solution to this latency issue is to implement Amazon CloudFront as the access point that connects any device to the cloud using a global network of edge locations. It works as a ‘front door’ that ensures only the correct people can gain entry to certain levels of data. High connectivity and low latency rates also ensures that content delivery (loading, transfering, downloading and/or streaming) will remain smooth for users at all times. CloudFront can be optimized to work with AWS services like Amazon S3 and Elastic Load Balancers as origins for delivering both dynamic and static web content, based on the proposed architecture diagram.

### Load Balancing
The main purpose of load balancers is to prevent server overload and maintaining a healthy amount of network activity in each server. By balancing incoming requests and the compute power provided by resources for EC2 instances, a load balancer creates a highly available, scalable, and reliable web application for students and university lecturers. This means that the processing load can be shared across many nodes, rather than being limited to a single server, thus increasing performance during times of high network activity.

Load balancers optimize servers’ capacity through auto scaling, rather than adding resources to improve application server’s performance. It enables and simplifies one of the most important tasks of the cloud architecture: scaling up and down horizontally with high availability. Using load balancers together with auto-scaling makes it easier to add or remove targets from load balancer as needed without disrupting the overall flow of requests to application servers. 

From the architecture diagram, application load balancer is placed between cloudfront and web servers, while network load balancer is placed between web servers and every group of application servers. Application load balancers are used to route traffic based on application-level content that contains HTTP and HTTPS details that makes routing faster and much more efficient. Meanwhile, network load balancers are capable of handling millions of client requests per second, which is best suited for treating volatile incoming traffic.

### Auto Scaling
In the traditional IT world, there are a limited number of servers to handle application workloads. When the number of requests increases, the workload on the servers also increases, which causes high network latency and failures. Variation in application workloads is expected to occur, which will result in over-utilizing or under-utilizing on-premise servers. Amazon Web service provides Amazon EC2 Auto Scaling services to overcome this issue. 

Auto Scaling ensures that Amazon EC2 instances are optimized to handle changes in application workloads at all times using a horizontal scaling approach. Auto-scaling groups can be created for different layers of the architecture (web and application tier) that contains a collection of EC2 instances, while specifying a minimum number of EC2 instances in that group and auto-scaling will maintain and ensure the minimum number of healthy EC2 instances running at all times. From the architecture diagram, one auto scaling group will be used for each app and web server to maintain ongoing traffic across all availability zones in one region.

### Database Configuration
Majority of web applications for a university contain some form of data persistence, usually in the form of a relational or non-relational database. For relational databases, Amazon RDS provides access to capabilities of the most popular database engines available including MySQL. 

In comparison to hosting a Relational Database Management System (RDBMS) system on an Amazon EC2 instance, Amazon RDS provides several advantages that outweighs the cost of using a fully-managed database service, but not limited to the following:
- Amazon RDS has the flexibility of vertical scaling compute resources (VCPU) or storage capacity (RAM) associated with the relational database instance by either making a single API call or using the AWS management console. Note that there is a six hour waiting time-period for every change made to the resources associated with the relational database instance.
- Amazon RDS has the ability to deploy relational database instances across multiple availability zones to protect primary database instances against unplanned shutdowns for higher availability, while ensuring that the primary database instance is replicated synchronously at all times.
- Like all AWS services, there are no upfront investments required and you only pay for the resources you use, instead of an annual subscription of around 2000 USD for using MYSQL Standard edition or 5000 USD for using MYSQL Enterprise edition on premise.

From the architecture diagram, database read and write workloads are separated in the event that some application servers are required to perform transactions for reading data from the database, while others are required to write data to the database simultaneously. This approach prevents all read and write traffic directed to a single database instance that may result in a higher risk of database failover as the university expands its business over time. 

Read database workloads can be distributed to several read-replicas through the use of Route53, which is a DNS service such that a private hosted zone can be configured for allowing read database traffic to be directed to individual read replica endpoints for retrieving data. Note that the read replicas are replicated from the primary database instance asynchronously whenever there are changes to the database, such that there might be a little delay for the changes in the primary database instances to be reflected in the read replicas. However, this approach is mostly suitable for university web applications that are not time-critical.

### Caching within web application
For web application servers that require to perform read-transactions very frequently, executing those transactions through database servers like Amazon RDS directly may result in lower performance. To overcome this potential risk, having in-memory application caches can reduce workload on those services and improve performance and scalability by caching frequently used information. A classic example of frequently used information that can be stored in application caches are session variables when students and lecturers log into their LMS (learning management systems) account. AWS provides Amazon ElastiCache service that makes deployment and operation processes of in-memory application caches easier to manage. In particular, Amazon ElastiCache for Redis allows in-memory cache to automatically scale with workloads and it automatically replaces failed nodes within a cluster of nodes.

From the architecture diagram, write workloads for in-memory application caches are split into two primary nodes within 2 individual shards deployed across multiple availability zones to reduce risk of failover. Within each shard, there is one primary node and two read replicas that are also deployed across multiple availability zones for higher availability. Having read replicas reduces the workload of primary nodes, such that primary nodes can be configured to handle only write traffic of in-memory caches and read replicas handle only read traffic of in-memory caches through a configuration endpoint within a cluster. Note that read replicas are replicated from primary nodes asynchronously, when there are changes to the in-memory application caches.

### Data and media asset storage
The amount of media and data that was generated and stored by the university was already massive to begin with for the past 5 years, and this amount will grow continuously as time goes by. Security will also be an issue as sensitive data begins to accumulate with the increasing number of students and staff to manage. The purpose of Amazon S3 is to store static data in the form of various formats that can be accessed through website links with the integration of CloudFront for streaming lecture video recordings. Once a file is sent for storage inside the S3 Buckets, a URL link will be generated and made available to others depending on their level of access to the systems and database with the help of AWS IAM.

Among the features of Amazon S3 is that the contents of the storage can be mirrored throughout Amazon servers anywhere around the globe, making the content highly accessible to others who are in different countries without compromising on latency. Aside from storing new content, archiving will be an important aspect of media storage because old files that are more than 5 years old in this case can be made inactive and securely stored for long periods of time, but easily accessible should the need for it arrives. Amazon S3 Glacier is a low cost solution for such archiving purposes. Because of its high durability and tight security features, data can be stored for long periods of time with retrieval rates that can be custom set by the university. 

In order to facilitate media storage and file transfer, Elastic File System (EFS) for Linux is added to ensure that files can be managed without interrupting other applications within the Amazon VPC used and lowers latency. Because the storage capacity is elastic and dynamic, the system will grow and shrink automatically as files are added or removed. This feature is handy to have because it will only be billed based on the amount of storage used, unlike Elastic Block Storage (EBS) which is fixed cost in terms of storage amount.

### Cloud monitoring
Cloud monitoring is a broad category that includes monitoring of web and cloud applications, infrastructure, networks, platform, application, and microservices to ensure utilized services on the cloud are running smoothly and efficiently. It is still possible to build high-level tools to track and monitor the overall state of the AWS environment, but as the systems grow larger it becomes complex to carry out manual monitoring. Therefore, AWS provides versatile monitoring tools like Amazon CloudWatch, SNS (Simple Notification Service) and CloudTrail that makes monitoring of AWS infrastructure simple for universities.

Amazon CloudWatch provides real-time monitoring of AWS resources and customer applications running on AWS infrastructure. CloudWatch also provides a comprehensive at-a-glance view of AWS infrastructure to keep track of application performance, spot trends and troubleshoot operational issues. In addition, Amazon CloudWatch configures high-resolution alarms and sends real-time notifications in case of sudden operational changes in the AWS environment. 

Alerts can also be configured through setting rules and trigger alarms by sending notifications using Amazon SNS (Simple Notification Service). Amazon SNS allows cloud operation teams to push messages to Windows, Google, Apple and certain internet-connected smart devices by using an application programming interface (API) or through the AWS management console. Once a message is published to the service, it can be sent multiple times to different recipients through a topic that acts as the access point for both publishing alerts and subscribing to receive future alerts based on publish-subscribe model.

Amazon CloudTrail enables governance, compliance, operational auditing and risk auditing of the university’s AWS account. With CloudTrail, users can log, continuously monitor and retain account activity related to actions across their customized AWS cloud infrastructure. CloudTrail provides event history of AWS account activity, including actions taken through the AWS Management Console, command line tools and other AWS services. In addition, you can use CloudTrail insights to detect users who took certain anomaly actions to respond to accordingly.

### Legality
This is an internship project made with 360DIGITMG for non-commercial uses ONLY. This project will not be used to generate any promotional or monetary value for me, the creator, or the user.
