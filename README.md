# pelican Installation Guide

pelican is a free and open-source Python static generator. Pelican provides static site generator powered by Python

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for sites
  - Network: Build tool
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default pelican port)
  - Dev server on 8000
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install pelican
sudo dnf install -y pelican

# Enable and start service
sudo systemctl enable --now pelican

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
pelican --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install pelican
sudo apt install -y pelican

# Enable and start service
sudo systemctl enable --now pelican

# Configure firewall
sudo ufw allow N/A

# Verify installation
pelican --version
```

### Arch Linux

```bash
# Install pelican
sudo pacman -S pelican

# Enable and start service
sudo systemctl enable --now pelican

# Verify installation
pelican --version
```

### Alpine Linux

```bash
# Install pelican
apk add --no-cache pelican

# Enable and start service
rc-update add pelican default
rc-service pelican start

# Verify installation
pelican --version
```

### openSUSE/SLES

```bash
# Install pelican
sudo zypper install -y pelican

# Enable and start service
sudo systemctl enable --now pelican

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
pelican --version
```

### macOS

```bash
# Using Homebrew
brew install pelican

# Start service
brew services start pelican

# Verify installation
pelican --version
```

### FreeBSD

```bash
# Using pkg
pkg install pelican

# Enable in rc.conf
echo 'pelican_enable="YES"' >> /etc/rc.conf

# Start service
service pelican start

# Verify installation
pelican --version
```

### Windows

```bash
# Using Chocolatey
choco install pelican

# Or using Scoop
scoop install pelican

# Verify installation
pelican --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/pelican

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
pelican --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable pelican

# Start service
sudo systemctl start pelican

# Stop service
sudo systemctl stop pelican

# Restart service
sudo systemctl restart pelican

# Check status
sudo systemctl status pelican

# View logs
sudo journalctl -u pelican -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add pelican default

# Start service
rc-service pelican start

# Stop service
rc-service pelican stop

# Restart service
rc-service pelican restart

# Check status
rc-service pelican status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'pelican_enable="YES"' >> /etc/rc.conf

# Start service
service pelican start

# Stop service
service pelican stop

# Restart service
service pelican restart

# Check status
service pelican status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start pelican
brew services stop pelican
brew services restart pelican

# Check status
brew services list | grep pelican
```

### Windows Service Manager

```powershell
# Start service
net start pelican

# Stop service
net stop pelican

# Using PowerShell
Start-Service pelican
Stop-Service pelican
Restart-Service pelican

# Check status
Get-Service pelican
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream pelican_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name pelican.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name pelican.example.com;

    ssl_certificate /etc/ssl/certs/pelican.example.com.crt;
    ssl_certificate_key /etc/ssl/private/pelican.example.com.key;

    location / {
        proxy_pass http://pelican_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName pelican.example.com
    Redirect permanent / https://pelican.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName pelican.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/pelican.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/pelican.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend pelican_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/pelican.pem
    redirect scheme https if !{ ssl_fc }
    default_backend pelican_backend

backend pelican_backend
    balance roundrobin
    server pelican1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R pelican:pelican /etc/pelican
sudo chmod 750 /etc/pelican

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status pelican

# View logs
sudo journalctl -u pelican -f

# Monitor resource usage
top -p $(pgrep pelican)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/pelican"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/pelican-backup-$DATE.tar.gz" /etc/pelican /var/lib/pelican

echo "Backup completed: $BACKUP_DIR/pelican-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop pelican

# Restore from backup
tar -xzf /backup/pelican/pelican-backup-*.tar.gz -C /

# Start service
sudo systemctl start pelican
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u pelican -n 100
sudo tail -f /var/log/pelican/pelican.log

# Check configuration
pelican --version

# Check permissions
ls -la /etc/pelican
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep pelican)

# Check disk I/O
iotop -p $(pgrep pelican)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  pelican:
    image: pelican:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/pelican
      - ./data:/var/lib/pelican
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update pelican

# Debian/Ubuntu
sudo apt update && sudo apt upgrade pelican

# Arch Linux
sudo pacman -Syu pelican

# Alpine Linux
apk update && apk upgrade pelican

# openSUSE
sudo zypper update pelican

# FreeBSD
pkg update && pkg upgrade pelican

# Always backup before updates
tar -czf /backup/pelican-pre-update-$(date +%Y%m%d).tar.gz /etc/pelican

# Restart after updates
sudo systemctl restart pelican
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/pelican

# Clean old logs
find /var/log/pelican -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/pelican
```

## Additional Resources

- Official Documentation: https://docs.pelican.org/
- GitHub Repository: https://github.com/pelican/pelican
- Community Forum: https://forum.pelican.org/
- Best Practices Guide: https://docs.pelican.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
