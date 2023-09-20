
# LEMP Stack and Wordpress Setup Script

This script automates the setup of a LEMP (Linux, Nginx, MySQL, PHP) stack on an Amazon Linux instance. It is intended for use on Amazon EC2 instances running the Amazon Linux operating system. The script installs and configures the necessary software components for hosting a WordPress website.

### Step 1: System Update
The script begins by updating the system packages using sudo yum update -y.

    sudo yum update -y

### Step 2: Installing Software
MariaDB is installed using sudo yum install mariadb-server.x86_64 -y.

NGINX is installed using sudo amazon-linux-extras install nginx1.

PHP 7.2 is installed using sudo amazon-linux-extras install php7.2.
    
    sudo yum install mariadb-server.x86_64 -y
    sudo amazon-linux-extras install nginx1
    sudo amazon-linux-extras install php7.2

### Step 3: Starting Services
The script starts the following services:

1. MariaDB: sudo systemctl enable mariadb and sudo systemctl start mariadb.

2. NGINX: sudo systemctl enable nginx and sudo systemctl start nginx.

3. PHP-FPM: sudo systemctl enable php-fpm and sudo systemctl start php-fpm.
             
       sudo systemctl enable mariadb 
       sudo systemctl start mariadb 
       sudo systemctl enable nginx 
       sudo systemctl start nginx 
       sudo systemctl enable php-fpm 
       sudo systemctl start php-fpm 

### Step 4: Configuration

1. PHP-FPM configuration is modified to use the nginx user and group instead of apache using sudo sed.

2. The /var/www/ directory is created and ownership is set to the ec2-user.

3. WordPress is downloaded, extracted, and placed in the /var/www/ directory.

4. Permissions are granted to the nginx user to use the /var/www/ directory.

5. MariaDB is configured with an initial root password and security settings.

       sudo sed -i 's/user = apache/user = nginx/' /etc/php-fpm.d/www.conf
       sudo sed -i 's/group = apache/group = nginx/' /etc/php-fpm.d/www.conf

       sudo mkdir /var/www/
       sudo chown ec2-user:ec2-user /var/www/
       cd /var/www/
       wget http://wordpress.org/latest.tar.gz
       tar -xvf latest.tar.gz
       rm latest.tar.gz
       cd /var/

       sudo chown -R nginx:nginx /var/www/
       sudo mysql_install_db

### Step 5: NGINX Configuration
A basic NGINX server block (vhost) configuration is created for hosting WordPress. The configuration specifies the server_name, root directory, and other settings.
    
    cd /tmp/
    sudo echo "server {
    listen 80 default_server;
    listen [::]:80 default_server;
    server_name wordpress;
    root /var/www/wordpress;
    index index.php;
    # Load configuration files for the default server block.
    include /etc/nginx/default.d/*.conf;
    }" > wordpress.conf
    sudo chown root:root wordpress.conf
    sudo chmod 644 wordpress.conf
    sudo mv wordpress.conf /etc/nginx/conf.d/

### Step 6: Additional NGINX Configuration (Optional)
Additional NGINX configuration settings can be added as needed. The provided script contains commented-out sections that can be uncommented and modified.

### Step 7: Restart Services
All services are restarted to apply the configuration changes.
    
    sudo service nginx restart
    sudo service php-fpm restart
    sudo service mariadb restart

### Security Considerations

#### Hardcoded Credentials:
 This script uses hardcoded credentials for the MariaDB root user and WordPress database user. It is essential to change these credentials for security purposes.

#### Production Use:
 This script is intended for demonstration or development environments. For production use, consider additional security measures and best practices.

### Disclaimer
This script provides a basic setup for a LAMP stack with WordPress. Ensure you adapt it to your specific requirements and follow best practices for security and performance when deploying in a production environment.