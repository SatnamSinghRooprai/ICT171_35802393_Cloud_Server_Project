# AWS EC2 Deployment Documentation  
CyberPath — ICT171 Cloud Server Project  
Satnam Singh Rooprai - 35802393

---------------------------------------------------------------------------------------------------------------------

## 1. Overview
This document explains how the CyberPath Quiz website (HTML, CSS, JavaScript) was deployed on an AWS EC2 Ubuntu 24.04 server using Apache2.  
It includes:

- EC2 instance creation  
- SSH access  
- Apache installation  
- Uploading website files  
- Elastic IP attachment  
- DNS setup (GoDaddy → EC2)  
- SSL/TLS setup (Let’s Encrypt)  

This documentation allows any ICT171 student to rebuild the server from scratch.

------------------------------------------------------------------------------------------------

## 2. EC2 Instance Creation
Follow the exact steps to deploy the AWS EC2 Cloud Server

### 2.1 Launch EC2 Instance
- Create an AWS EC2 instance by considering the following options: 
- AMI: Ubuntu Server 24.04 LTS  
- Instance type: t3.micro (Free Tier)  
- Storage: 8–10 GB  
- Key pair: RSA (`.pem` file)  
- Security group rules:
  - SSH (22) — your_IP
  - HTTP (80) — 0.0.0.0/0
  - HTTPS (443) — 0.0.0.0/0

Setup Elastic IP:
- Under the Network & Security tab select Elastic IPs
- Click on the instance created recently
- Enter the Public IP address mentioned on the instance information tab and click confirm.

------------------------------------------------------------------------------------------------

## 3. Connect to EC2 instance on AWS
- Click on the connect button present on the top of the instance panel on AWS. Once you  enter the Virtual Machine terminal follow the below given commands to successfully setup this server. 

```Preparing the server
sudo apt update
sudo apt upgrade -y

```Installing Apache2 Web Server
sudo apt install apache2 -y

Enable Apache on Boot
sudo systemctl enable apache2

```Start Apache
sudo sytemctl start apache2

```Verify Apache Status
systemctl status apache2

```




## Access the server remotely 

```Use bash to login to the machine remotely
chmod 400 satnam-key.pem
ssh -i "your_key_name.pem" ubuntu@YOUR_ELASTIC_IP
