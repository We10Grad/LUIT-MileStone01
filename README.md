DokuWiki on AWS EC2 - Complete Deployment Script
This bash script automatically deploys a fully functional DokuWiki installation on an AWS EC2 instance running Amazon Linux. The script creates a complete LEMP stack (Linux, Nginx, MySQL-less, PHP) with pre-populated educational content about AWS services and Linux commands.
Features

Automated Installation: Complete one-script deployment of DokuWiki
Nginx Web Server: Optimized configuration for DokuWiki
PHP-FPM: Fast and secure PHP processing
Pre-loaded Content: Ready-to-use wiki pages with educational material
Security Optimized: Proper file permissions and access restrictions
Production Ready: Follows DokuWiki security best practices

What Gets Installed
Core Components

DokuWiki: Latest stable release
Nginx: High-performance web server
PHP 8.x: With required extensions (XML, GD)
PHP-FPM: FastCGI Process Manager

Pre-created Wiki Pages

Start Page: Welcome message and site overview
AWS Notes: Comprehensive descriptions of IAM, EC2, and S3 services
Linux Commands: Essential CLI commands with descriptions

Prerequisites

AWS EC2 instance running Amazon Linux (AL2 or AL2023)
Instance with internet access for package downloads
Security group allowing HTTP traffic (port 80)
At least 1GB RAM and 10GB storage recommended

Quick Start
Method 1: User Data Script (Recommended)

Launch a new EC2 instance
In the "Advanced Details" section, paste the entire script into the "User data" field
Launch the instance
Wait 5-10 minutes for installation to complete
Access your wiki at http://[your-instance-public-ip]

Method 2: Manual Execution

SSH into your EC2 instance
Create the script file: nano dokuwiki-install.sh
Paste the script content and save
Make executable: chmod +x dokuwiki-install.sh
Run as root: sudo ./dokuwiki-install.sh

Installation Process
The script performs these steps automatically:

System Update: Updates all packages to latest versions
Package Installation: Installs PHP, extensions, and PHP-FPM
Service Setup: Starts and enables PHP-FPM
Nginx Installation: Installs and starts Nginx web server
DokuWiki Download: Downloads latest stable DokuWiki release
File Extraction: Extracts DokuWiki directly to web root
Directory Creation: Creates required data and configuration directories
Permission Setting: Sets proper ownership and permissions
Nginx Configuration: Creates DokuWiki-optimized server configuration
Service Restart: Restarts Nginx to load new configuration
Content Creation: Creates initial wiki pages with educational content

Post-Installation
Immediate Access

Navigate to http://[your-instance-public-ip]
You'll see the welcome page immediately
Access other pages via:

/aws_notes - AWS service descriptions
/linux_commands - Linux CLI reference



Optional: Complete Setup Wizard

Visit http://[your-instance-public-ip]/install.php for advanced configuration
Create admin account and customize settings
Security: After setup, manually edit /etc/nginx/conf.d/dokuwiki.conf to uncomment:
nginxlocation ~ /(install.php) { deny all; }

Restart Nginx: sudo systemctl restart nginx

File Structure
Key Directories

Web Root: /usr/share/nginx/html/
Wiki Pages: /usr/share/nginx/html/data/pages/
Configuration: /usr/share/nginx/html/conf/
Media Files: /usr/share/nginx/html/data/media/
Nginx Config: /etc/nginx/conf.d/dokuwiki.conf

Pre-created Content

data/pages/start.txt - Main welcome page
data/pages/aws_notes.txt - AWS services reference
data/pages/linux_commands.txt - Linux commands guide

Configuration Details
Nginx Configuration

PHP-FPM Integration: Unix socket communication
Security Headers: Server tokens disabled
File Upload Limit: 4MB maximum
Static Caching: 365-day browser caching for assets
URL Rewriting: Clean URLs without .php extensions
Access Restrictions: Protects sensitive directories and files

Security Features

Blocks access to .htaccess, version control, and sensitive files
Restricts access to data/, conf/, and other internal directories
Proper file ownership (nginx:nginx)
Appropriate permissions (755 for code, 777 for data)

Troubleshooting
Common Issues
Wiki not accessible:
bash# Check services
sudo systemctl status nginx php-fpm

# Check logs
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/nginx/access.log
Pages not displaying:

Verify files exist: ls -la /usr/share/nginx/html/data/pages/
Check permissions: sudo chown -R nginx:nginx /usr/share/nginx/html/

PHP errors:
bash# Restart PHP-FPM
sudo systemctl restart php-fpm

# Check PHP-FPM status
sudo systemctl status php-fpm
Service Management
bash# Restart services
sudo systemctl restart nginx php-fpm

# View service status
sudo systemctl status nginx php-fpm

# Check if services start on boot
sudo systemctl is-enabled nginx php-fpm
Customization
Adding More Content
Create additional wiki pages:
bashcat > /usr/share/nginx/html/data/pages/newpage.txt << 'EOF'
Your content here
EOF
sudo chown nginx:nginx /usr/share/nginx/html/data/pages/newpage.txt
Modifying Upload Limits
Edit /etc/nginx/conf.d/dokuwiki.conf:
nginxclient_max_body_size 10M;  # Increase as needed
Restart nginx: sudo systemctl restart nginx
Backup Strategy
Important directories to backup:

/usr/share/nginx/html/data/ - All wiki content
/usr/share/nginx/html/conf/ - Configuration files

System Requirements
Minimum Requirements

Instance Type: t2.micro (1 vCPU, 1GB RAM)
Storage: 10GB EBS volume
Network: VPC with internet gateway

Recommended for Production

Instance Type: t3.small or larger
Storage: 20GB+ EBS volume
Backup: Regular snapshots of EBS volume
Security: HTTPS with SSL certificate

Educational Content Included
AWS Services Overview
Detailed explanations of:

AWS IAM: Identity and Access Management
Amazon EC2: Elastic Compute Cloud
Amazon S3: Simple Storage Service

Linux Commands Reference
Essential commands covered:

Navigation: cd, pwd, ls
File operations: cp, mv, mkdir
And more with practical descriptions

Security Considerations

Change default DokuWiki admin credentials after setup
Consider implementing HTTPS with Let's Encrypt or AWS Certificate Manager
Keep DokuWiki updated regularly
Monitor access logs for unusual activity
Implement regular backups

Contributing
This script is designed for educational purposes and basic deployments. For production use, consider:

Adding SSL/HTTPS configuration
Implementing automated backups
Adding monitoring and alerting
Using Application Load Balancer for high availability

Support Resources

DokuWiki Documentation
Nginx Documentation
AWS EC2 Documentation
Amazon Linux User Guide

License
This deployment script is provided as-is under the MIT License. DokuWiki is released under the GPL license.

Note: This script is optimized for Amazon Linux. Other Linux distributions may require package name and path modifications.
