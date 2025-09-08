#!/bin/bash

# User data script to be used in conjunction with an AWS EC2 instance and Amazon Linux AMI to launch a fully functional web based wiki system. 

# Initial system update
yum update -y

# Install packages
yum install -y php php-xml php-gd wget php-fpm

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
mv dokuwiki-*/* /usr/share/nginx/html/

# Configure Docuwiki
server {
    listen 80;                          # Listen on port 80 (HTTP)
    root /usr/share/nginx/html;         # Where your files are located
    index index.php index.html;         # What files to serve by default
    
   
# Configuration
    location / {
        try_files $uri $uri/ @dokuwiki;
    }
    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

# Set 
