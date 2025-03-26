📚 Project Overview
This project demonstrates the deployment of a highly available WordPress website on AWS using a three-tier architecture. The infrastructure is designed for scalability, fault tolerance, and security by utilizing EC2 instances, RDS, EFS, VPC, NAT Gateway, and Application Load Balancer.

The GitHub repository contains:

Infrastructure diagram outlining the architecture.

Deployment scripts for configuring EC2 instances and launching WordPress.

Launch template script for auto-scaling EC2 instances.

🛠️ Architecture Components
1️⃣ Network Setup (VPC)
3-Tier VPC: Includes public and private subnets across two availability zones (AZs) for fault tolerance.

Subnets:

Public Subnets: For NAT Gateway and Application Load Balancer (ALB).

Private Subnets: For EC2 instances (app servers) and RDS (database).

Internet Gateway: Allows public-facing resources to communicate with the internet.

NAT Gateway: Enables private subnets to access the internet securely.

2️⃣ Compute & Storage
EC2 Instances: Hosts the WordPress application.

Elastic File System (EFS): Provides shared storage for WordPress files across multiple instances.

Amazon RDS: Stores the WordPress database securely in the private subnet.

3️⃣ Load Balancing & Scaling
Application Load Balancer (ALB): Distributes incoming traffic across multiple EC2 instances.

Auto Scaling Group (ASG): Ensures the website scales dynamically to handle traffic fluctuations.

4️⃣ Security
Security Groups: Protects EC2, RDS, and ALB with fine-grained access control.

SSL Certificate: Encrypts traffic between users and the website using AWS Certificate Manager.

Route 53 DNS: Manages the domain name and points it to the ALB.

⚙️ Installation & Deployment
✅ 1. Clone the Repository
bash
Copy
Edit
git clone <repository_url>
cd <repository_name>
✅ 2. Create AWS Resources
Follow the architecture diagram and create:

VPC: With public and private subnets.

Internet Gateway & NAT Gateway

Route Tables: Configure public and private route tables.

Security Groups

EFS & RDS Instances

✅ 3. Deploy WordPress on EC2
Use the provided script to configure WordPress on the EC2 instance:

bash
Copy
Edit
# Switch to root user
sudo su

# Update packages
sudo yum update -y

# Create HTML directory and mount EFS
sudo mkdir -p /var/www/html
EFS_DNS_NAME=<your-efs-dns>
sudo mount -t nfs4 -o nfsvers=4.1 "$EFS_DNS_NAME":/ /var/www/html

# Install Apache and PHP
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and required extensions
sudo dnf install -y php php-mysqlnd php-xml php-mbstring php-json php-intl php-zip php-bcmath php-ctype

# Install MySQL Server
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Download and configure WordPress
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php

# Restart Apache
sudo systemctl restart httpd
✅ 4. Launch Template Script
The script for the Launch Template installs all necessary services and mounts EFS:

bash
Copy
Edit
#!/bin/bash
# Update software packages
sudo yum update -y

# Install Apache and PHP
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and extensions
sudo dnf install -y php php-mysqlnd php-xml php-mbstring php-json php-intl php-zip php-bcmath php-ctype

# Install MySQL Server
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Mount EFS
EFS_DNS_NAME=<your-efs-dns>
echo "$EFS_DNS_NAME:/ /var/www/html nfs4 nfsvers=4.1 0 0" >> /etc/fstab
mount -a

# Set permissions and restart Apache
chown apache:apache -R /var/www/html
sudo systemctl restart httpd
🌐 AWS Services Used
EC2 Instances: For hosting WordPress.

Elastic Load Balancer (ALB): For distributing web traffic.

Auto Scaling Group: For high availability and scaling.

Amazon RDS: For the MySQL database.

Amazon EFS: For shared file storage.

Amazon Route 53: For DNS management.

AWS Certificate Manager: For SSL encryption.

🚀 How to Access the Website
Get the ALB DNS name from the AWS console.

Map the domain name using Route 53.

Visit the website using the domain or ALB DNS name.

Log in to WordPress Admin:

https://your-domain/wp-admin

Use the admin credentials you created during the MySQL setup.

🛠️ Project Structure
bash
Copy
Edit
/scripts  
 ├── install-wordpress.sh          # Script for installing WordPress on EC2  
 ├── launch-template.sh            # Script for EC2 launch template  
/diagrams  
 ├── architecture-diagram.png      # Reference diagram of the architecture  
/README.md                         # This file  
🚦 Usage & Contributing
Contribute: Feel free to fork the repo, suggest improvements, or submit pull requests.

Issues: If you encounter any problems, please submit an issue.

🔒 Security Considerations
Ensure your security groups restrict access only to necessary ports (e.g., 80, 443, 22).

Regularly update your EC2 instances to patch vulnerabilities.

Use IAM roles with the least privilege principle for EC2 instances.








