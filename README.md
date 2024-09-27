# Host-a-dynamic-web-app-on-aws

## Description
This project demonstrates how to build a scalable, secure, and highly available dynamic website using various AWS services. The website is hosted on EC2 instances within a private subnet for enhanced security, with public-facing components like a Load Balancer and NAT Gateway to manage traffic and provide secure connectivity. This project integrates best practices for high availability, fault tolerance, and security.

The website architecture includes resources like VPCs, EC2 instances, Security Groups, Auto Scaling Groups, and more. The project also provides automated notifications for system events and uses S3 for storing application code.

---

## Table of Contents
1. [Architecture](#architecture)
2. [Technologies Used](#technologies-used)
3. [Problem Solved](#problem-solved)
4. [Challenges Faced & Solutions](#challenges-faced--solutions)
5. [Setup and Installation](#setup-and-installation)
6. [Usage](#usage)
7. [Deployment](#deployment)
8. [Contributing](#contributing)
9. [License](#license)

---

## Architecture
The infrastructure for the dynamic website includes the following AWS services:
- **VPC** with both public and private subnets across two availability zones.
- **Internet Gateway** for allowing the VPC instances to connect to the internet.
- **Security Groups** as a firewall mechanism to control inbound and outbound traffic.
- **EC2 Instances** hosted in private subnets to serve the dynamic website.
- **Application Load Balancer (ALB)** to distribute traffic across EC2 instances in multiple availability zones.
- **NAT Gateway** to allow instances in private subnets to access the internet.
- **Auto Scaling Group (ASG)** to dynamically manage the EC2 instances for scalability and fault tolerance.
- **Amazon Certificate Manager (ACM)** to secure website communications via SSL.
- **Simple Notification Service (SNS)** to provide notifications about changes in the Auto Scaling Group.
- **S3** for storing the application code and assets.
- **Route 53** for domain registration and DNS management.

![Architecture Diagram](link-to-diagram)

---

## Technologies Used
- **AWS EC2**: Web hosting with security enhancements.
- **AWS VPC**: Custom network environment with public/private subnets.
- **Amazon S3**: Storage for web application code.
- **Amazon Route 53**: DNS management and domain registration.
- **AWS Application Load Balancer (ALB)**: Load balancing across EC2 instances.
- **AWS Auto Scaling Group (ASG)**: Automatic scaling of EC2 instances.
- **AWS Certificate Manager (ACM)**: SSL certificate management.
- **AWS SNS**: Alerts and notifications for system changes.
- **Apache**: Web server on EC2 instances.
- **MySQL**: Relational database for the dynamic content.
- **PHP**: Server-side language for dynamic web pages.
- **Bash**: Scripting for automated deployment.

---

## Problem Solved
The goal of this project was to create a secure, scalable, and highly available dynamic website using a cloud-native infrastructure. The architecture leverages AWS to solve common problems such as:

- **Security**: EC2 instances are placed in private subnets with an Application Load Balancer in the public subnets, minimizing exposure to direct internet traffic.
- **Scalability**: The Auto Scaling Group ensures the website can handle varying amounts of traffic by adding or removing EC2 instances as needed.
- **High Availability**: Utilizing two Availability Zones and the Application Load Balancer ensures that the website remains available even if one Availability Zone fails.
- **Automated Monitoring**: With SNS integration, administrators are notified in real time about system changes or potential issues, ensuring rapid response to scaling events.

---

## Challenges Faced & Solutions
### 1. **Issue**: Difficulty in connecting securely to the EC2 instances in private subnets.
   **Solution**: Implemented EC2 Instance Connect Endpoint, allowing secure access to instances within both public and private subnets. This reduced the need for using bastion hosts or exposing SSH ports publicly.

### 2. **Issue**: Ensuring secure communication between the website and the end-users.
   **Solution**: Configured AWS Certificate Manager (ACM) to issue and manage SSL certificates for the Application Load Balancer. This enabled HTTPS communication, enhancing the security of the website.

### 3. **Issue**: Web servers in private subnets needed internet access for software updates.
   **Solution**: Deployed a NAT Gateway in the public subnet to allow outbound internet access for instances in private subnets, while keeping inbound traffic controlled and secure.

### 4. **Issue**: Dynamic scaling of EC2 instances in response to traffic surges.
   **Solution**: Used an Auto Scaling Group that adjusts the number of EC2 instances based on traffic demand, ensuring the website remains available and responsive during high traffic periods.

---

## Setup and Installation

### Prerequisites
- AWS CLI installed and configured.
- EC2 Instance with required IAM roles and policies.
- Amazon Route 53 domain registered.
- S3 bucket for hosting the application files.

### Installation
1. **Clone the Repository:**
   ```bash
   git clone https://github.com/yourusername/yourproject.git
   cd yourproject
   ```

2. **Install EC2 and MySQL Setup Script:**
   Upload the following script to the EC2 instance via SSH or SSM.

   ```bash
   #!/bin/bash
   sudo yum update -y
   sudo yum install -y httpd
   sudo systemctl enable httpd 
   sudo systemctl start httpd
   sudo dnf install -y php mysql-community-server
   sudo systemctl start mysqld
   sudo systemctl enable mysqld
   sudo aws s3 sync s3://dynamic-web-files /var/www/html
   ```

3. **Edit the .env File:**
   Provide your database credentials in the `.env` file after copying the application code to `/var/www/html`.

4. **Start the Web Application:**
   After the configuration, restart the Apache server.
   ```bash
   sudo service httpd restart
   ```

---

## Usage
- **Domain Name**: Access the website using the domain registered via Route 53.
- **Auto Scaling**: The website will automatically scale to handle traffic spikes based on the Auto Scaling Group configuration.
- **Secure Communication**: Communication is encrypted via HTTPS using a certificate from AWS Certificate Manager.

---

## Deployment
1. **Prepare Infrastructure**: Launch the resources (VPC, Subnets, Load Balancer, etc.) using AWS CloudFormation or Terraform (optional).
2. **Deploy Application**: Sync the application code from the S3 bucket to the EC2 instances.
3. **Enable Auto Scaling**: Set up the Auto Scaling Group to ensure high availability and scalability.

---

## Contributing
Contributions are welcome! Please fork the repository, create a feature branch, and submit a pull request.

---

## License
This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
