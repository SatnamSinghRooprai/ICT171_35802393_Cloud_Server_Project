# AWS EC2 Deployment Documentation  
CyberPath - ICT171 Cloud Server Project  
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
  - SSH (22) - your_IP
  - HTTP (80) - 0.0.0.0/0
  - HTTPS (443) - 0.0.0.0/0

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
4.3 Allow traffic to HTTP(80) and SSH(22) port
```
sudo ufw allow 80/tcp
sudo ufw allow 22/tcp
```

4.4 check firewall status
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
5.3 Create a HTML file and add the code (you can access the code from [CyberPath.Code.HTML](https://github.com/SatnamSinghRooprai/ICT171_35802393_Cloud_Server_Project/blob/main/CyberPath.Code.html)
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
---

## 6. DNS Configuration
 
These steps connect the domain `cyberpath.space` (purchased via GoDaddy) to the EC2 server using AWS Route 53.
 
### 6.1 Purchase a Domain
 
Purchase your domain from a registrar of your choice. For this project, `cyberpath.space` was purchased via GoDaddy.
 
### 6.2 Create a Hosted Zone in AWS Route 53
 
1. In AWS Console, search **Route 53** → **Hosted Zones** → **Create hosted zone**
2. Fill in the following:
| Field | Value |
|---|---|
| Domain name | cyberpath.space |
| Type | Public hosted zone |
 
3. Click **Create hosted zone**
### 6.3 Create DNS Records
 
Click **Create record** and add the following two records:
 
**A Record — maps domain to IP address:**
 
| Record name | Record type | Value | TTL | Routing policy |
|---|---|---|---|---|
| cyberpath.space | A | 13.50.124.14 | 300 | Simple routing |
 
**CNAME Record — maps www to root domain:**
 
| Record name | Record type | Value | TTL | Routing policy |
|---|---|---|---|---|
| www | CNAME | cyberpath.space | 300 | Simple routing |
 
### 6.4 Update GoDaddy Nameservers to Point to AWS
 
Route 53 generates four nameservers for your hosted zone (e.g. `ns-1151.awsdns-15.org`). You must copy these into GoDaddy so all DNS lookups for `cyberpath.space` are handled by AWS.
 
1. In GoDaddy → **My Products** → **DNS** next to your domain
2. Click **Nameservers** → **Change Nameservers**
3. Select **Enter my own nameservers**
4. Copy each nameserver from Route 53 (excluding the trailing dot `.`)
5. Click **Save**
> **Note:** Nameserver propagation typically takes 10 minutes to 4 hours, and up to 48 hours in rare cases. The domain may show errors during this period - this is normal.
 
### 6.5 Configure Apache Virtual Host
 
```bash
sudo nano /etc/apache2/sites-available/000-default.conf
```
 
Add the following lines inside the `<VirtualHost *:80>` block:
 
```apache
ServerName cyberpath.space
ServerAlias www.cyberpath.space
```
 
### 6.6 Verify DNS Resolution
 
```bash
nslookup cyberpath.space
```
 
Expected output: returns `13.50.124.14`
 
### 6.7 Reload Apache
 
```bash
sudo systemctl reload apache2
```
 
The website is now accessible at `http://cyberpath.space`
 
---
 
## 7. SSL/TLS Configuration (Let's Encrypt)
 
SSL/TLS encrypts traffic between the user's browser and the server, enabling HTTPS. The certificate is obtained free from Let's Encrypt via Certbot.
 
### 7.1 Allow HTTPS Through the Firewall
 
```
sudo ufw allow 443/tcp
sudo ufw status
```
 
### 7.2 Install Certbot
 
```
sudo apt install certbot python3-certbot-apache -y
```
 
### 7.3 Obtain and Install SSL Certificate
 
```
sudo certbot --apache
```
 
When prompted:
- Enter your email address
- Agree to the Terms of Service
- Select both domain names (type `1,2` when asked which domains to cover)
Certbot automatically edits the Apache config to redirect HTTP to HTTPS and installs the certificate.
 
### 7.4 Verify the Certificate
 
```
sudo certbot certificates
```
 
Expected output:
 
```
Certificate Name: cyberpath.space
Domains: cyberpath.space www.cyberpath.space
Expiry Date: (89 days from issue date) (VALID)
Certificate Path: /etc/letsencrypt/live/cyberpath.space/cert.pem
```
 
You can also verify by visiting `https://cyberpath.space` in a browser and clicking the padlock icon → **Certificate is valid**.
 
### 7.5 Verify Auto-Renewal is Active
 
Let's Encrypt certificates expire every 90 days. Certbot sets up automatic renewal via a systemd timer.
 
```
sudo systemctl status certbot.timer
```
 
Expected output: `Active: active (waiting)`
 
If not active:
 
```
sudo systemctl enable certbot.timer
sudo systemctl start certbot.timer
```
 
---
 
## 8. Script Documentation
 
### 8.1 What the Script Does
 
The CyberPath quiz logic is implemented as a client-side JavaScript scoring algorithm embedded in `CyberPath.Code.html`. No server-side processing is required — the entire quiz runs in the user's browser.
 
The algorithm works as follows:
 
Each of the 10 quiz questions has four answer options. Each option is mapped to one or more of six personality traits: **Aggression**, **Vigilance**, **Curiosity**, **Systems Thinking**, **Communication**, and **Precision**. When a user selects an answer, the points associated with that option are added to a running total for each relevant trait.
 
After the user answers all 10 questions, the algorithm identifies the trait with the highest cumulative score. This dominant trait is then used as a key to look up the matching career profile from the `CAREERS` object, which contains the role description, responsibilities, roadmap, certifications, and free resources for that career.
 
The result is rendered dynamically into the page without any page reload — the quiz, scoring, and results display are all handled by the same JavaScript file.
 
### 8.2 Core Scoring Function (Commented)
 
```javascript
function finishQuiz() {
  // Reset all trait scores to zero before recalculating.
  // This allows the quiz to be retaken cleanly in the same session.
  Object.keys(scores).forEach(k => scores[k] = 0);
 
  // Loop through every question and add the points from the chosen
  // answer option to the running totals for each relevant trait.
  answers.forEach((choiceIndex, qIndex) => {
    Object.entries(QUESTIONS[qIndex].options[choiceIndex].points)
      .forEach(([trait, value]) => scores[trait] += value);
  });
 
  // Find the trait with the highest score.
  // This becomes the "winning" trait that determines the career result.
  let topTrait = 'aggression', topScore = -1;
  Object.entries(scores).forEach(([trait, value]) => {
    if (value > topScore) { topScore = value; topTrait = trait; }
  });
 
  // Hide the quiz, show the results screen,
  // and pass the winning trait to renderResults().
  progressShell.style.display = 'none';
  quizMain.classList.remove('active');
  renderResults(topTrait);
  resultsMain.style.display = 'block';
  window.scrollTo({ top: 0, behavior: 'smooth' });
}
```
 
### 8.3 Verifiable Output
 
The script output can be independently verified by visiting:
 
**https://cyberpath.space**
 
Complete the 10-question quiz to see the scoring algorithm produce a personalised career recommendation, roadmap, certifications, and free resources. The trait breakdown bar chart at the bottom of the results page visually confirms the scores assigned to each trait across all answers.
 
The full commented source code is available at:
https://github.com/SatnamSinghRooprai/ICT171_35802393_Cloud_Server_Project/blob/main/CyberPath.Code.html
 
---
 
## 9. Video Explainer
 
A full walkthrough video demonstrating the deployment of this project from start to finish is available at:
 
*(link will be added here)*
 
The video covers:
- EC2 instance setup and SSH access
- Apache installation and website deployment
- DNS configuration via Route 53 and GoDaddy
- SSL/TLS certificate installation via Certbot
- Live demonstration of the CyberPath quiz
---

