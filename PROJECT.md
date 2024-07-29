# My-Dynamic-Web-App

## AWS Setup Steps

### 1. Create a Virtual Private Cloud (VPC)
- **Virtual Private Cloud (VPC) Name:** `mydynamicwebapp-VPC`
![image](https://github.com/user-attachments/assets/e01c3420-4a40-4669-9c6e-324da0fb8b94)


### 2. Create Subnets

#### 2.1. Create Public Subnet
- **Public Subnet Name:** `mydynamicwebapp-PublicSubnet`
![image](https://github.com/user-attachments/assets/5332543a-4aa2-4633-bb02-db898a7a9cdf)

#### 2.2. Create Private Subnet
- **Public Subnet Name:** `mydynamicwebapp-PrivateSubnet`
![image](https://github.com/user-attachments/assets/b648b4f0-c2b4-4c26-a1cc-2b91fd4d5bde)


### 3. Create an Internet Gateway (IGW)
- **Internet Gateway Name:** `mydynamicwebapp-IGW`
![image](https://github.com/user-attachments/assets/71c82794-e3c8-4b31-b869-81a053710d2c)


### 4. Create Route Tables

#### 4.1. Create Public Subnet Route Table
- **Route Table Name:** `mydynamicwebapp-RouteTable`
![image](https://github.com/user-attachments/assets/db73da70-1a42-48f7-a4c5-0a140d99d28f)


### 5. Create Security Group (SG)
- **Security Group Name:** `mydynamicwebapp-SG`
![image](https://github.com/user-attachments/assets/44f87670-bc0d-456c-baef-70c3cb8314dc)

### 6. Launch EC2 Instances in Public and Private Subnets

#### 6.1. Launch EC2 Instance in WordPressPublicSubnet
- **EC2 Instance Name:** `mydynamicwebapp-EC2`
- **Key Pair:** `mydynamicwebapp-keypair`
![image](https://github.com/user-attachments/assets/6a7505c2-ba3e-46e7-aeb9-6dc53d9eae7f)

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
![image](https://github.com/user-attachments/assets/a80b2a40-ce0b-4e7b-bedc-cf24a63ea114)

**Repeat for site2**
![image](https://github.com/user-attachments/assets/c8bc93cc-d598-4344-9d62-cbbb6ebc2336)

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

#### Site1
**WordPress < Setup Configuration**
![image](https://github.com/user-attachments/assets/09547921-39d1-4f98-970d-db1ad4f962a4)
![image](https://github.com/user-attachments/assets/09d3a525-c104-4843-96cb-141e9a434fb4)
![image](https://github.com/user-attachments/assets/7424e2a9-755e-4d63-bcf0-12c353ff5fcb)

**WordPress < Installation**
![image](https://github.com/user-attachments/assets/83f2853f-e220-49a5-9050-e8318a0aa62b)
![image](https://github.com/user-attachments/assets/ee82fc65-1731-4565-b8c8-8573769d19f1)

**WordPress Log In < My Dynamic Web App Site 1**
![image](https://github.com/user-attachments/assets/5c111aba-466e-40ea-a187-7328434242bb)

**WordPress Dashboard < My Dynamic Web App Site 1**
![image](https://github.com/user-attachments/assets/06b940a5-6cca-47c2-bd2c-d01d10d26e53)

**My Dynamic Web App Site 1 Homepage**
![image](https://github.com/user-attachments/assets/de734977-faca-4883-ba33-ce3e9731450a)

#### Site2
**WordPress < Setup Configuration**
![image](https://github.com/user-attachments/assets/3f53b094-4263-4937-89b8-ddc2bee3478c)
![image](https://github.com/user-attachments/assets/800fbd19-90cf-4a6b-a5df-78469270efc9)
![image](https://github.com/user-attachments/assets/ac80f65d-6a77-42aa-8115-4ae8f0b82ebc)

**WordPress < Installation**
![image](https://github.com/user-attachments/assets/82f860c7-ec82-4593-89d9-a9af15a0b1c5)
![image](https://github.com/user-attachments/assets/57036592-c93b-4e45-839b-060ccf0b3bad)

**WordPress Log In < My Dynamic Web App Site 2**
![image](https://github.com/user-attachments/assets/0a548c81-f8bf-42b6-a6bc-96ca24aba291)

**WordPress Dashboard < My Dynamic Web App Site 2**
![image](https://github.com/user-attachments/assets/fba067a3-a0e2-4317-8290-233f9baaa459)

**My Dynamic Web App Site 2 Homepage**
![image](https://github.com/user-attachments/assets/edccce9d-c80a-4bf2-bfef-f092a7554bae)

### NOTE:
- For the naming of the items, make them unique to your project.
