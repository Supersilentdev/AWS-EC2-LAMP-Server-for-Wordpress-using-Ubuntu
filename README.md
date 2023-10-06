# AWS-EC2-LAMP-Server-for-Wordpress-using-Ubuntu
Installing LAMP (Ubuntu Linux, Apache. MySql and PHP) on Ec2 Machine to Deploy Wordpress App.
For Amazon Linux 2 LAMP Server <a href="https://github.com/Supersilentdev/AWS-EC2-LAMP-Server-for-Wordpress-using-Amazon-Linux-2">click hear</a>.

**Sign Up or Log In to AWS**

   - Create an AWS <a href="https://aws.amazon.com/">free-tier</a> account if you don't already have one. If you do, simply <a href="https://aws.amazon.com">log in</a> to your existing account.

**Setting Up an EC2 Instance**

   - Navigate to the EC2 service within the AWS Console.
   - Ensure that the IAM user associated with your account has the necessary EC2 permissions to avoid encountering any errors.

**Selecting Region and Availability Zone**

   - Choose the specific AWS Region according to your project's requirements.

**Instance Configuration**

   - Under the "Instances" section, select "Launch Instances."
   - Provide a meaningful name for your instance and optionally, add tags for resource organization.
   - Select the Amazon Machine Image (AMI), choose "Ubuntu Server LTS."
   - Choose the instance type based on your server's requirements. (For the AWS free tier, options like "t2.micro" or "t3.micro" are suitable, depending on your region.)

**Key Pair Creation**

   - Create a new key pair. This key pair represents your public key and is required for SSH access to your EC2 machine. Ensure the utmost care in protecting this key. If you already have a key, you can choose to use it.

**Networking Configuration**

   - Select the Virtual Private Cloud (VPC) and Subnet. Amazon provides default VPC and Subnet options in each region, but you can also opt for custom configurations.
   - Assign an auto-assigned IP address.

**Security Group Configuration**

   - Create a new security group or use an existing one. Ensure that this security group has SSH, HTTP, and HTTPS ports open to the public (0.0.0.0).

**Storage Configuration**

   - Configure Elastic Block Store (EBS) volumes, specifying type and size (e.g., gp2/gp3 for free tier, depending on your region).
   - Optionally, encrypt the EBS volume with a Key Management Service (KMS) key, ensuring that you have the necessary KMS permissions.

**Advanced Configuration (User Data)** 

- Paste the following script into the user data section.
    
    ```bash
    #!/bin/bash
    sudo apt update -y
    sudo apt install apache2 mariadb-server php -y
    sudo apt install libapache2-mod-php php-mysql php-redis php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip -y
    sudo wget https://wordpress.org/latest.tar.gz
    sudo tar -xzvf latest.tar.gz
    sudo rm latest.tar.gz
    sudo mv wordpress /var/www/
    sudo chown -R www-data:www-data /var/www/wordpress
    sudo chmod -R 775 /var/www/wordpress
    sudo echo "<Directory /var/www/wordpress/> AllowOverride All </Directory>" > /etc/apache2/sites-available/wordpress.conf
    sudo systemctl enable apache2
    ```
- Launch the Instance.

**Setting Up SSH to Access Your Server**

   - Open your preferred SSH client.

   - Locate the private key file associated with your instance, which is typically in the format KEYXXX.pem.

   - Run the following command to secure your private key(change"KEYXXX.pem" with your key name in the command).

     ```bash
     chmod 0400 KEYXXX.pem
     ```
   - Use the following SSH command to access your server (change "KEYXXX.pem" with your KEY and "3-XXX-XX-235" with your server public IP address in the command).

     ```bash
     ssh -i KEYXXX.pem ubuntu@3-XXX-XX-235
     ```

**Creating a Database for WordPress**

   - Create an SQL User and Set Password**

     ```bash
     sudo mysql -u root -p
     ```

   - Replace 'wpuser' and 'wppassword' with a secure username and password in the command:

     ```sql
     CREATE DATABASE wordpress;
     CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'wppassword';
     GRANT ALL PRIVILEGES ON * . * TO 'wpuser'@'localhost';
     FLUSH PRIVILEGES;
     exit;
     ```

**Making Wordprees default**

   - Update the Apache2 configuration file to set WordPress as the default DocumentRoot:

     ```bash
     sudo nano /etc/apache2/sites-available/000-default.conf
     ```

   - Change:

     ```
     DocumentRoot /var/www/html
     ```

     to:

     ```
     DocumentRoot /var/www/wordpress/
     ```

   - Restart Apache2:

     ```bash
     sudo systemctl restart apache2
     ```

Done, Now Access Your Wordpress Installation & Set it up using Server Public IP Address.(Example: http://3-XXX-XX-235 use http not https)

**Installing SSL Certificate for WordPress**

1. **Adding Your Website's DNS Name to the Configuration File**

   - Edit the Apache2 configuration file:

     ```bash
     sudo nano /etc/apache2/sites-available/000-default.conf
     ```

   - Add your website's DNS name by pasting the below lines and replace 'myxxxxxxxx.com' with your DNS:

     ```
     ServerName myxxxxxx.com
     ServerAlias myxxxxxxxx.com
     ```

2. **Installing SSL/TLS Certificate Using Let's Encrypt Certbot**

   - Install Certbot and the Certbot Apache plugin:

     ```bash
     sudo apt install certbot python3-certbot-apache -y
     ```

   - Run Certbot to obtain and configure the SSL certificate:

     ```bash
     sudo certbot --apache
     ```

   - Follow the prompts to provide your email and select your DNS.

Congratulations! Your WordPress website is now set up securely with SSL/TLS encryption. Enjoy your website.
