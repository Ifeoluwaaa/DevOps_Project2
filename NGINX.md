### STEP 1 INSTALLING THE NGINX WEB SERVER
> SSH into EC2 instance

> Update server's package index and install NGINX

       sudo apt update
       sudo apt install nginx
![updating server software packages](./images/NginxUpdate.PNG)
![nginx installation](./images/Nginxinstall.PNG)

> Confirm the web server is successully installed

        sudo systemctl status nginx
![Install_Successful](./images/Install_Success.PNG)

> Add rule to EC2 configuration to open inbound connection through port 80
![Add_port80](./images/AWS.PNG)

> Accessing server locally in Ubuntu shell and from the internet using DNS address or IP address

      curl http://localhost:80
      or
      curl http://127.0.0.1:80
      http://<Public-IP-Address>:80 -from the internet
![Ubuntu_Shell](./images/Ubuntu_html.PNG)
![Browser](./images/Browser%20welcome.PNG)

> Command to retrieve for public address without accessing AWS console

       curl -s http://169.254.169.254/latest/meta-data/public-ipv4
![Retrieve_IP_Address](./images/IP_Address.PNG)

### STEP 3 INSTALLING MYSQL
> Install a MySQL server

      sudo apt install mysql-server
![installing_mysql](./images/MySQL.PNG)
> Log into the server

      sudo mysql
![log_into_mysql](./images/Log_into_mysqlPNG.PNG)
> Set a password for the root user

        ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'PassWord.1';
![set_password_for_root](./images/Set_Password.PNG)
> Exit mysql

    mysql> exit

![exit_page](./images/Exit.PNG)

> Start an interactive script 

    sudo mysql_secure_installation
![installation](./images/Installation_Process.PNG)

>Log into mysql with password prompt

      sudo mysql -p

![Password_Prompt](./images/Password_Prompt.PNG)

> Exit mysql

     mysql> exit

### STEP 3 INSTALLING PHP
> Installation packages of PHP on NGINX

     sudo apt install php-fpm php-mysql
![phpinstallation](./images/PHP_NGINX.PNG)
>PHP successfully installed


### STEP 4 CONFIGURING NGINX TO USE PHP PROCESSOR
> Create root web directory for domain name projectLEMP

      sudo mkdir /var/www/projectLEMP
> Assign ownership of the directory to current system user

      sudo chown -R $USER:$USER /var/www/projectLEMP
> Open new configuration file in Nginx in nano text editor

       sudo nano /etc/nginx/sites-available/projectLEMP
![Nano_editor](./images/NANO_EDITOR.PNG)
> Paste the following

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
        fastcgi_pass unix:/var/run/php/php8.1-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}

Save and close the file
![bare_bones_configuration](./images/script.png)

> Activate configuration by linking config file  

    sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
> Test configuration for syntax errors

      sudo nginx -t
![syntax_error](./images/syntax.PNG)

> Disable default Nginx host 

    sudo unlink /etc/nginx/sites-enabled/default
> Reload Nginx to apply changes

      sudo systemctl reload nginx
> Create an index.html file to test the server block works

        sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

> Open website URL using IP address

    http://<Public-IP-Address>:80
![htmtl_file](./images/html_Nginx.PNG)

> Open website URL using DNS IP address

     http://<Public-DNS-Name>:80
![html_DNS](./images/DNS_Check.PNG)

### STEP 5 TESTING PHP WITH NGINX
> Validate Nginx can handle .php files

> Create a test php file in document root

    sudo nano /var/www/projectLEMP/info.php
![Nano_file](./images/Nano_Page.PNG)
> Paste the codes into the new file and save 

     <?php
     phpinfo();
![php_info_nano](./images/phpinfo.PNG)
> Access the page in web browser with domain name or IP address 

    http://`server_domain_or_IP`/info.php
![html_PHP](./images/Html_PHP.PNG)

> Remove the file due to it containing sensitive information

      sudo rm /var/projectLEMP/info.php


### STEP 6 RETRIEVING DATA FROM MYSQL DATABASE WITH PHP

> Connect to the MySQL console using the root account

    sudo mysql -p
![log_into_MySQL](./images/step%206.PNG)

> Create a database

    mysql> CREATE DATABASE 'example_database';
![database_creation](./images/Database%20name.PNG) 
> Create a user and authenticate password

     mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
![new_user](./images/New_user.PNG)
> Grant the user permission to the database

       mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';
![Grant_permission](./images/Permission_granted.PNG)
> Exit MySQL
> Confirm the new configuration by logging into mysql using the new user

     mysql -u example_user -p
![login_with_user](./images/UserPassword.PNG)
> Check access to database

    mysql> SHOW DATABASES;
![Show_database](./images/Database.PNG)

> Create a table named todo_list an input data

    CREATE TABLE example_database.todo_list (
    mysql>     item_id INT AUTO_INCREMENT,
    mysql>     content VARCHAR(255),
    mysql>     PRIMARY KEY(item_id)
    mysql> );
![table](./images/Table_Content.PNG)
> Insert a few rows of content into table

    mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
![Table_To_Do_List](./images/To_Do_List_Table.PNG)
> Confirm data has successfully saved

      mysql>  SELECT * FROM example_database.todo_list;
![table_content](./images/List.PNG)
> Exit

> Create a PHP file in web root directory 

    nano /var/www/projectLEMP/todo_list.php
![php_script](./images/Php_Script.PNG)
> Save and close file
> Access the page on a web browser

      http://<Public_domain_or_IP>/todo_list.php
![web_browser](./images/Web_Browser.PNG)


      