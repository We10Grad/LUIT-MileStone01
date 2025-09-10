# DokuWiki on AWS EC2 - Automated Deployment Script

This bash script automatically deploys a fully functional DokuWiki installation on an AWS EC2 instance running Amazon Linux. The script sets up a complete LEMP stack (Linux, Nginx, MySQL-less, PHP) optimized for DokuWiki.

## Features

- **Automated Installation**: One-script deployment of DokuWiki
- **Nginx Web Server**: High-performance web server configuration
- **PHP-FPM**: Fast and secure PHP processing
- **Security Optimized**: Proper file permissions and access restrictions
- **SELinux Compatible**: Automatic SELinux configuration when available
- **Production Ready**: Follows DokuWiki security best practices

## Prerequisites

- AWS EC2 instance running Amazon Linux (AL2 or AL2023)
- Instance should have internet access for package downloads
- Security group allowing HTTP traffic (port 80)
- Appropriate IAM permissions if using additional AWS services

## Quick Start

### Method 1: User Data Script (Recommended)
1. Launch a new EC2 instance
2. In the "Advanced Details" section, paste the entire script into the "User data" field
3. Launch the instance
4. Wait 5-10 minutes for the installation to complete
5. Access your wiki via `http://[your-instance-public-ip]`

### Method 2: Manual Execution
1. SSH into your EC2 instance
2. Upload the script file or copy its contents
3. Make the script executable: `chmod +x dokuwiki-install.sh`
4. Run as root: `sudo ./dokuwiki-install.sh`

## What the Script Does

1. **System Updates**: Updates all system packages to latest versions
2. **Package Installation**: Installs PHP, PHP extensions, Nginx, and PHP-FPM
3. **DokuWiki Download**: Downloads the latest stable DokuWiki release
4. **Web Server Setup**: Configures Nginx with DokuWiki-optimized settings
5. **File Permissions**: Sets proper ownership and permissions for security
6. **Service Configuration**: Starts and enables all required services
7. **SELinux Setup**: Configures SELinux policies if present

## Post-Installation Steps

### Initial Setup
1. Navigate to `http://[your-instance-public-ip]/install.php`
2. Complete the DokuWiki installation wizard
3. Create your admin account and configure basic settings
4. **Important**: After setup, uncomment the install.php restriction in `/etc/nginx/conf.d/dokuwiki.conf`:
   ```nginx
   location ~ /(install.php) { deny all; }
   ```
5. Restart Nginx: `sudo systemctl restart nginx`

### Security Recommendations
- Configure SSL/TLS certificate for HTTPS
- Set up regular backups of the `/usr/share/nginx/html/data` directory
- Consider implementing additional authentication layers
- Keep DokuWiki updated regularly

## File Structure

After installation, DokuWiki files are located at:
- **Web Root**: `/usr/share/nginx/html/`
- **Data Directory**: `/usr/share/nginx/html/data/`
- **Configuration**: `/usr/share/nginx/html/conf/`
- **Nginx Config**: `/etc/nginx/conf.d/dokuwiki.conf`

## Configuration Files

### Nginx Configuration
The script creates a production-ready Nginx configuration with:
- PHP-FPM upstream handler
- Security headers
- File upload limits (4MB)
- Static file caching
- URL rewriting for clean URLs
- Directory access restrictions

### PHP-FPM Configuration
Uses the default Amazon Linux PHP-FPM configuration with Unix socket communication.

## Troubleshooting

### Common Issues

**DokuWiki not accessible:**
- Check if services are running: `sudo systemctl status nginx php-fpm`
- Verify security group allows port 80
- Check logs: `sudo tail -f /var/log/nginx/error.log`

**Permission errors:**
- Verify ownership: `sudo chown -R nginx:nginx /usr/share/nginx/html/`
- Check data directory permissions: `sudo chmod -R 777 /usr/share/nginx/html/data/`

**PHP not working:**
- Ensure PHP-FPM is running: `sudo systemctl restart php-fpm`
- Check PHP-FPM logs: `sudo tail -f /var/log/php-fpm/www-error.log`

### Service Management

```bash
# Restart services
sudo systemctl restart nginx
sudo systemctl restart php-fpm

# Check service status
sudo systemctl status nginx
sudo systemctl status php-fpm

# View logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

## Customization

### Modify Upload Limits
Edit `/etc/nginx/conf.d/dokuwiki.conf` and change:
```nginx
client_max_body_size 4M;  # Increase as needed
```

### Add SSL/HTTPS
Consider using AWS Certificate Manager with an Application Load Balancer or configure Let's Encrypt directly on the instance.

### Backup Strategy
Create regular backups of:
- `/usr/share/nginx/html/data/` (wiki content)
- `/usr/share/nginx/html/conf/` (configuration)

## System Requirements

- **Instance Type**: t2.micro or larger (t3.micro recommended)
- **Storage**: 10GB minimum (20GB+ recommended for content growth)
- **Memory**: 1GB minimum
- **Network**: VPC with internet gateway for downloads

## Contributing

Feel free to submit issues, fork the repository, and create pull requests for improvements.

## License

This script is provided as-is under the MIT License. DokuWiki is released under the GPL license.

## Support

- [DokuWiki Documentation](https://www.dokuwiki.org/dokuwiki)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)

---

**Note**: This script is designed for Amazon Linux. For other distributions, package names and paths may need to be adjusted.
