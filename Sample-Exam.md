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
```
## No.4
```
```
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