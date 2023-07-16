## WEB STACK IMPLEMENTATION (LEMP STACK)

### Step 0 – Preparing prerequisites
- You need an AWS account and Ubuntu server provisioned. We will implement a similar stack as in [project-one](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-one.md), but with an alternative Web Server – NGINX. You can follow step 0 of [project-one](https://github.com/uzukwujp/Darey.io-Internship/blob/main/project-one.md) to achieve that.


- On the Terminal, run

  `ssh -i <Your-private-key.pem> ubuntu@<EC2-Public-IP-address>`
  
- You should see something like the screenshot below on your console:

![Screenshot from 2022-09-29 21-27-01](https://user-images.githubusercontent.com/23356682/193135278-eabb0ac0-e97a-4112-ae34-77b0fb12201f.png)

### Step 1 – Installing the Nginx Web Server

- To Update your Package Repository run the command below:

  `sudo apt update`
  
- To install Nginx run the command below:

  `sudo apt install nginx`

- To verify that nginx was successfully installed and is running as a service in Ubuntu, run:

  `sudo systemctl status nginx`
  
- You should the something like the screenshot below:

![Screenshot from 2022-09-29 20-29-49](https://user-images.githubusercontent.com/23356682/193137522-2cfd661b-8570-4aef-95b4-58f613d5151e.png)

- You can access your Public-IP-Address from your EC2 instance.

### Step 2 — Installing MySQL

- Run the command below to install mysql:

  `sudo apt install mysql-server`
  
 When prompted, confirm installation by typing Y, and then ENTER.
 
- To secure your database run the preinstalled script below:

  `sudo mysql_secure_installation`
  
 This will ask if you want to configure the VALIDATE PASSWORD PLUGIN. Answer Y for yes or any character for No. Answer Y subsequently when prompted to complete the       process.
 
- Run the command below to confirm mysql is running:

  `sudo systemctl status mysql`
  
- You should find something like the screenshot below:

![Screenshot from 2022-09-29 21-17-23](https://user-images.githubusercontent.com/23356682/193141871-5dbdd763-fe9b-408d-8d66-6202d0bd8573.png)
  
- Confirm that you have access to the database by running the command below:

  `sudo mysql`
  
- Exit the database with the command below:

  `exit`
  
  
### Step 3 – Installing PHP 
While Apache embeds the PHP interpreter in each request, Nginx requires an external program to handle PHP processing and act as a bridge between the PHP interpreter itself and the web server. This allows for a better overall performance in most PHP-based websites, but it requires additional configuration. You’ll need to install php-fpm, which stands for “PHP fastCGI process manager”, and tell Nginx to pass PHP requests to this software for processing. Additionally, you’ll need php-mysql, a PHP module that allows PHP to communicate with MySQL-based databases. Core PHP packages will automatically be installed as dependencies.

- To install these 2 packages at once, run:

  `sudo apt install php-fpm php-mysql -y`
  
  
### Step 4 — Configuring Nginx to Use PHP Processor
- Create the root web directory for your_domain as follows:

  `sudo mkdir /var/www/projectLEMP`
  
- Set logged-in user as the owner of the directory  */var/www/projectLEMP/*

  `sudo chown -R $USER:$USER /var/www/projectLEMP`
  
- open a new configuration file in Nginx’s sites-available directory.

  `sudo vi /etc/nginx/sites-available/projectLEMP`
  
- Paste in the following bare-bones configuration:

  server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

    }

- Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:

  `sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/`
  
- You can test your configuration for syntax errors by typing:

  `sudo nginx -t`
  
- You shall see following message:

 `nginx: the configuration file /etc/nginx/nginx.conf syntax is ok`
 
 `nginx: configuration file /etc/nginx/nginx.conf test is successful`
 
- Disable default Nginx host that is currently configured to listen on port 80, for this run:

  `sudo unlink /etc/nginx/sites-enabled/default`
  
- Reload Nginx to apply the changes:

  `sudo systemctl reload nginx`
  
- Your new website is now active, but the web root /var/www/projectLEMP is still empty. Create an index.html file in that location so that we can test that your new     server block works as expected:

  sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s
  http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
 
- Now go to your browser and try to open your website URL using IP address:

  `http://<Public-IP-Address>:80`
  
  Your screen will look like this

![Screenshot from 2022-09-29 20-36-14](https://user-images.githubusercontent.com/23356682/193142431-1f85a238-1f83-46c2-adea-f029d033fc99.png)


### Step 5 – Testing PHP with Nginx


- Open a new file called info.php within your document root in your text editor:

  `sudo vi /var/www/projectLEMP/info.php`
  
- paste the following lines below into the new file.

  <?php
  phpinfo();

- You can access your website with the Url below:

  `http://`server_domain_or_IP`/info.php`

- You should see something like the screenshot below:

  ![Screenshot from 2022-09-29 21-10-07](https://user-images.githubusercontent.com/23356682/193142670-87564df4-9f73-4014-9e6e-614975fe4afc.png)

- The screenshot above contains sensitive information of your environment. To remove it, Run the command below:

  `sudo rm /var/www/your_domain/info.php`


### Step 6 — Retrieving data from MySQL database with PHP

- First, connect to the MySQL console using the root account:

  `sudo mysql`

- To create a new database, run:

  `mysql> CREATE DATABASE 'example_database';`

- To Create a user and password run the command below:

  `mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';`

- Now we need to give this user permission over the example_database database:

  `mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';`

- To exit mysql as root user, run the command below:

  `mysql> exit`

- To test our database, log in as user: `example_user` using the command below:

  `mysql -u example_user -p`

- change into `example_database` using the command below:
  `mysql> SHOW DATABASES;`

- Next, we’ll create a test table named todo_list. From the MySQL console, run the following statement:

  CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT, content VARCHAR(255), PRIMARY KEY(item_id));

- Insert a few rows of content in the test table. You might want to repeat the next command a few times, using different VALUES:

  `mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");`

- To confirm that the data was successfully saved to your table, run:


  `mysql>  SELECT * FROM example_database.todo_list;`

-  You can exit the MySQL console:

  `mysql> exit`

- Now you can create a PHP script that will connect to MySQL and query for your content. 

  `vi /var/www/projectLEMP/todo_list.php`

- Paste the text below into the blank file:

  <?php
  $user = "example_user";
  $password = "password";
  $database = "example_database";
  $table = "todo_list";

  try {
   $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
   echo "<h2>TODO</h2><ol>";
   foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
   }
   echo "</ol>";
   } catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
  }

- Save and close the file when you are done editing.


- Access the browser through the URL below:

  
  `http://<Public_domain_or_IP>/todo_list.php`

- You should see the screenshot below:

![Screenshot from 2022-09-29 21-16-45](https://user-images.githubusercontent.com/23356682/193143248-25824947-2a4d-4aa7-9358-768d0efe7ba6.png)


Happy Reading
