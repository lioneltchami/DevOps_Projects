# WEB STACK IMPLEMENTATION (LAMP STACK) IN AWS

## Step 0 – Prerequisites
- [AWS account setup and Provisioning of an Ubuntu server](https://www.youtube.com/watch?v=xxKuB9kJoYM&list=PLtPuNR8I4TvkwU7Zu0l0G_uwtSUXLckvh&index=7)
- [Connecting to EC2 instance](https://www.youtube.com/watch?v=TxT6PNJts-s&list=PLtPuNR8I4TvkwU7Zu0l0G_uwtSUXLckvh&index=8)

#### STEP 1 — INSTALLING APACHE AND UPDATING THE FIREWALL
Install Apache using Ubuntu’s package manager ‘apt’:

`sudo apt update`

Run apache2 package installation
`sudo apt install apache2`

 Use following command to verify that apache2 is running as a Service in our OS

`sudo systemctl status apache2`

You should find a printout on your console like the one below:
![Screenshot from 2022-09-28 22-26-16](https://user-images.githubusercontent.com/23356682/192891783-61697924-35ec-4c4d-ab6a-1629deb21cb9.png)

- To publicly access your webserver on your browser add new security rule to open TCP port 80. The example is given below:

![Screenshot from 2022-09-28 22-29-21](https://user-images.githubusercontent.com/23356682/192892438-1a4d4119-f03a-42a4-8764-fd2c9cc56394.png)

- Access your webserver using the url below:

`http://<Public-IP-Address>:80`

- You find a picture like this below:
![Screenshot from 2022-09-28 12-04-42](https://user-images.githubusercontent.com/23356682/192892602-bb8b6f0b-21c5-4bf5-8adf-52e24fc73f49.png)

#### STEP 2 — INSTALLING MYSQL

- Install MYSQL

`sudo apt install mysql-server`

- Run preinstalled mysql script to lock down database access. It will prompt to **Configure Password validation**. Enter Y to accept or any other character to object. Enter Y subsequently when prompted to complete the process.

`sudo systemctl status mysql`

You should find screenshot below on your console:

![Screenshot from 2022-09-28 22-35-35](https://user-images.githubusercontent.com/23356682/192893137-93364a4f-7683-460d-9e59-e5266ac21b93.png)

`sudo mysql_secure_installation`

- Confirm that the mysql is running by running the command below:

![Screenshot from 2022-09-28 21-10-15](https://user-images.githubusercontent.com/23356682/192893638-37281111-a279-499a-b0b1-b05a14582e9f.png)

- Exit from mysql by running the command below:

`exit`

#### STEP 3 — INSTALLING PHP

- To install these 3 packages at once, run:

`sudo apt install php libapache2-mod-php php-mysql`

- confirm the version of PHP by running the command below:

`php -v`

- You should find something like this below:
![Screenshot from 2022-09-28 21-15-02](https://user-images.githubusercontent.com/23356682/192894262-0570d94a-35ab-4c29-b2dd-1597e4f36488.png)

#### STEP 4 — CREATING A VIRTUAL HOST FOR YOUR WEBSITE USING APACHE
Apache webserver serves content placed in its document root located at path: `/var/www/html`. This is fine when Apache is required to serve one website. However when Apache is required to serve multiple websites, a **Virtual Host** is configured. **Virtual Host** servers as a container for each website. You require one for each website to be served.

- Create a directory for projectlamp in */var/www/* directory by running the command below:

  `sudo mkdir /var/www/projectlamp`

- Set ownership of projectlamp directory to the logged-in user using the command below:

  `sudo chown -R $USER:$USER /var/www/projectlamp`

- Then, create and open a new configuration file in Apache’s sites-available directory using your preferred command-line editor.

  `sudo vi /etc/apache2/sites-available/projectlamp.conf`
  
- This will create a new blank file. Paste in the following bare-bones configuration by hitting on i on the keyboard to enter the insert mode, and paste the text:

  `<VirtualHost *:80>
    ServerName projectlamp
    ServerAlias www.projectlamp 
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/projectlamp
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>`

- You can use the ls command to show the new file in the sites-available directory

  `sudo ls /etc/apache2/sites-available`
  
- You will see something like this;

  `000-default.conf  default-ssl.conf  projectlamp.conf`

- With this VirtualHost configuration, we’re telling Apache to serve projectlamp using /var/www/projectlampl as its web root directory. If you would like to test Apache without a domain name, you can remove or comment out the options ServerName and ServerAlias by adding a # character in the beginning of each option’s lines. Adding the # character there will tell the program to skip processing the instructions on those lines.

- You can now use a2ensite command to enable the new virtual host:

  `sudo a2ensite projectlamp`

- You might want to disable the default website that comes installed with Apache. This is required if you’re not using a custom domain name, because in this case Apache’s default configuration would overwrite your virtual host. To disable Apache’s default website use a2dissite command , type:

  `sudo a2dissite 000-default`

- To make sure your configuration file doesn’t contain syntax errors, run:

  `sudo apache2ctl configtest`

- Finally, reload Apache so these changes take effect:

  `sudo systemctl reload apache2`

- Your new website is now active, but the web root /var/www/projectlamp is still empty. Create an index.html file in that location so that we can test that the virtual host works as expected:

 `sudo echo 'Hello LAMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s    http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectlamp/index.html`

- Now go to your browser and try to open your website URL using IP address:

  `http://<Public-IP-Address>:80`

- You should find something like this below:

![Screenshot from 2022-09-28 21-21-12](https://user-images.githubusercontent.com/23356682/192897179-19098c7f-c5c7-45d3-8757-6cf19a0ecd5d.png)

#### STEP 5 — ENABLE PHP ON THE WEBSITE
With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. This is useful for setting up maintenance pages in PHP applications, by creating a temporary index.html file containing an informative message to visitors. Because this page will take precedence over the index.php page, it will then become the landing page for the application. Once maintenance is over, the index.html is renamed or removed from the document root, bringing back the regular application page

- In case you want to change this behavior, you’ll need to edit the /etc/apache2/mods-enabled/dir.conf file and change the order in which the index.php file is listed   within the DirectoryIndex directive:

  `sudo vim /etc/apache2/mods-enabled/dir.conf`

` <IfModule mod_dir.c>
        #Change this:
        #DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
        #To this:
        DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
  </IfModule>`

- After saving and closing the file, you will need to reload Apache so the changes take effect:

 `sudo systemctl reload apache2`
 
- Create a new file named index.php inside your custom web root folder:

  `vim /var/www/projectlamp/index.php`

- Paste and save the text below:

  `<?php
  phpinfo();`
  
- You should see something like this below:

![Screenshot from 2022-09-28 21-22-17](https://user-images.githubusercontent.com/23356682/192897425-2e387858-db23-4639-a2f3-098d196987c6.png)
