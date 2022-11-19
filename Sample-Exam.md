## No.1
```
A company stores sensitive files in an Amazon S3 bucket. 
The company uploads files periodically from an application that runs on an Amazon EC2 instance. 
A network engineer must encrypt all the files that the company uploads to the S3 bucket in transit.

Which solution will meet these requirements?
```
- Configure a bucket policy that denies all actions that are sent over an unencrypted connection.


> Correct. 
> The aws:SecureTransport IAM condition key checks whether a request was made over a secure connection. 
> This key, along with a deny statement, will prevent any action on an S3 bucket that is sent over an unsecure channel.
> For more information about S3 encryption in transit, see Security Best Practices for Amazon S3.
> https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html

## No.2
```
A network engineer must configure a VIF for a newly provisioned 1 Gbps AWS Direct Connect connection. 
A company will use the new VIF to access resources inside of a VPC from an on-premises network.

Which configuration values does the network engineer need to provide? (Select TWO.)
```
![image](https://user-images.githubusercontent.com/60680996/201679958-1cc83b46-1165-458c-9609-4fc54eee126e.png)
![image](https://user-images.githubusercontent.com/60680996/201680054-89cf19c4-0ab0-4638-b98f-f71ac0bc1de3.png)

## No.3
```
A company plans to migrate applications from its on-premises data center to the AWS Cloud. 
The company will begin by hosting 20 applications in a single AWS Region. 
The company plans to use a dedicated AWS account for each application because of a company policy.

These applications require connectivity to the existing on-premises infrastructure for the company's application authentication service. 
Some of the applications require connectivity to other applications that the company will also move to AWS. 
After the successful migration of the 20 applications, the next migration phase will migrate 1,000 applications.

Which solution will meet these requirements with the LEAST management overhead?
```
![image](https://user-images.githubusercontent.com/60680996/201681079-1ccb5205-5b82-4340-b424-7083a9bde999.png)

![image](https://user-images.githubusercontent.com/60680996/201681169-d1917cfb-9210-47ec-8aa0-bbe51e665420.png)


## No.4
```
A company deployed multiple VPCs in the ap-southeast-1 Region, the us-east-1 Region, and the eu-west-2 Region. 
The company deployed one transit gateway in each Region. 
The company established an AWS Direct Connect connection from an on-premises data center in ap-southeast-1. 
A network engineer configured one Direct Connect gateway. 
The network engineer must complete the configuration of the network path between the on-premises data center and the VPCs in the three Regions.

Which solution will meet these requirements?
```
![image](https://user-images.githubusercontent.com/60680996/201681982-4b1afd23-2d4d-403c-af37-c5c2ed42908f.png)

![image](https://user-images.githubusercontent.com/60680996/201682062-57f8da7b-ad8e-499c-8528-101635881c90.png)


## No.5
```
A network engineer needs to find a solution that prevents DNS traffic from being hijacked. 
The network engineer has configured DNS Security Extensions (DNSSEC) in Amazon Route 53. 
However, the KeySigningKey status is ACTION_NEEDED. This status causes a connectivity outage for the clients that use DNS validating resolvers.

Which action will resolve this issue?
```
![image](https://user-images.githubusercontent.com/60680996/201682566-5f777147-2dfb-4967-87b3-5e5e67f54345.png)

![image](https://user-images.githubusercontent.com/60680996/201682626-8c8686bf-e713-43ef-a723-f2b057b9e08f.png)


## No.6
```
A security engineer pings an Amazon EC2 instance from a home computer (IP address 203.0.113.12) but does not receive a response. 
The security engineer finds the following two records in the VPC flow logs:

2 123456789010 eni-1235b8ca123456789 203.0.113.12 172.31.16.139 0 0 1 4 336 1432917027 1432917142 ACCEPT OK

2 123456789010 eni-1235b8ca123456789 172.31.16.139 203.0.113.12 0 0 1 4 336 1432917094 1432917142 REJECT OK

How can the security engineer determine why the home computer did not receive a response from the EC2 instance?
```
![image](https://user-images.githubusercontent.com/60680996/201683367-efea0c36-3a5c-4150-8bc9-f2af9d6da616.png)


## No.7
```
A financial company wants to establish a private connection between the company's offices and its resources on AWS. The company has many VPCs, Amazon EC2 instances, and databases in the us-east-1 Region and in the us-west-2 Region. A network engineer determines that the company needs 7–8 Gbps of bandwidth between the company's corporate network resources and AWS. The network engineer needs a solution that will provide high resiliency for the company's critical workloads.

Which solution will meet these requirements?
```

![image](https://user-images.githubusercontent.com/60680996/202841913-87d90bfd-2b1e-491d-bb2d-4d589dcd795c.png)

![image](https://user-images.githubusercontent.com/60680996/202841932-d25a985a-10d8-4a5b-a58b-4f65dea14a9e.png)

![image](https://user-images.githubusercontent.com/60680996/202841947-24cacd1e-3f0b-4bf3-b159-cc9a1dfd7a6f.png)



## No.8
```
A network engineer manages two AWS Direct Connect connections between a company's on-premises equipment and AWS. Each connection has a public VIF that the network engineer configured with a private Autonomous System Number (ASN). The network engineer wants to configure active/passive routing between the Direct Connect connections to access AWS public endpoints.

Which combination of BGP configurations must the network engineer apply to the on-premises equipment? (Select TWO.)
```

![image](https://user-images.githubusercontent.com/60680996/202842041-e9ab67d0-d031-4e67-a2e4-711a952d7926.png)

![image](https://user-images.githubusercontent.com/60680996/202842066-416875af-fbc0-4bf1-bb4d-e67eba8e98fa.png)

→自分の回答があっている気がする。サポートに聞いてみよう。

## No.9
```
A company has two AWS Direct Connect connections in different AWS Regions between two on-premises data centers and two VPCs. An inter-data center connection connects the two data centers. A network engineer configured two private VIFs, VIF A and VIF B. The network engineer configured VIF A for the data center in the us-east-1 Region. The network engineer configured VIF B for the data center in the us-west-2 Region. Both VIFs terminate on a single Direct Connect gateway. The network engineer set up the on-premises routers to prefer VIF A for the production networks and VIF B for the development networks.

The company needs an active/passive solution that sustains the complete loss of a Direct Connect link and ensures the symmetry of flows between the on-premises networks and AWS.

Which configurations will meet these requirements? (Select TWO.)
```

![image](https://user-images.githubusercontent.com/60680996/202842545-ddb4d5ce-d9bb-4605-ae50-4a94e619c1c9.png)

![image](https://user-images.githubusercontent.com/60680996/202842564-ea70f457-ae61-49ad-80c3-0af1a745c353.png)

![image](https://user-images.githubusercontent.com/60680996/202842582-bdb9d3a1-bfb0-4c9d-995f-7eaf5e493937.png)

## No.10
```
A company recently established an AWS Direct Connect connection from its on-premises data center to AWS. A network engineer blocked all traffic that is destined for Amazon DynamoDB over the company's gateway to the internet at the company's on-premises firewall. DynamoDB traffic must travel across only the Direct Connect connection. Currently, no one in the on-premises data center can access DynamoDB.

Which solution will resolve this connectivity issue?
```

![image](https://user-images.githubusercontent.com/60680996/202842753-4413528a-205f-43bf-8a64-5f05c001aa9e.png)

![image](https://user-images.githubusercontent.com/60680996/202842768-89c53d7b-4263-4cbd-90fc-3090e0cdcf03.png)


## No.11
```
A company's security team must encrypt all data that leaves the company's on-premises data center at the network layer. Data transmission must use dedicated connections. The security team must also centrally log all traffic flow in Amazon VPC environments. The company orders an AWS Direct Connect connection to build out this design.

Which combination of steps should the company take to ensure that connectivity to AWS meets these security requirements? (Select TWO.)
```

![image](https://user-images.githubusercontent.com/60680996/202842902-38a08235-6bd6-4251-a752-6b65a2843eb4.png)

![image](https://user-images.githubusercontent.com/60680996/202842915-dfec5097-e20c-4fc1-b596-48b367d04f68.png)


## No.12
```
A company has a static website. The company updates the website files quarterly. Customers must use secure connections (HTTPS) to connect to the website. The company wants to use its own domain name for the website (www.example.com). The company creates an Amazon S3 bucket to store the website files. The company creates a wildcard certificate in AWS Certificate Manager (ACM) for example.com.

Which solution will meet these requirements?
```

![image](https://user-images.githubusercontent.com/60680996/202843003-5a604888-8880-4721-922d-48f1ebec50b1.png)

![image](https://user-images.githubusercontent.com/60680996/202843031-6e602a91-cc14-4ccf-bc8f-69c8d94ca0dd.png)


## No.13
```
```
## No.14
```
```
## No.15
```
```
