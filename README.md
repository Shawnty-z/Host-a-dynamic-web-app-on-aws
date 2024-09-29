# Host-a-dynamic-web-app-on-aws

## Project Overview

This project involves hosting a dynamic website on AWS using multiple AWS services and resources such as EC2, RDS, S3, Route 53, and more. The project was designed to demonstrate the full lifecycle of deploying a web application in a scalable and secure cloud environment. Below is the architecture used for this deployment:

![Alt text](/Host_a_Dynamic_Web_App_on_AWS.jpg)

## Architecture Breakdown

1. **Amazon Route 53**: Used for DNS routing.
2. **VPC with Public and Private Subnets**: Segregating web and database layers for enhanced security.
3. **NAT Gateway**: To allow EC2 instances in private subnets to access the internet securely.
4. **EC2 Instances**: Web servers hosted in an auto-scaling group.
5. **Application Load Balancer**: Ensuring high availability and distributing incoming traffic across multiple EC2 instances.
6. **Amazon RDS**: MySQL relational database deployed in private subnets, with a standby instance for failover.
7. **Amazon S3**: Hosting static assets such as media files and serving as a backup storage location.
8. **EC2 Instance Connect Endpoint**: Used for secure access to private EC2 instances.
9. **IAM Roles**: To manage access and permissions to resources.

## Problem Solved

The project demonstrates the complete workflow of deploying a dynamic web application in AWS. It solves key challenges such as:

- **Scalability**: Using an Auto Scaling Group ensures that the web application can handle variable loads by scaling in and out based on demand.
- **High Availability**: Leveraging multiple Availability Zones for both the web and database tiers ensures that the application remains highly available, even if one zone experiences issues.
- **Security**: By separating web servers into public subnets and databases into private subnets, we mitigate potential attack vectors. Additionally, security groups and IAM roles tightly control access to resources.
- **Automation**: Using a Bash script to automate the installation of necessary software and synchronization of files from S3 ensures that the deployment is repeatable and reduces manual intervention.

## Bash Deployment Script

```bash
#!/bin/bash

# Update server packages
sudo yum update -y

# Install Apache Web Server and start it
sudo yum install -y httpd
sudo systemctl enable httpd 
sudo systemctl start httpd

# Install PHP and necessary extensions
sudo dnf install -y \
php \
php-pdo \
php-openssl \
php-mbstring \
php-exif \
php-fileinfo \
php-xml \
php-ctype \
php-json \
php-tokenizer \
php-curl \
php-cli \
php-fpm \
php-mysqlnd \
php-bcmath \
php-gd \
php-cgi \
php-gettext \
php-intl \
php-zip

# Install MySQL 8
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm 
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server 
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Enable mod_rewrite for Apache
sudo sed -i '/<Directory "\/var\/www\/html">/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/httpd/conf/httpd.conf

# Define S3 bucket name and sync the application code
S3_BUCKET_NAME=dynamic-web-files
sudo aws s3 sync s3://"$S3_BUCKET_NAME" /var/www/html

# Navigate to web server's root directory and unzip application code
cd /var/www/html
sudo unzip shopwise.zip
sudo cp -R shopwise/. /var/www/html/
sudo rm -rf shopwise shopwise.zip

# Set permissions for web files and restart Apache
sudo chmod -R 777 /var/www/html
sudo chmod -R 777 storage/
sudo service httpd restart
```

## Issues Faced and Solutions

### 1. **Issue: Installing MySQL 8 on EC2 instance**
   - During the setup, I encountered difficulties installing the correct version of MySQL, specifically version 8.
   - **Solution**: To solve this, I used the `wget` command to download and install the MySQL community repository, which enabled me to install the correct version of MySQL and start the database service.

### 2. **Issue: Permissions Problem with Apache**
   - After uploading the application files to the EC2 instance, I ran into permission issues that prevented Apache from accessing certain files.
   - **Solution**: The issue was resolved by setting the correct file permissions using the `chmod -R 777 /var/www/html` command for the web directory and restarting the Apache service.

### 3. **Issue: Automating deployment with S3 Sync**
   - Synchronizing the web application files from S3 to the EC2 instance posed a challenge initially, especially with file extraction and permission settings.
   - **Solution**: I automated the entire process by incorporating `aws s3 sync` in the deployment script, followed by unzipping and setting correct permissions for the web files.

## Key Takeaways

- The project successfully demonstrated the power of AWS services in deploying a dynamic, scalable, and secure web application.
- Automation using Bash scripting helped streamline the setup and configuration process, reducing the chances of manual errors.
- Incorporating AWS best practices such as multi-AZ deployments, secure VPC setups, and least privilege access via IAM enhanced the robustness and security of the system.

## Future Improvements

- **CI/CD Pipeline**: Implementing a CI/CD pipeline using AWS CodePipeline or Jenkins for automatic deployment on code changes.
- **Monitoring**: Integrating CloudWatch for enhanced monitoring and alerting on system performance and errors.
- **Backup & Restore**: Setting up automatic backups for the RDS instance for data durability.

## Conclusion

This project provided a hands-on experience in designing, deploying, and managing a web application on AWS. The challenges faced helped strengthen my understanding of various AWS services and how they can be used to solve real-world problems.
