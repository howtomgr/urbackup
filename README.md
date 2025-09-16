# urbackup Installation Guide

urbackup is a free and open-source client/server backup. UrBackup provides easy-to-use backup system with continuous backups

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
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 100GB for backups
  - Network: Client connections
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 55414 (default urbackup port)
  - Web UI on 55415
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

# Install urbackup
sudo dnf install -y urbackup

# Enable and start service
sudo systemctl enable --now urbackup

# Configure firewall
sudo firewall-cmd --permanent --add-port=55414/tcp
sudo firewall-cmd --reload

# Verify installation
urbackup --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install urbackup
sudo apt install -y urbackup

# Enable and start service
sudo systemctl enable --now urbackup

# Configure firewall
sudo ufw allow 55414

# Verify installation
urbackup --version
```

### Arch Linux

```bash
# Install urbackup
sudo pacman -S urbackup

# Enable and start service
sudo systemctl enable --now urbackup

# Verify installation
urbackup --version
```

### Alpine Linux

```bash
# Install urbackup
apk add --no-cache urbackup

# Enable and start service
rc-update add urbackup default
rc-service urbackup start

# Verify installation
urbackup --version
```

### openSUSE/SLES

```bash
# Install urbackup
sudo zypper install -y urbackup

# Enable and start service
sudo systemctl enable --now urbackup

# Configure firewall
sudo firewall-cmd --permanent --add-port=55414/tcp
sudo firewall-cmd --reload

# Verify installation
urbackup --version
```

### macOS

```bash
# Using Homebrew
brew install urbackup

# Start service
brew services start urbackup

# Verify installation
urbackup --version
```

### FreeBSD

```bash
# Using pkg
pkg install urbackup

# Enable in rc.conf
echo 'urbackup_enable="YES"' >> /etc/rc.conf

# Start service
service urbackup start

# Verify installation
urbackup --version
```

### Windows

```bash
# Using Chocolatey
choco install urbackup

# Or using Scoop
scoop install urbackup

# Verify installation
urbackup --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/urbackup

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
urbackup --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable urbackup

# Start service
sudo systemctl start urbackup

# Stop service
sudo systemctl stop urbackup

# Restart service
sudo systemctl restart urbackup

# Check status
sudo systemctl status urbackup

# View logs
sudo journalctl -u urbackup -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add urbackup default

# Start service
rc-service urbackup start

# Stop service
rc-service urbackup stop

# Restart service
rc-service urbackup restart

# Check status
rc-service urbackup status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'urbackup_enable="YES"' >> /etc/rc.conf

# Start service
service urbackup start

# Stop service
service urbackup stop

# Restart service
service urbackup restart

# Check status
service urbackup status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start urbackup
brew services stop urbackup
brew services restart urbackup

# Check status
brew services list | grep urbackup
```

### Windows Service Manager

```powershell
# Start service
net start urbackup

# Stop service
net stop urbackup

# Using PowerShell
Start-Service urbackup
Stop-Service urbackup
Restart-Service urbackup

# Check status
Get-Service urbackup
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream urbackup_backend {
    server 127.0.0.1:55414;
}

server {
    listen 80;
    server_name urbackup.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name urbackup.example.com;

    ssl_certificate /etc/ssl/certs/urbackup.example.com.crt;
    ssl_certificate_key /etc/ssl/private/urbackup.example.com.key;

    location / {
        proxy_pass http://urbackup_backend;
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
    ServerName urbackup.example.com
    Redirect permanent / https://urbackup.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName urbackup.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/urbackup.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/urbackup.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:55414/
    ProxyPassReverse / http://127.0.0.1:55414/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend urbackup_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/urbackup.pem
    redirect scheme https if !{ ssl_fc }
    default_backend urbackup_backend

backend urbackup_backend
    balance roundrobin
    server urbackup1 127.0.0.1:55414 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R urbackup:urbackup /etc/urbackup
sudo chmod 750 /etc/urbackup

# Configure firewall
sudo firewall-cmd --permanent --add-port=55414/tcp
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
sudo systemctl status urbackup

# View logs
sudo journalctl -u urbackup -f

# Monitor resource usage
top -p $(pgrep urbackup)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/urbackup"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/urbackup-backup-$DATE.tar.gz" /etc/urbackup /var/lib/urbackup

echo "Backup completed: $BACKUP_DIR/urbackup-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop urbackup

# Restore from backup
tar -xzf /backup/urbackup/urbackup-backup-*.tar.gz -C /

# Start service
sudo systemctl start urbackup
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u urbackup -n 100
sudo tail -f /var/log/urbackup/urbackup.log

# Check configuration
urbackup --version

# Check permissions
ls -la /etc/urbackup
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 55414

# Test connectivity
telnet localhost 55414

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep urbackup)

# Check disk I/O
iotop -p $(pgrep urbackup)

# Check connections
ss -an | grep 55414
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  urbackup:
    image: urbackup:latest
    ports:
      - "55414:55414"
    volumes:
      - ./config:/etc/urbackup
      - ./data:/var/lib/urbackup
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update urbackup

# Debian/Ubuntu
sudo apt update && sudo apt upgrade urbackup

# Arch Linux
sudo pacman -Syu urbackup

# Alpine Linux
apk update && apk upgrade urbackup

# openSUSE
sudo zypper update urbackup

# FreeBSD
pkg update && pkg upgrade urbackup

# Always backup before updates
tar -czf /backup/urbackup-pre-update-$(date +%Y%m%d).tar.gz /etc/urbackup

# Restart after updates
sudo systemctl restart urbackup
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/urbackup

# Clean old logs
find /var/log/urbackup -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/urbackup
```

## Additional Resources

- Official Documentation: https://docs.urbackup.org/
- GitHub Repository: https://github.com/urbackup/urbackup
- Community Forum: https://forum.urbackup.org/
- Best Practices Guide: https://docs.urbackup.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
