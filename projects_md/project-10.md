## LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS
In this project we will be implementing load balancing using Nginx. Nginx is a powerful software able to handle many concurrent connections. We will also be implementing SSL/TLS. Which secures the site from Man in the Middle Attack.

### Configure Nginx as a Load Balancer

- Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is     used for secured HTTPS connections)

- Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

- Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

- Update the instance and Install Nginx

  ``` sudo apt update
      sudo apt install nginx
  ```
  
- Open the default nginx configuration file and paste the text below:

  `sudo vi /etc/nginx/nginx.conf`


  ``` 
      insert following configuration into http section
      upstream myproject {
      server Web1 weight=5;
     server Web2 weight=5;
     }
     server {
        listen 80;
        server_name www.domain.com;
        location / {
        proxy_pass http://myproject;
    }
    }
    #comment out this line
    # include /etc/nginx/sites-enabled/*;
  ```
  
- Restart Nginx and make sure the service is up and running

  ```
      sudo systemctl restart nginx
      sudo systemctl status nginx
  
  ```
  
 


### Register a new Domain Name and Configure Secured Connection using SSL/TLS Certificates

- Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other)

- Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP

- Update A record in your registrar to point to Nginx LB using Elastic IP address

  
 ![Screenshot from 2022-10-09 14-08-27](https://user-images.githubusercontent.com/23356682/194775905-e62881f3-d2bb-4cb1-b0c2-81a01038fc93.png)
![Screenshot from 2022-10-09 14-27-29](https://user-images.githubusercontent.com/23356682/194775907-0a40043a-f57b-4abf-909f-98973d495b2f.png)

  
  
- Configure Nginx to recognize your new domain name
  Update your nginx.conf with server_name www.<your-domain-name.com> instead of server_name www.domain.com
  
  Your website will look like this
  
  ![Screenshot from 2022-10-09 15-02-50](https://user-images.githubusercontent.com/23356682/194776045-3257f145-e7e1-462a-971c-f955722a75bf.png)

  
- Install certbot and request for an SSL/TLS certificate
  Make sure snapd service is active and running
  
  `sudo systemctl status snapd`
  
- Install certbot

  `sudo snap install --classic certbot`
  
  
- Request your certificate (just follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be   looked up from nginx.conf file so make sure you have updated it on step 4).



   
![Screenshot from 2022-10-09 15-21-52](https://user-images.githubusercontent.com/23356682/194776004-ca573df8-d2ce-42a5-a5af-f8051d3c5c65.png)

- Set up periodical renewal of your SSL/TLS certificate
  By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.
  
  
- You can test renewal command in dry-run mode

  `sudo certbot renew --dry-run`
  
  
- Best pracice is to have a scheduled job that to run renew command periodically. Let us configure a cronjob to run the command twice a day.

- To do so, lets edit the crontab file with the following command:

  `crontab -e`
  
- Add following line:


  `* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1`
