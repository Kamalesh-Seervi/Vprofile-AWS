# Prerequisites
#
- JDK 1.8 or later
- Maven 3 or later
- MySQL 5.6 or later
- Domain 


## Tools:

## Windows

```
Choco install jdk8 -y
```
```
Choco install maven -y
```
```
Choco install awscli -y
```
## MacOS
```
brew install --cask homebrew/cask-versions/adoptopenjdk8
```
```
brew install maven
```
```
brew install awscli -y
```

## Technologies 
- Maven
- JSP
- MySQL
- memcache
- rabbitmq


## Database

## Steps to Create the project 

* Get a SSL certificate for your domain from ACM in AWS.
* Create Security groups.
For load balancer, application and for data base services.

- Now lets create the EC2 instances for backend services 
- use the given userdata scripts for the EC2 instances.

## Route 53
- Create a hosted zones
- Then go to configure route in that simple route
- Do the configuration of the 3 dbs with their known private IPs

<img width="1552" alt="image" src="https://github.com/Kamalesh-Seervi/Vprofile-AWS/assets/107933310/56a45c87-e04b-4f30-8dce-40fa8d23e2f4">

## Now lets create tomcat EC2 instance:
- Paste the tomcat script in userdata section.
- We use ubuntu instead of centos because installation of tomcat is easy in ubuntu.

<img width="1199" alt="image" src="https://github.com/Kamalesh-Seervi/Vprofile-AWS/assets/107933310/39296286-4834-4a2b-8f5c-2570f38eb65c">

## Now in src/main/resources/application change the active host name to route 53 domain names which you created.

```
jdbc.url=jdbc:mysql://db01.vprofile.in:3306/accounts?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
```
```
memcached.active.host=db02.vprofile.in
```
```
rabbitmq.address=db03.vprofile.in
```

## maven install for vprofile 

- Inside the project directory type ```mvn install```
- A target subfolder will be created. 

## To create S3 bucket :

- In local terminal inside target type the following command to create S3 bucket:
``` 
aws s3 mb s3://<s3 bucket name> 
```
- Move the vprofile2.var file to s3 bucket using following command:
```
aws s3 cp vprofile-v2.var s3://<s3 bucket name>/vprofile-v2.war
```
- Thus the folder is exported to the s3 bucket.

## Access TomCat to s3 bucket:

- First create a role for EC2 and grant s3 full access for that instance.
- Add this IAM role to the TomCat isntance.
- To check whether the access for s3 is granted to TomCat, use ssh and type the following command to check the s3 access
```
aws s3 ls s3://<s3 bucket name>
```
## Move s3 bucket to webapps of tomcat9

- First move the s3 bucket to /tmp/vprofile-v2.war

```
 aws s3 cp s3://<s3 bucket name>/vprofle-v2.war /tmp/vprofile-v2.war
```

- Then stop tomcat9
```
systemctl stop tomcat9
```

- Go to this ```/var/lib/tomcat9/webapp``` location and delete the existing ROOT folder and paste the vprofile-v2.war file as ROOT.var
(rename name to ROOt.var)
- Then restart the tomcat9
```
cd /var/lib/tomcat9/webapp
```
```
rm -rf ROOT
```
```
cp /tmp/vprofile-v2.war ./ROOT.war
```
```
systemctl start tomcat9
```

## To check whether the services are connected to tomcat we use telnet command for it:

```
telnet <host name of service>
```

<img width="787" alt="image" src="https://github.com/Kamalesh-Seervi/Vprofile-AWS/assets/107933310/8bc5eafa-0e4f-4d8f-b3e2-005a7283c704">

## Setting a Load balancer ELB:

- First create a target group and use port 8080 with /login.

<img width="1552" alt="image" src="https://github.com/Kamalesh-Seervi/Vprofile-AWS/assets/107933310/4189e919-a1a7-4c74-ad29-ac9e85b75a41">

- Then now create a ELB.
- Add HTTPS to as listener.
- Then in secure listener settings add ACM certificate.
- Thus load balance is created\

## AutoScaling :

<img width="1552" alt="image" src="https://github.com/Kamalesh-Seervi/Vprofile-AWS/assets/107933310/6b33c7ef-0f56-4393-9463-17c18bfa7286">

