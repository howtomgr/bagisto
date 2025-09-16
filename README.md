# bagisto Installation Guide

bagisto is a free and open-source Laravel e-commerce. Bagisto provides Laravel-based e-commerce platform

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
  - Storage: 10GB for data
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default bagisto port)
  - None
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

# Install bagisto
sudo dnf install -y bagisto

# Enable and start service
sudo systemctl enable --now bagisto

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
bagisto --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install bagisto
sudo apt install -y bagisto

# Enable and start service
sudo systemctl enable --now bagisto

# Configure firewall
sudo ufw allow 80

# Verify installation
bagisto --version
```

### Arch Linux

```bash
# Install bagisto
sudo pacman -S bagisto

# Enable and start service
sudo systemctl enable --now bagisto

# Verify installation
bagisto --version
```

### Alpine Linux

```bash
# Install bagisto
apk add --no-cache bagisto

# Enable and start service
rc-update add bagisto default
rc-service bagisto start

# Verify installation
bagisto --version
```

### openSUSE/SLES

```bash
# Install bagisto
sudo zypper install -y bagisto

# Enable and start service
sudo systemctl enable --now bagisto

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
bagisto --version
```

### macOS

```bash
# Using Homebrew
brew install bagisto

# Start service
brew services start bagisto

# Verify installation
bagisto --version
```

### FreeBSD

```bash
# Using pkg
pkg install bagisto

# Enable in rc.conf
echo 'bagisto_enable="YES"' >> /etc/rc.conf

# Start service
service bagisto start

# Verify installation
bagisto --version
```

### Windows

```bash
# Using Chocolatey
choco install bagisto

# Or using Scoop
scoop install bagisto

# Verify installation
bagisto --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/bagisto

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
bagisto --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable bagisto

# Start service
sudo systemctl start bagisto

# Stop service
sudo systemctl stop bagisto

# Restart service
sudo systemctl restart bagisto

# Check status
sudo systemctl status bagisto

# View logs
sudo journalctl -u bagisto -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add bagisto default

# Start service
rc-service bagisto start

# Stop service
rc-service bagisto stop

# Restart service
rc-service bagisto restart

# Check status
rc-service bagisto status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'bagisto_enable="YES"' >> /etc/rc.conf

# Start service
service bagisto start

# Stop service
service bagisto stop

# Restart service
service bagisto restart

# Check status
service bagisto status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start bagisto
brew services stop bagisto
brew services restart bagisto

# Check status
brew services list | grep bagisto
```

### Windows Service Manager

```powershell
# Start service
net start bagisto

# Stop service
net stop bagisto

# Using PowerShell
Start-Service bagisto
Stop-Service bagisto
Restart-Service bagisto

# Check status
Get-Service bagisto
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream bagisto_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name bagisto.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name bagisto.example.com;

    ssl_certificate /etc/ssl/certs/bagisto.example.com.crt;
    ssl_certificate_key /etc/ssl/private/bagisto.example.com.key;

    location / {
        proxy_pass http://bagisto_backend;
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
    ServerName bagisto.example.com
    Redirect permanent / https://bagisto.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName bagisto.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/bagisto.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/bagisto.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend bagisto_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/bagisto.pem
    redirect scheme https if !{ ssl_fc }
    default_backend bagisto_backend

backend bagisto_backend
    balance roundrobin
    server bagisto1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R bagisto:bagisto /etc/bagisto
sudo chmod 750 /etc/bagisto

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status bagisto

# View logs
sudo journalctl -u bagisto -f

# Monitor resource usage
top -p $(pgrep bagisto)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/bagisto"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/bagisto-backup-$DATE.tar.gz" /etc/bagisto /var/lib/bagisto

echo "Backup completed: $BACKUP_DIR/bagisto-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop bagisto

# Restore from backup
tar -xzf /backup/bagisto/bagisto-backup-*.tar.gz -C /

# Start service
sudo systemctl start bagisto
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u bagisto -n 100
sudo tail -f /var/log/bagisto/bagisto.log

# Check configuration
bagisto --version

# Check permissions
ls -la /etc/bagisto
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep bagisto)

# Check disk I/O
iotop -p $(pgrep bagisto)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  bagisto:
    image: bagisto:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/bagisto
      - ./data:/var/lib/bagisto
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update bagisto

# Debian/Ubuntu
sudo apt update && sudo apt upgrade bagisto

# Arch Linux
sudo pacman -Syu bagisto

# Alpine Linux
apk update && apk upgrade bagisto

# openSUSE
sudo zypper update bagisto

# FreeBSD
pkg update && pkg upgrade bagisto

# Always backup before updates
tar -czf /backup/bagisto-pre-update-$(date +%Y%m%d).tar.gz /etc/bagisto

# Restart after updates
sudo systemctl restart bagisto
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/bagisto

# Clean old logs
find /var/log/bagisto -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/bagisto
```

## Additional Resources

- Official Documentation: https://docs.bagisto.org/
- GitHub Repository: https://github.com/bagisto/bagisto
- Community Forum: https://forum.bagisto.org/
- Best Practices Guide: https://docs.bagisto.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
