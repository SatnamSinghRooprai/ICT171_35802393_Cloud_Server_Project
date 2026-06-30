# AWS EC2 Deployment Documentation  
CyberPath — ICT171 Cloud Server Project  
Satnam Singh Rooprai - 35802393

---------------------------------------------------------

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

------------------------------------------------------------

## 2. EC2 Instance Creation

### 2.1 Launch EC2 Instance
- AWS Service: EC2  
- AMI: Ubuntu Server 24.04 LTS  
- Instance type: t3.micro (Free Tier)  
- Storage: 8–10 GB  
- Key pair: RSA (`.pem` file)  
- Security group rules:
  - SSH (22) — My IP  
  - HTTP (80) — Anywhere  
  - HTTPS (443) — Anywhere  

----------------------------------------------------------

## 3. Connect to EC2 via SSH

```bash
chmod 400 satnam-key.pem
ssh -i "satnam-key.pem" ubuntu@YOUR_ELASTIC_IP
