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

# Configure Nginx for PHP (using official DokuWiki config)
cat > /etc/nginx/conf.d/dokuwiki.conf << 'EOF'
 
# PHP Handler
upstream php-handler {
    server 127.0.0.1:9000;                    # Distro independent
}
 
# Redirect SSL/443 
server {
    server_name default_server;
    listen           80;            # IPv4
    listen      [::]:80;            # IPv6
    return      301 https://$server_name$request_uri;
}
 
 
 
# Actual configuration
server {
 
# BASICS 
    server_name default_server;     # optional: your domain name
    root        /var/www/dokuwiki;
    index       doku.php;
 
 
# SSL version 
    listen           443 ssl;     # IPv4
    listen      [::]:443 ssl;     # IPv6
    http2       on;               # if supported, optional
 
    ssl_certificate     /etc/ssl/certs/***.crt;
    ssl_certificate_key /etc/ssl/private/***.key;
 
# HEADERS 
    # Information "leaks"
    server_tokens       off;
    fastcgi_hide_header X-Powered_by;
 
# TWEAKS 
 
    client_max_body_size 4M;          # Maximum file upload size
    client_body_buffer_size 128k;
 
    location ~ ^/lib.*\.(js|css|gif|png|ico|jpg|jpeg|svg)$ {
        expires 365d;   # browser caching
    }
 
 
# RESTRICT ACCESS 
    # Reference: https://www.dokuwiki.org/security#deny_directory_access_in_nginx
                 # TODO: Compare with this
 
 
    # Comment out while installing, then uncomment
    location ~ /(install.php) { deny all; }
 
 
    # .ht             - .htaccess, .htpasswd, .htdigest, .htanything
    # .git, .hg, .svn - Git, Mercurial, Subversion.
    # .vs             - Visual Studio (Code)
    # All directories except lib.
    # All "other" files that you don't want to delete, but don't want public.
 
    location ~ /(\.ht|\.git|\.hg|\.svn|\.vs|data|conf|bin|inc|vendor|README|VERSION|SECURITY.md|COPYING|composer.json|composer.lock) {
        #return 404; # https://www.dokuwiki.org/install:nginx?rev=1734102057#nginx_particulars
        deny all;    # Returns 403
    }

 
# REDIRECT & PHP    
 
    location / {
        try_files $uri $uri/ @dokuwiki;
 
        # This means; where $uri is 'path', if 'GET /path' doesnt exist, redirect
        # client to 'GET /path/' directory. If neither, goto @dokuwiki rules.
    }
 
    location @dokuwiki {
        rewrite ^/_media/(.*) /lib/exe/fetch.php?media=$1 last;
        rewrite ^/_detail/(.*) /lib/exe/detail.php?media=$1 last;
        rewrite ^/_export/([^/]+)/(.*) /doku.php?do=export_$1&id=$2 last;
        rewrite ^/(.*) /doku.php?id=$1&$args last;
 
        # rewrites "doku.php/" out of the URLs if you set the userewrite
        # setting to .htaccess in dokuwiki config page
    }
 
    location ~ \.php$ {
        try_files $uri $uri/ /doku.php;
 
        include       fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param REDIRECT_STATUS 200;
        # fastcgi_param HTTPS on;     # optional ?! TODO
 
        fastcgi_pass php-handler;
    }
}
EOF
