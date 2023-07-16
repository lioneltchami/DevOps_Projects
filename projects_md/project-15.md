## AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY


![image](https://user-images.githubusercontent.com/52359007/170261120-b583cdf0-dc68-451a-8a01-3873febaaf9c.png)

Project fifteen is all about implementing the infrastructure architecture above and deploying tooling and wordpress websites we deployed in previous projects in AWS.


### Starting Off Your AWS Cloud Project

- Properly configure your AWS account and Organization Unit [watch the video to learn how](https://www.youtube.com/watch?v=9PQYCc_20-Q)

- Create a free domain name for your fictitious company [here](https://www.freenom.com/en/index.html?lang=en)

- Create a hosted zone in AWS, and map it to your free domain from Freenom. [watch how](https://www.youtube.com/watch?v=IjcHp94Hq8A)


![Screenshot from 2022-10-28 10-17-01](https://user-images.githubusercontent.com/23356682/198552653-c43c5e01-d084-4368-a6a3-c516d5d1888a.png)

  

- Create a VPC

![Screenshot from 2022-10-28 03-40-24](https://user-images.githubusercontent.com/23356682/198553006-a201f024-4cc8-4072-a7bd-c72917f12758.png)


- Create subnets as shown in the architecture

![Screenshot from 2022-10-28 03-41-24](https://user-images.githubusercontent.com/23356682/198567831-2673ca37-aa77-41e4-84e2-70a84948806e.png)

- Create a route table and associate it with public subnets


![Screenshot from 2022-10-28 03-41-31](https://user-images.githubusercontent.com/23356682/198566370-1ce93e60-0aab-499d-b8c6-9275a0920a83.png)




- Create an Internet Gateway


![Screenshot from 2022-10-28 11-51-15](https://user-images.githubusercontent.com/23356682/198570497-c6ba237b-009e-43c5-8166-5c5e16109040.png)


- Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble from the Internet)

- Create 3 Elastic IPs


![Screenshot from 2022-10-28 11-53-08](https://user-images.githubusercontent.com/23356682/198570845-ac22f9be-b644-4aca-8b3d-572ac53f49b2.png)



- Create a Nat Gateway and assign one of the Elastic IPs (*The other 2 will be used by Bastion hosts)


![Screenshot from 2022-10-28 03-41-14](https://user-images.githubusercontent.com/23356682/198570975-82cef9f7-db4f-43fd-9da5-18b77d8d9e9b.png)



- Create a Security Group for: 

  - Nginx Servers: Access to Nginx should only be allowed from a Application Load balancer (ALB). At this point, we have not created a load balancer, therefore we will     update the rules later. For now, just create it and put some dummy records as a place holder.

  - Bastion Servers: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your             workstation public IP address. To get this information, simply go to your terminal and type curl www.canhazip.com


  - Application Load Balancer: ALB will be available from the Internet

  - Webservers: Access to Webservers should only be allowed from the Nginx servers. Since we do not have the servers created yet, just put some dummy records as a         place holder, we will update it later.

  - Data Layer: Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) must be carefully             desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have access to EFS Mountpoint.

![Screenshot from 2022-10-28 03-41-48](https://user-images.githubusercontent.com/23356682/198566376-3602abaf-62a1-4647-bb5b-0dcc94930b84.png)

- Set Up Compute Resources for Nginx


  - Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) in any 2 Availability Zones (AZ) in any AWS Region (it is recommended to use the Region that is       closest to your customers). Use EC2 instance of T2 family (e.g. t2.micro or similar)

  - Ensure that it has the following software installed:

     - python : sudo yum install -y  python
     - ntp : sudo yum install ntp
     - net-tools : sudo yum -y install net-tools
     - vim : sudo yum install -y vim-enhanced  
     - wget : sudo yum install -y wget
     - telnet : sudo yum -y install telnet.
     - epel-release : sudo yum install -y epel-release
     - htop : sudo yum install -y htop

  - Create an AMI out of the EC2 instance

  - Prepare Launch Template For Nginx (One Per Subnet)

    - Make use of the AMI to set up a launch template
    
    - Ensure the Instances are launched into a public subnet
    
    - Assign appropriate security group
    
    - Configure Userdata to update yum package repository and install nginx

 

 ![Screenshot from 2022-10-28 00-58-57](https://user-images.githubusercontent.com/23356682/198571324-2fb4194d-8d26-47d0-9c47-5a11b4cfc869.png)
     
     
  - Prepare Launch Template For Nginx (One Per Subnet)

    - Make use of the AMI to set up a launch template
    
    - Ensure the Instances are launched into a public subnet
    
    - Assign appropriate security group
    
    - Configure Userdata to update yum package repository and install nginx

![Screenshot from 2022-10-28 02-37-51](https://user-images.githubusercontent.com/23356682/198572729-98d06357-a34a-4e3d-b3ec-55d174ef6f69.png)
     
  - Configure Target Groups

    - Select Instances as the target type
    
    - Ensure the protocol HTTPS on secure TLS port 443
    
    - Ensure that the health check path is /healthstatus
    
    - Register Nginx Instances as targets
    
    - Ensure that health check passes for the target group

  
  ![Screenshot from 2022-10-28 01-03-20](https://user-images.githubusercontent.com/23356682/198572888-2ed74f99-7e3e-425a-8907-261cb9ed47b4.png)

   
   
  - Configure Autoscaling For Nginx

    - Select the right launch template
    
    - Select the VPC
    
    - Select both public subnets
    
    - Enable Application Load Balancer for the AutoScalingGroup (ASG)
    
    - Select the target group you created before
    
    - Ensure that you have health checks for both EC2 and ALB
    
    - The desired capacity is 2
        - Minimum capacity is 2
        
        - Maximum capacity is 4
        
        - Set scale out if CPU utilization reaches 90%
        
    - Ensure there is an SNS topic to send scaling notifications

 
![Screenshot from 2022-10-28 03-31-32](https://user-images.githubusercontent.com/23356682/198573198-d6fa713e-8183-4632-8993-d658c69b2f1d.png)

   
 - Set Up Compute Resources for Bastion


  - Provision the EC2 Instances for Bastion
  
  - Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) per each Availability Zone in the same Region and same AZ where you created Nginx server
  
  - Ensure that it has the following software installed

    - python
    
    - ntp
    
    - net-tools
    
    - vim
    
    - wget
    
    - telnet
    
    - epel-release
    
    - htop : sudo yum install -y htop
    
  - Associate an Elastic IP with each of the Bastion EC2 Instances
  
  - Create an AMI out of the EC2 instance


  - Prepare Launch Template For Bastion (One per subnet)
  
      - Make use of the AMI to set up a launch template
      
      - Ensure the Instances are launched into a public subnet
      
      - Assign appropriate security group
      
      - Configure Userdata to update yum package repository and install Ansible and git
      
  - Configure Target Groups
  
      - Select Instances as the target type
      
      - Ensure the protocol is TCP on port 22
      
      - Register Bastion Instances as targets
      
      - Ensure that health check passes for the target group
      
      
  - Configure Autoscaling For Bastion


    - Select the right launch template
    
    - Select the VPC
    
    - Select both public subnets
    
    - Enable Application Load Balancer for the AutoScalingGroup (ASG)
    
    - Select the target group you created before
    
    - Ensure that you have health checks for both EC2 and ALB
    
    - The desired capacity is 2
    
    - Minimum capacity is 2
    
    - Maximum capacity is 4
    
    - Set scale out if CPU utilization reaches 90%
    
    - Ensure there is an SNS topic to send scaling notifications


 - Set Up Compute Resources for Webservers

 - Provision the EC2 Instances for Webserver

 -  Now, you will need to create 2 separate launch templates for both the WordPress and Tooling websites

 -  Create an EC2 Instance (Centos) each for WordPress and Tooling websites per Availability Zone (in the same Region).

 -   Ensure that it has the following software installed

      - python
      
      - ntp
      
      - net-tools
      
      - vim
      
      - wget
      
      - telnet
      
      - epel-release
      
      - htop
      
      - php
      
 - Create an AMI out of the EC2 instance

 - Prepare Launch Template For Webservers (One per subnet)
 
   - Make use of the AMI to set up a launch template
   
   - Ensure the Instances are launched into a public subnet
   
   - Assign appropriate security group
   
   - Configure Userdata to update yum package repository and install wordpress (Only required on the WordPress launch template)
   

 ### TLS Certificates From Amazon Certificate Manager (ACM)
 
 You will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB)
 
  - Navigate to AWS ACM
  
  - Request a public wildcard certificate for the domain name you registered in Freenom
  
  - Use DNS to validate the domain name
  
  - Tag the resource

  ### CONFIGURE APPLICATION LOAD BALANCER (ALB)
  Application Load Balancer To Route Traffic To NGINX
  Nginx EC2 Instances will have configurations that accepts incoming traffic only from Load Balancers. No request should go directly to Nginx servers. With this kind     of setup, we will benefit from intelligent routing of requests from the ALB to Nginx servers across the 2 Availability Zones. We will also be able to offload SSL/TLS   certificates on the ALB instead of Nginx. Therefore, Nginx will be able to perform faster since it will not require extra compute resources to valifate certificates   for every request.
  
  - Create an Internet facing ALB
  
  - Ensure that it listens on HTTPS protocol (TCP port 443)
  
  - Ensure the ALB is created within the appropriate VPC | AZ | Subnets
  
  - Choose the Certificate from ACM
  
  - Select Security Group
  
  - Select Nginx Instances as the target group

- Application Load Balancer To Route Traffic To Web Servers
  Since the webservers are configured for auto-scaling, there is going to be a problem if servers get dynamically scalled out or in. Nginx will not know about the new   IP addresses, or the ones that get removed. Hence, Nginx will not know where to direct the traffic.

  To solve this problem, we must use a load balancer. But this time, it will be an internal load balancer. Not Internet facing since the webservers are within a         private subnet, and we do not want direct access to them.
  
    - Create an Internal ALB
    
    - Ensure that it listens on HTTPS protocol (TCP port 443)
    
    - Ensure the ALB is created within the appropriate VPC | AZ | Subnets
    
    - Choose the Certificate from ACM
    
    - Select Security Group
    
    - Select webserver Instances as the target group
    
    - Ensure that health check passes for the target group

 - NOTE: This process must be repeated for both WordPress and Tooling websites.

 - Setup EFS

  Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic Network File System (NFS) for use with AWS Cloud services and on-premises   resources. In this project, we will utulize EFS service and mount filesystems on both Nginx and Webservers to store data.
  
    - Create an EFS filesystem
    
    - Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer
    
    - Associate the Security groups created earlier for data layer.
    
    - Create an EFS access point. (Give it a name and leave all other settings as default)
    
    
  - Setup RDS

  - Pre-requisite: Create a KMS key from Key Management Service (KMS) to be used to encrypt the database instance.

    Amazon Relational Database Service (Amazon RDS) is a managed distributed relational database service by Amazon Web Services. This web service running in the cloud    designed to simplify setup, operations, maintenans & scaling of relational databases. Without RDS, Database Administrators (DBA) have more work to do, due to RDS,      some DBAs have become jobless

   To ensure that yout databases are highly available and also have failover support in case one availability zone fails, we will configure a multi-AZ set up of RDS      MySQL database instance. In our case, since we are only using 2 AZs, we can only failover to one, but the same concept applies to 3 Availability Zones. We will not    consider possible failure of the whole Region, but for this AWS also has a solution – this is a more advanced concept that will be discussed in following projects.

   To configure RDS, follow steps below:
   
    - Create a subnet group and add 2 private subnets (data Layer)
    
    - Create an RDS Instance for mysql 8.*.*
    
    - To satisfy our architectural diagram, you will need to select either Dev/Test or Production Sample Template. But to minimize AWS cost, you can select the Do not
    
    - create a standby instance option under Availability & durability sample template (The production template will enable Multi-AZ deployment)
    
    - Configure other settings accordingly (For test purposes, most of the default settings are good to go). In the real world, you will need to size the database           appropriately. You will need to get some information about the usage. If it is a highly transactional database that grows at 10GB weekly, you must bear that in         mind while configuring the initial storage allocation, storage autoscaling, and maximum storage threshold.
    
    - Configure VPC and security (ensure the database is not available from the Internet)
    
    - Configure backups and retention
    
    - Encrypt the database using the KMS key created earlier
    
    - Enable CloudWatch monitoring and export Error and Slow Query logs (for production, also include Audit)
    
 - Note This service is an expensinve one. Ensure to review the monthly cost before creating. (DO NOT LEAVE ANY SERVICE RUNNING FOR LONG)

 - Configuring DNS with Route53

   Earlier in this project you registered a free domain with Freenom and configured a hosted zone in Route53. But that is not all that needs to be done as far as DNS      configuration is concerned.

   You need to ensure that the main domain for the WordPress website can be reached, and the subdomain for Tooling website can also be reached using a browser.

  Create other records such as CNAME, alias and A records.

  NOTE: You can use either CNAME or alias records to achieve the same thing. But alias record has better functionality because it is a faster to resolve DNS record,     and can coexist with other records on that name. Read here to get to know more about the differences.

  Create an alias record for the root domain and direct its traffic to the ALB DNS name.
  Create an alias record for tooling.<yourdomain>.com and direct its traffic to the ALB DNS name.
  
  
  
![Screenshot from 2022-10-28 10-20-54-2](https://user-images.githubusercontent.com/23356682/198569520-e3a2a3a8-a5e3-461a-8e88-ec4925fa1454.png)











