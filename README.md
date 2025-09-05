#!/bin/bash

# User data script to be used in conjunction with an AWS EC2 instance and Amazon Linux AMI to launch a fully functional web based wiki system. 

# Initial system update
yum update -y

# Install packages
yum install -y php php-xml php-gd wget

# Installing and starting Nginx  
yum install -y nginx
systemctl start nginx
systemctl enable nginx

# Download Docuwiki
cd /tmp
wget https://download.dokuwiki.org/src/dokuwiki/dokuwiki-stable.tgz

# Extract Docuwiki
tar -xzf dokuwiki-stable.tgz

# Move Docuwiki to web root
mv dokuwiki-*/* /var/www/html/
