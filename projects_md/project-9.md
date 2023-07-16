## TOOLING WEBSITE DEPLOYMENT AUTOMATION WITH CONTINUOUS INTEGRATION. INTRODUCTION TO JENKINS
Previously, we practices horizontal scaling in project 8 where we deployed a load balancer which distibutes traffic between two webservers. Manually configuring two webservers is not labourous but how about configuring thousands of servers. There where automation comes in.

In this project we introduce Jenkins. Jenkins is an open source self contained automation server. We will be using Jenkins free style project to automate pulling changes in our github repository, build, test and update our NFS server.


### Step 1 – Install Jenkins server

- Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it "Jenkins"

- Install JDK (since Jenkins is a Java-based application)

  ```    
       sudo apt update
       sudo apt install default-jdk-headless
  ```
  
- Install Jenkins


  ```
       wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
       sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
       /etc/apt/sources.list.d/jenkins.list'
       sudo apt update
       sudo apt-get install jenkins
  ```
  
- Make sure Jenkins is up and running


  `sudo systemctl status jenkins`
  
  
  ![Screenshot from 2022-10-08 20-51-13](https://user-images.githubusercontent.com/23356682/194727507-5905dc75-8aa5-4c0e-9c1d-2e0a3cbd3ca8.png)

 


  
- By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

- Perform initial Jenkins setup

  
  ![Screenshot from 2022-10-08 19-09-49](https://user-images.githubusercontent.com/23356682/194727653-53998fe3-d79c-4684-b268-5ddaeeb7e7cc.png)

  


### Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

- Enable webhooks in your GitHub repository settings

  
![Screenshot from 2022-10-08 22-02-07](https://user-images.githubusercontent.com/23356682/194727701-4c9da386-96ae-4496-8633-d2280dd0833e.png)
  
  
- Go to Jenkins web console, click "New Item" and create a "Freestyle project"



### Step 3 – Configure Jenkins to copy files to NFS server via SSH


- Install "Publish Over SSH" plugin.

  
- Configure the job/project to copy artifacts over to NFS server.

  - Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

  - Arbitrary name

  - Hostname – can be private IP address of your NFS server

  - Username – ec2-user (since NFS server is based on EC2 with RHEL 8)

  - Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server


- Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

- To make sure that the files in /mnt/apps have been udated – connect via SSH/Putty to your NFS server and check README.MD file

  
![Screenshot from 2022-10-08 20-48-06](https://user-images.githubusercontent.com/23356682/194727879-91b0b8cf-491c-4bb3-93d1-f3bee59df9dc.png)

  
  

