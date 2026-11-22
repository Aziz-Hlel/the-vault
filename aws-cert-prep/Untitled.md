



## type of compute : 

- IaaS (Infrastructure as Service) :
	- provides building blocks for IT : Networking, computers, data storage
	- Exp: EC2,  
- PaaS (Platform as Service):
	- Remove the need to manage the underlying infrastructure 
	- Exp: Elastik BeanStalk, Google App Engine, Vercel
- SaaS (Software as Service):
	- Completed product that is run and managed by the service provider
	- Exp: Rekognition for Machine Learning, Gmail, Shopify, Zoom
	- According to the course many aws services are SaaS



## Aws Global Infrastructure

- AWS Regions :
	- A region is a cluster of data centers
	- Most aws Services are region scoped
	- Regions are composed of multiple AZ, min 3, max 6 and the usual is 3
- AWS Availability Zone :
	- Each AZ  is one or more discrete data centers 
	- AZs are separate from each other, so that they're isolated from disasters
- AWS Data Centers
- AWS Edge Locations/ Point of presence : 
	- +400 Point of presence


- AZ comprised from one or two data centers each AZ is separated from the other to prevent disaster cascading to one another, they're connected with high bandwidth, ultra-low latency networking, the sum of 3 of these AZs (a, b and c) all together, linked together, it will form a region.

## AWS Service Scopes

- Global : IMA, CloudFront, Route53, WAF(Web Application Firewall)
- Region-Scoped : EC2, Lambda, S3, SQS, SNS (most services fall under this category)
- Zonal(AZ)-Scoped :  EC2, EBS volumes

## Shared Responsibility Model Diagram

![[Pasted image 20261121235748.png]]


## IAM: Users & Groups

- root account should not be used or shared
- Groups only contain users, not other groups
- user can belong to no group but that is not best practice
- user can be in multiple groups
- **Least privilege principle** : don't give more permissions than a user needs

### How to increase IMA Users security

- Password Policy : make passwords stronger by setting different password policies such as min length, require specific specialized letters, require users to change their password after a period of time, etc
- MFA: it could be Virtual MFA device such as Google Authenticator and Authy on a phone, or U2F (Universal 2nd Factor) Security Key which is a physical device, basically a flash-disc  
## IMA Policies

### IAM Policy Structure
![[Pasted image 20261122144605.png]]


### How can users access AWS 

- AWS Management Console (protected by password + MFA)
- AWS CLI: protected by access keys, it's a tool that enables you to interact with aws using command-line shell, 
- AWS SDK (Software Development Kit): for code, protected by access keys, Embedded within your application
- AWS CloudShell : it is a browser-based CLI and the recommended alternative of using AWS CLI



stopped at 04-13