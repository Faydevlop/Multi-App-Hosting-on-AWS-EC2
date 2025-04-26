# Hosting Multiple Projects on a Single Instance
## A Comprehensive Guide for Students

This guide will walk you through the process of deploying multiple web projects on a single AWS EC2 instance using industry-standard tools and best practices.

## 📋 Overview

Using a single server to host multiple projects is a cost-effective approach that's perfect for:
- Personal portfolios
- Course assignments
- Small startup projects
- Testing environments

## 🔧 Technologies We'll Use

- **AWS EC2** - Cloud-based virtual server
- **Ubuntu 22.04** - Server operating system
- **Node.js** - JavaScript runtime
- **PM2** - Process manager to keep apps running
- **Nginx** - Web server and reverse proxy
- **Let's Encrypt** - Free SSL certificates

## 🚀 Step-by-Step Installation Guide

### 1. Set Up Your EC2 Instance

1. Launch an EC2 instance with Ubuntu 22.04 LTS
2. Configure security groups to allow HTTP (80), HTTPS (443), and SSH (22)
3. Assign an Elastic IP for a static address
4. Configure your domain DNS to point to this IP

### 2. Initial Server Setup

Connect to your instance:
```bash
ssh -i your-key.pem ubuntu@your-instance-ip
```

Update the system:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y git htop wget curl
```

### 3. Install Node.js Using NVM

```bash
# Install NVM
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

# Load NVM
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"

# Install Node.js LTS
nvm install --lts

# Verify installation
node -v
npm -v
```

### 4. Install PM2 (Process Manager)

PM2 will keep your applications running even after server restarts:

```bash
npm install -g pm2
```

### 5. Clone and Configure Your Projects

For each project:

```bash
# Clone the repository
git clone https://github.com/yourusername/project.git
cd project

# Install dependencies
npm install

# Set up environment (if needed)
nano .env (copy past your env file here)
# Edit the .env file with your settings

# Start the application with PM2
pm2 start app.js --name project-name
```

**Important:** Make sure each project listens on a different port!
- Project 1: Port 3000
- Project 2: Port 4000
- Project 3: Port 5000

### 6. Set Up Nginx as a Reverse Proxy

Install Nginx:
```bash
sudo apt install -y nginx
```

Create configuration for your projects:
```bash
sudo nano /etc/nginx/sites-available/default
```

Add this configuration (customize for your domains):

```nginx
server {
    listen 80;
    server_name project1.yourdomain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

server {
    listen 80;
    server_name project2.yourdomain.com;

    location / {
        proxy_pass http://localhost:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

server {
    listen 80;
    server_name project3.yourdomain.com;

    location / {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Test and apply the configuration:
```bash
sudo nginx -t
sudo service nginx restart
```

### 7. Implement Free SSL with Let's Encrypt

```bash
# Install Certbot
sudo apt install -y certbot python3-certbot-nginx

# Generate certificates
sudo certbot --nginx -d project1.yourdomain.com -d project2.yourdomain.com -d project3.yourdomain.com

# Test auto-renewal
sudo certbot renew --dry-run
```

### 8. Configure PM2 to Start on Boot

```bash
pm2 startup
# Run the command that PM2 gives you
pm2 save
```

## 🔍 Monitoring and Maintenance

### Check Status of Your Applications
```bash
pm2 status
pm2 logs
pm2 logs app-name
```

### Nginx Commands
```bash
sudo nginx -t           # Test configuration
sudo service nginx restart
sudo service nginx status
```

### System Monitoring
```bash
htop                    # Interactive process viewer
df -h                   # Disk usage
```

## 🛠️ Troubleshooting

1. **Application not starting:** Check logs with `pm2 logs`
2. **Nginx not routing properly:** Verify config with `sudo nginx -t`
3. **SSL issues:** Run `sudo certbot certificates` to check certificate status
4. **Permission problems:** Make sure your application has the right permissions to access files

## 📚 Additional Resources

- [PM2 Documentation](https://pm2.keymetrics.io/docs/usage/quick-start/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)

## 🎓 Assignment

For practice, try to:
1. Deploy at least two different web applications on your EC2 instance
2. Set up subdomains for each project
3. Implement SSL for secure access
4. Configure automatic application restart on server reboot

Good luck with your projects!
