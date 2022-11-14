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
```
## No.6
```
```
## No.7
```
```
## No.8
```
```
## No.9
```
```
## No.10
```
```
## No.11
```
```
## No.12
```
```
## No.13
```
```
## No.14
```
```
## No.15
```
```
