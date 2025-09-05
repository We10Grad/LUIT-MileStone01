#!/bin/bash

# User data script to be used in conjunction with an AWS EC2 instance and Amazon Linux AMI to launch a fully functional web based wiki system. 

# Initial server update and installing Nginx 
yum update -y
yum install -y nginx
echo "<h1>Proof the first test worked, now on to bigger challenges!</h1>" > /usr/share/nginx/html/index.html
systemctl start nginx
systemctl enable nginx
