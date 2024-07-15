# My-Dynamic-Web-App
**Objective:** Deploy multiple WordPress applications on an Amazon Linux server using Apache and secure them with Free SSL.
**Objective Description:** This repository contains scripts and configurations to deploy multiple WordPress applications on an Amazon Linux server using Apache, and to secure each site with Free SSL certificates for enhanced security.

## Key Features
- **Multi-site Deployment**: Setup and configure multiple WordPress sites on a single Amazon Linux server.
- **Apache Web Server**: Utilize Apache as the web server to host and serve WordPress applications.
- **Free SSL Integration**: Secure each WordPress site with Free SSL certificates (Let's Encrypt) to enable HTTPS encryption.
- **Automated Setup Scripts**: Scripts provided to automate the deployment and configuration process, ensuring ease of setup and maintenance.
- **Best Practices**: Follow best practices for server security and WordPress configuration to maintain a robust and secure environment.

## Prerequisites
- Amazon Linux 2023 EC2 instance with necessary permissions.
- Domain name and subdomains configured through a domain provider (e.g., (1&1) IONOS, GoDaddy, Domain.com, Google Domains, etc).
- Apache web server installed.

## AWS Setup Steps

### 1. Create a Virtual Private Cloud (VPC)

- Go to the AWS Management Console and navigate to the VPC dashboard.
- Click on "Create VPC" and provide the necessary details:
  - **VPC Name:** `WordPressVPC`
  - **VPC Purpose:** A dedicated virtual network environment in AWS to securely host multiple WordPress applications, ensuring isolation and controlled networking.
  - **IPv4 CIDR block:** `10.0.0.0/16`
- Click "Create".

### 2. Create Subnets

- In the VPC dashboard, navigate to "Subnets".
- Click on "Create subnet" and select the VPC `WordPressVPC`.

#### 2.1. Create Public Subnet

- Enter the details for a public subnet:
  - **Subnet Name:** `WordPressPublicSubnet`
  - **Subnet Purpose:** Hosts public-facing WordPress instances with direct internet access.
  - **Availability Zone (AZ) :** Select an availability zone (i.e. us-east-1a (N. Virginia))
  - **IPv4 CIDR block:** `10.0.1.0/24`
- Click "Create".

#### 2.2. Create Private Subnet

- Enter the details for a private subnet:
  - **Subnet Name:** `WordPressPrivateSubnet`
  - **Subnet Purpose:** Isolated subnet for backend WordPress components like databases, ensuring enhanced security.
  - **Availability Zone (AZ) :** Select an availability zone (i.e. us-east-1b (N.Virginia)
  - **IPv4 CIDR block:** `10.0.2.0/24`
- Click "Create".

### 3. Create an Internet Gateway 
- In the VPC dashboard, navigate to "Internet Gateways".
- Click on "Create Internet Gateway".
  - **Internet Gateway Name:** `WordPressIGW`.
  - **Internet Gateway Purpose:** Enables communication for instances in WordPressPublicSubnet with the internet.
- Select the newly created internet gateway and attach it to `WordPressVPC`.

### 4. Create Route Tables

#### 4.1. Create Public Subnet Route Table

- In the VPC dashboard, navigate to "Route Tables".
- Click on "Create Route Table".
  - **Route Table Name:** `WordPressRouteTable`.
  - **Route Table Purpose:** Route Table within WordPressVPC directing traffic, allowing WordPressPublicSubnet internet access via WordPressIGW while restricting access for 
    WordPressPrivateSubnet.
  - **VPC:** `WordPressVPC`
- Click "Create Route Table".
- Select the newly created `WordPressPublicRouteTable`.
- Click on the "Routes" tab.
- Click "Edit routes".
- Click "Add route".
  - **Destination:** `0.0.0.0/0`
  - **Target:** Select `WordPressIGW` (Internet Gateway)
- Click "Save routes".

#### 4.2. Associate Public Subnet with Route Table

- In the Route Table details page, click the "Subnet Associations" tab.
- Click "Edit subnet associations".
- Select the `WordPressPublicSubnet`.
- Click "Save associations".

#### 4.3. Create Private Subnet Route Table

- Click on "Create Route Table".
  - **Route Table Name:** `WordPressPrivateRouteTable`.
  - **VPC:** Select the `WordPressVPC` you created.
- Click "Create Route Table".
- No need to add a route to `0.0.0.0/0` since it's a private subnet.

#### 4.4. Associate Private Subnet with Route Table

- In the Route Table details page, click the "Subnet Associations" tab.
- Click "Edit subnet associations".
- Select the `WordPressPrivateSubnet`.
- Click "Save associations".

### 5. Create WordPressSecurityGroup

- In the EC2 dashboard, navigate to "Security Groups".
- Click on "Create security group" and provide:
  - **Security Group Name:** `WordPressSecurityGroup`
  - **Description:** Security group for WordPress EC2 instances.

#### 5.1. Inbound Rules for WordPressSecurityGroup
- Configure inbound and outbound rules as needed (e.g., allow SSH from your IP and HTTP/HTTPS).
  - **Type #1:** HTTP
    - **Port:** 80
    - **Source:** 0.0.0.0/0
    - **Purpose:** Allow HTTP traffic from the internet to the web servers.
  - **Type #2:** HTTPS
    - **Port:** 443
    - **Source:** 0.0.0.0/0
    - **Purpose:** Allow HTTPS traffic from the internet to the web servers.
  - **Type #3:** SSH
    - **Port:** 22
    - **Source:** Your IP address
    - **Purpose:** Allow SSH access from your IP address for management purposes.

### 6. Launch EC2 Instances in Public and Private Subnets

#### 6.1. Launch EC2 Instance in WordPressPublicSubnet

- **Purpose:** Deploy a web server instance to host WordPress.
- **Subnet:** Select `WordPressPublicSubnet`.
- **Security Group:** Assign `WordPressSecurityGroup`.
- **Key Pair:** `WordPressKeyPair`
  - Secure SSH access to EC2 instances using key pairs, ensuring secure management of WordPress servers deployed within AWS.
  - Use the downloaded `.pem` file to securely SSH into your EC2 instances.
 
#### 6.2. Configure EC2 Public IP to Your Domain

**1. Get the EC2 Public IP Address**
- You can find your EC2 Instance's Public IP address from the EC2 Dashboard in the AWS Management Console.

**2. Update DNS Settings in Your Domain Provider**
- Log in to your Domain Provider account.
- Go to the DNS settings for your domain.
- Create or update `A` records for your subdomains to point to your EC2 instance's public IP.

*For example, create A records like this:*
- `site1.mydynamicwebapp.com` -> 123.123.123.123 (Replace with your EC2 public IP)
- `site2.mydynamicwebapp.com` -> 123.123.123.123 (Replace with your EC2 public IP)

**3. Verify DNS Propagation**
Use a tool like WhatsMyDNS to check if your domain is correctly resolving to your EC2 public IP globally.

### 7. Access Your EC2 Instances

#### 1. Setup Amazon Linux Server
ssh -i /path/to/your-key.pem ec2-user@public-instance-ip

#### 2. Install Apache, MySQL, and PHP
**1. Update the package manager**
sudo yum update -y

**2. Install Apache**
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl enable httpd

**3. Install MySQL**
sudo yum install mariadb-server -y
sudo systemctl start mariadb
sudo systemctl enable mariadb

**4. Secure MySQL Installation**
sudo mysql_secure_installation

**5. Install PHP and PHP modules**
sudo yum install php php-mysqlnd php-fpm -y
sudo systemctl restart httpd

#### 3. Create MySQL Databases and Users for WordPress
**1. Log in to MySQL**
sudo mysql -u root -p

**2. Create Databases and Users**
CREATE DATABASE wordpress1;
CREATE USER 'user1'@'localhost' IDENTIFIED BY 'password1'; #Replace `password1` with your own password.
GRANT ALL PRIVILEGES ON wordpress1.* TO 'user1'@'localhost';
FLUSH PRIVILEGES;

CREATE DATABASE wordpress2;
CREATE USER 'user2'@'localhost' IDENTIFIED BY 'password2'; #Replace `password2` with your own password.
GRANT ALL PRIVILEGES ON wordpress2.* TO 'user2'@'localhost';
FLUSH PRIVILEGES;

#### 4. Download and Configure WordPress
**1. Download WordPress**
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz

**2. Copy WordPress Files to Appropriate Directories**
sudo cp -r wordpress /var/www/html/site1
sudo cp -r wordpress /var/www/html/site2

**3. Set Appropriate Permissions**
sudo chown -R apache:apache /var/www/html/site1
sudo chown -R apache:apache /var/www/html/site2
sudo chmod -R 755 /var/www/html/site1
sudo chmod -R 755 /var/www/html/site2

#### 5. Configure Apache Virtual Hosts
**1. Create Virtual Host Files**
sudo nano /etc/httpd/conf.d/site1.mydynamicwebapp.com.conf (Replace `site1.mydynamicwebapp.com.conf`)

*Add the following configuration*
<VirtualHost *:80>
    ServerName site1.mydynamicwebapp.com #Replace with `site1.mydynamicwebapp.com` your own domain.
    DocumentRoot /var/www/html/site1 
    <Directory /var/www/html/site1>
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog /var/log/httpd/site1-error.log
    CustomLog /var/log/httpd/site1-access.log combined
</VirtualHost>

**Repeat for site2**
sudo nano /etc/httpd/conf.d/site2.mydynamicwebapp.com.conf

*Add the following configuration*
<VirtualHost *:80>
    ServerName site2.mydynamicwebapp.com #Replace with `site2.mydynamicwebapp.com` your own domain.
    DocumentRoot /var/www/html/site2
    <Directory /var/www/html/site2>
        AllowOverride All
        Require all granted
    </Directory>
    ErrorLog /var/log/httpd/site2-error.log
    CustomLog /var/log/httpd/site2-access.log combined
</VirtualHost>

**2. Restart Apache**
sudo systemctl restart httpd

### 8. Install Certbot and Obtain SSL Certificates

**1. Enable the EPEL repository and install Certbot**
sudo amazon-linux-extras install epel -y
sudo yum install certbot python2-certbot-apache -y

**2. Obtain SSL Certificate for Both Sites**
sudo certbot --apache -d site1.mydynamicwebapp.com
sudo certbot --apache -d site2.mydynamicwebapp.com

**3. Verify Automatic Renewal**
sudo certbot renew --dry-run

### 9. Complete WordPress Setup

**1. Navigate to the respective URLs**

**2. Complete the WordPress Installation through the web interface by providing database details created earlier.**


### NOTE:
- In this README.md, I've specified that the domain and subdomains are configured through IONOS (1&1). Adjust the domain names and subdomains accordingly based on your actual setup.
