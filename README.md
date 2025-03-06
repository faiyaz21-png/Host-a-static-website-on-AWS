![Alt text](/Host-a-Static-Website-on-AWS.png)

---

# Host a Static Website on AWS  

## Overview  
I recently completed a DevOps project where I hosted a static HTML web application on AWS. The setup involved implementing a highly available and secure architecture using AWS services. Below, I outline the steps I followed to deploy the website successfully.  

## Architecture 

1. **Virtual Private Cloud** – Created a Virtual Private Cloud (VPC) with distinct public and private subnets across multiple availability zones.  
2. **Internet Gateway** – Attached an Internet Gateway (IGW) to the VPC to allow internet connectivity for public-facing components.  
3. **Security Group** – Configured Security Groups to control network traffic, ensuring only authorized access.  
4. **Availability Zones** – Distributed resources across multiple availability zones to ensure reliability and fault tolerance.  
5. **Public and Private Subnets** – Allocated subnets for infrastructure components like the NAT Gateway and Load Balancer, ensuring proper isolation and accessibility.  
6. **EC2 Instance Connect Endpoint (EICE)** – Enabled secure SSH access to EC2 instances without relying on public IP addresses.  
7. **Enhanced Security with Private Subnets** – Deployed web servers in private subnets for an added security layer.  
8. **NAT Gateway** – Configured a NAT Gateway in a public subnet to allow private instances to reach the internet securely.  
9. **EC2 Instances** – Deployed EC2 instances to host the website within the private subnet.  
10. **Load Balancing** – Used an Application Load Balancer (ALB) to evenly distribute traffic among EC2 instances within the Auto Scaling Group.  
11. **Automated Scaling** – Configured an Auto Scaling Group to manage server availability dynamically.  
12. **Integrated Version Control** – Stored website files in a GitHub repository to track changes and collaborate efficiently.  
13. **Certificate Manager** – Used AWS Certificate Manager to enable HTTPS encryption.  
14. **SNS** – Configured Amazon SNS to send notifications about key events in the Auto Scaling Group.  
15. **Route 53** – Used Route 53 for domain management and DNS configuration.  

## How I Built It  

I started by configuring **IAM permissions**, granting myself **administrator access** to manage AWS resources effectively. Next, I set up **AWS CLI on my Mac**—this part was challenging since some commands failed due to expired passkeys. After troubleshooting, I resolved it by **removing the old passkeys, generating new ones, and assigning them correctly.**  

With access configured, I created my **VPC** and named it **"Static VPC"** to align with the goal of hosting a static website. I then attached an **Internet Gateway (IGW)** to it, ensuring proper connectivity, while keeping in mind that each VPC is tied to a single IGW.  

I proceeded to **create multiple subnets**, being meticulous about naming them correctly to differentiate between **public and private subnets**, which played a crucial role later. Next, I configured a **route table** and explicitly associated it with the IGW, ensuring public subnets had internet access. Understanding that any subnet not explicitly linked to a custom route table defaults to the **main route table (which makes it private)** was key in properly setting up network isolation.  

To facilitate internet access for private instances, I configured a **NAT Gateway within a public subnet** and linked it to a **private route table**. I then associated the **private subnet with this private route table** to allow outbound internet traffic while keeping inbound traffic restricted.  

For security, I created separate **Security Groups** for my **Application Load Balancer (ALB)**, **Server**, and **EC2 Instance Connect (EICE)**. I paid special attention to **defining inbound and outbound rules**, ensuring only necessary traffic was permitted.  

I used **EC2 Instance Connect Endpoint (EICE)** to establish **SSH access** into the VPC-hosted EC2 instances. Testing EC2 connectivity via **AWS Management Console** helped refine my approach to managing **SSH configurations and security group settings**.  

Once the instance connectivity was verified, I proceeded with configuring the **Application Load Balancer (ALB)**. Before that, I created a **target group** and later assigned my EC2 instances to it. Deploying **servers within a private subnet** and linking them to the **target group** ensured traffic routing through the ALB.  

When launching the **EC2 instances** that hosted my application, I opted **not to assign a key pair**, as I relied entirely on **EICE for SSH access**. I carefully **assigned the appropriate security group** and linked the EC2 instances to the **target group** for the ALB.  

Finally, I created the **ALB**, ensuring it was **internet-facing** and deployed within the **public subnet**. I assigned the **ALB Security Group** to control access. With the infrastructure ready, I proceeded to deploy the website.  

### Deployment  

To deploy the website, I:  

1. **Prepared the installation script**, connected to the **GitHub repository**, and pulled the website files onto the EC2 instance.  
2. **Configured the DNS settings** for my website and assigned it the domain name **faiyazstatic21.com** using Route 53.  
3. **Set up Auto Scaling** to handle traffic spikes and **enabled SNS notifications** for monitoring.  
4. **Created this README** to document the entire process and make it easy for others to replicate.  

### Deployment Script  

Here’s the script I used to configure and deploy the web server:  

```bash
#!/bin/bash 
 
# Switch to the root user to gain full administrative privileges 
sudo su 
 
# Update all installed packages to their latest versions 
yum update -y 
 
# Install Apache HTTP Server 
yum install -y httpd 
 
# Change the current working directory to the Apache web root 
cd /var/www/html 
 
# Install Git 
yum install git -y 
 
# Clone the project GitHub repository to the current directory 
git clone https://github.com/faiyaz21-png/Host-a-static-website-on-AWS.git 
 
# Copy all files, including hidden ones, from the cloned repository to the Apache web root 
cp -R Host-a-static-website-on-AWS/. /var/www/html/ 
 
# Remove the cloned repository directory to clean up unnecessary files 
rm -rf Host-a-static-website-on-AWS 
 
# Enable the Apache HTTP Server to start automatically at system boot 
systemctl enable httpd  
 
# Start the Apache HTTP Server to serve web content 
systemctl start httpd  
```

### Key Takeaways  

- **Troubleshooting AWS CLI** was essential due to passkey issues.  
- **Subnets and route table associations** are crucial for public and private network configurations.  
- **Security Groups and ALB setup** play a vital role in managing access control.  
- **Using EC2 Instance Connect Endpoint (EICE)** simplifies SSH access to private instances.  
- **Proper Auto Scaling and SNS configuration** improve system resilience and monitoring.  

## GitHub Repository  

All reference diagrams and scripts are available in the repository:  
🔗 [GitHub Repo](https://github.com/faiyaz21-png/Host-a-static-website-on-AWS.git)  

---
