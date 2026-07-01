# AWS EC2 Deployment Documentation  
CyberPath — ICT171 Cloud Server Project  
Satnam Singh Rooprai - 35802393

------------------------------------------------------------------------------------------------

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

## Access the server remotely 

Use bash to login to the machine remotely
```
chmod 400 satnam-key.pem
ssh -i "your_key_name.pem" ubuntu@YOUR_ELASTIC_IP
```
3.1 Preparing the server
```
sudo apt update
sudo apt upgrade -y
```
3.2 Installing Apache2 Web Server
```
sudo apt install apache2 -y
```
3.3 Enable Apache on Boot
```
sudo systemctl enable apache2
```
3.4 Start Apache
```
sudo sytemctl start apache2
```
3.5 Verify Apache Status
```
systemctl status apache2
```
## 4. Configuring Firewall
4.1 Allow Apache Traffic
```
sudo ufw allow 'Apache'
```
4.2 Enable firewall
```
sudo ufw enable
```
4.3 check firewall status
```
sudo ufw status
```

## 5. Configuring Apache2 with website code 
5.1 Accessing the apache2 webpage
```
sudo nano /var/www/html/index.html
```
5.2 Delete the current code of web-server (optional)
```
sudo truncate -s 0 /var/www/html/index.html
```
5.3 Create a HTML file and add the code 
```
sudo nano CyberPath.html
```
5.4 moving the HTML file to apache2 web-server
```
mv CyberPath.html /var/www/html/index.html
```
5.5 Reload the server
```
sudo systmectl reload apache2
```
5.6 If the terminal throughs an error use this command to notify LINUX that a service file has been edited.
```
sudo systemctl deamon-reload
sudo systemctl reload apache2
```


