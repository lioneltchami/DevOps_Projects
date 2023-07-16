## LOAD BALANCER SOLUTION WITH APACHE

In project seven, We extended our 3- tier architecture for website by adding an NFS server. This architecture makes our webservers stateless. However, it implies we wll need to access each webserver directly from its public IP. This is not a good use of our resources because one server may unintentionally process all the request. 

To solve this problem, we need a single point of entry which routes traffic accross our webservers. 

### Step-0 Prerequisites
- Two RHEL8 Web Servers from Project 7

- One MySQL DB Server (based on Ubuntu 20.04) from project 7

- One RHEL8 NFS server from Project 7


### Configure Apache As A Load Balancer

- Create an Ubuntu Server 20.04 EC2 instance,

- Open TCP port 80 on the instance

- Install Apache Load Balancer on the server and configure it to point traffic coming to LB to both Web Servers:


  ```
    
       #Install apache2
       sudo apt update
       sudo apt install apache2 -y
       sudo apt-get install libxml2-dev
       #Enable following modules:
       sudo a2enmod rewrite
       sudo a2enmod proxy
       sudo a2enmod proxy_balancer
       sudo a2enmod proxy_http
       sudo a2enmod headers
       sudo a2enmod lbmethod_bytraffic
  ```
  
- Restart apache2 service

  `sudo systemctl restart apache2`
  
- Make sure apache2 is up and running


  `sudo systemctl status apache2`
  
- Configure load balancing:


  ```
  
  
      sudo vi /etc/apache2/sites-available/000-default.conf
      #Add this configuration into this section <VirtualHost *:80>  </VirtualHost>
      <Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
       </Proxy>
        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
       #Restart apache server
       sudo systemctl restart apache2
  ```
  
- bytraffic balancing method will distribute incoming load between your Web Servers according to current traffic load. We can control in which proportion the traffic     must be distributed by loadfactor parameter.

  You can also study and try other methods, like: bybusyness, byrequests, heartbeat
  
  Your configuration should look like this
  
  ![Screenshot from 2022-10-08 07-15-33](https://user-images.githubusercontent.com/23356682/194692267-5c39a8d4-a973-4485-afa4-7e2a9e4001c8.png)
  
  
- Verify that our configuration works – try to access your LB’s public IP address or Public DNS name from your browser:


  `http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php`
  
  - You should see something like the screenshot below:
  
![Screenshot from 2022-10-07 22-30-36](https://user-images.githubusercontent.com/23356682/194692330-0633472a-8ee4-4ed9-b44d-6a1d48bf73dc.png)

  



