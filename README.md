# fluent-bit Installation Guide

fluent-bit is a free and open-source log processor. Fluent Bit provides fast and lightweight log processor and forwarder

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
  - RAM: 128MB minimum
  - Storage: 1GB for buffers
  - Network: Various inputs
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 24224 (default fluent-bit port)
  - HTTP on 2020
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

# Install fluent-bit
sudo dnf install -y fluent_bit

# Enable and start service
sudo systemctl enable --now fluent-bit

# Configure firewall
sudo firewall-cmd --permanent --add-port=24224/tcp
sudo firewall-cmd --reload

# Verify installation
fluent-bit --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install fluent-bit
sudo apt install -y fluent_bit

# Enable and start service
sudo systemctl enable --now fluent-bit

# Configure firewall
sudo ufw allow 24224

# Verify installation
fluent-bit --version
```

### Arch Linux

```bash
# Install fluent-bit
sudo pacman -S fluent_bit

# Enable and start service
sudo systemctl enable --now fluent-bit

# Verify installation
fluent-bit --version
```

### Alpine Linux

```bash
# Install fluent-bit
apk add --no-cache fluent_bit

# Enable and start service
rc-update add fluent-bit default
rc-service fluent-bit start

# Verify installation
fluent-bit --version
```

### openSUSE/SLES

```bash
# Install fluent-bit
sudo zypper install -y fluent_bit

# Enable and start service
sudo systemctl enable --now fluent-bit

# Configure firewall
sudo firewall-cmd --permanent --add-port=24224/tcp
sudo firewall-cmd --reload

# Verify installation
fluent-bit --version
```

### macOS

```bash
# Using Homebrew
brew install fluent_bit

# Start service
brew services start fluent_bit

# Verify installation
fluent-bit --version
```

### FreeBSD

```bash
# Using pkg
pkg install fluent_bit

# Enable in rc.conf
echo 'fluent-bit_enable="YES"' >> /etc/rc.conf

# Start service
service fluent-bit start

# Verify installation
fluent-bit --version
```

### Windows

```bash
# Using Chocolatey
choco install fluent_bit

# Or using Scoop
scoop install fluent_bit

# Verify installation
fluent-bit --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/fluent_bit

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
fluent-bit --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable fluent-bit

# Start service
sudo systemctl start fluent-bit

# Stop service
sudo systemctl stop fluent-bit

# Restart service
sudo systemctl restart fluent-bit

# Check status
sudo systemctl status fluent-bit

# View logs
sudo journalctl -u fluent-bit -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add fluent-bit default

# Start service
rc-service fluent-bit start

# Stop service
rc-service fluent-bit stop

# Restart service
rc-service fluent-bit restart

# Check status
rc-service fluent-bit status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'fluent-bit_enable="YES"' >> /etc/rc.conf

# Start service
service fluent-bit start

# Stop service
service fluent-bit stop

# Restart service
service fluent-bit restart

# Check status
service fluent-bit status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start fluent_bit
brew services stop fluent_bit
brew services restart fluent_bit

# Check status
brew services list | grep fluent_bit
```

### Windows Service Manager

```powershell
# Start service
net start fluent-bit

# Stop service
net stop fluent-bit

# Using PowerShell
Start-Service fluent-bit
Stop-Service fluent-bit
Restart-Service fluent-bit

# Check status
Get-Service fluent-bit
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream fluent_bit_backend {
    server 127.0.0.1:24224;
}

server {
    listen 80;
    server_name fluent_bit.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name fluent_bit.example.com;

    ssl_certificate /etc/ssl/certs/fluent_bit.example.com.crt;
    ssl_certificate_key /etc/ssl/private/fluent_bit.example.com.key;

    location / {
        proxy_pass http://fluent_bit_backend;
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
    ServerName fluent_bit.example.com
    Redirect permanent / https://fluent_bit.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName fluent_bit.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/fluent_bit.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/fluent_bit.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:24224/
    ProxyPassReverse / http://127.0.0.1:24224/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend fluent_bit_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/fluent_bit.pem
    redirect scheme https if !{ ssl_fc }
    default_backend fluent_bit_backend

backend fluent_bit_backend
    balance roundrobin
    server fluent_bit1 127.0.0.1:24224 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R fluent_bit:fluent_bit /etc/fluent_bit
sudo chmod 750 /etc/fluent_bit

# Configure firewall
sudo firewall-cmd --permanent --add-port=24224/tcp
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
sudo systemctl status fluent-bit

# View logs
sudo journalctl -u fluent-bit -f

# Monitor resource usage
top -p $(pgrep fluent_bit)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/fluent_bit"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/fluent_bit-backup-$DATE.tar.gz" /etc/fluent_bit /var/lib/fluent_bit

echo "Backup completed: $BACKUP_DIR/fluent_bit-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop fluent-bit

# Restore from backup
tar -xzf /backup/fluent_bit/fluent_bit-backup-*.tar.gz -C /

# Start service
sudo systemctl start fluent-bit
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u fluent-bit -n 100
sudo tail -f /var/log/fluent_bit/fluent_bit.log

# Check configuration
fluent-bit --version

# Check permissions
ls -la /etc/fluent_bit
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 24224

# Test connectivity
telnet localhost 24224

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep fluent_bit)

# Check disk I/O
iotop -p $(pgrep fluent_bit)

# Check connections
ss -an | grep 24224
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  fluent_bit:
    image: fluent_bit:latest
    ports:
      - "24224:24224"
    volumes:
      - ./config:/etc/fluent_bit
      - ./data:/var/lib/fluent_bit
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update fluent_bit

# Debian/Ubuntu
sudo apt update && sudo apt upgrade fluent_bit

# Arch Linux
sudo pacman -Syu fluent_bit

# Alpine Linux
apk update && apk upgrade fluent_bit

# openSUSE
sudo zypper update fluent_bit

# FreeBSD
pkg update && pkg upgrade fluent_bit

# Always backup before updates
tar -czf /backup/fluent_bit-pre-update-$(date +%Y%m%d).tar.gz /etc/fluent_bit

# Restart after updates
sudo systemctl restart fluent-bit
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/fluent_bit

# Clean old logs
find /var/log/fluent_bit -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/fluent_bit
```

## Additional Resources

- Official Documentation: https://docs.fluent_bit.org/
- GitHub Repository: https://github.com/fluent_bit/fluent_bit
- Community Forum: https://forum.fluent_bit.org/
- Best Practices Guide: https://docs.fluent_bit.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
