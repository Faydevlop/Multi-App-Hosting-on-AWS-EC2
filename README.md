from pathlib import Path

readme_content = """
# 🟢 Node.js Multi-App Hosting on AWS EC2 with Free SSL & Nginx

This guide explains how to **deploy multiple Node.js projects** on a single AWS EC2 (Ubuntu 22.04) instance using **PM2**, **Nginx reverse proxy**, and **free SSL from Let's Encrypt**.

---

## 🚀 Installation Steps

### 1. Launch Your EC2 Ubuntu Server
- Choose **Ubuntu 22.04 LTS**.
- Assign a **static Elastic IP** to the instance.
- Make sure the Security Group allows **HTTP (80)**, **HTTPS (443)**, and custom **TCP ports** (e.g., 3000, 4000, 5000).

---

### 2. SSH Into the Instance

```bash
ssh -i <your-key.pem> ubuntu@<your-ec2-ip>
###3. Update Server & Install Essentials
bash
Always show details

Copy
sudo apt update && sudo apt upgrade -y
sudo apt install -y git htop wget curl
4. Install Node.js (Using NVM)
4.1 Install NVM (Node Version Manager)
bash
Always show details

Copy
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
4.2 Load NVM into Shell
bash
Always show details

Copy
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \\. "$NVM_DIR/bash_completion"
4.3 Install Node.js
bash
Always show details

Copy
nvm install --lts
4.4 Verify Installation
bash
Always show details

Copy
node -v
npm -v
5. Clone Your Projects
bash
Always show details

Copy
git clone https://github.com/yourusername/project1.git
git clone https://github.com/yourusername/project2.git
git clone https://github.com/yourusername/project3.git
Repeat the following steps inside each project folder:

bash
Always show details

Copy
cd project1
npm install
cp .env.example .env   # if applicable
pm2 start app.js --name project1
Make sure each app listens on a unique port (e.g., 3000, 4000, 5000).

6. Install and Configure Nginx
bash
Always show details

Copy
sudo apt install nginx
Edit the default Nginx config file:

bash
Always show details

Copy
sudo nano /etc/nginx/sites-available/default
Paste the following config (customize your domains and ports):

nginx
Always show details

Copy
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
Test and restart Nginx:

bash
Always show details

Copy
sudo nginx -t
sudo service nginx restart
7. Install Free SSL with Certbot (Let's Encrypt)
bash
Always show details

Copy
sudo apt install certbot python3-certbot-nginx
Generate certificates for your subdomains:

bash
Always show details

Copy
sudo certbot --nginx -d project1.yourdomain.com -d project2.yourdomain.com -d project3.yourdomain.com
Enable auto-renewal:

bash
Always show details

Copy
sudo certbot renew --dry-run
✅ Final Checklist
✅ Each Node.js project is running on a different port

✅ PM2 keeps apps alive and restarts them on crash

✅ Nginx routes each subdomain to the correct port

✅ SSL is active via Let's Encrypt

🛠 Useful Commands
bash
Always show details

Copy
pm2 status         # View running apps
pm2 restart all    # Restart all apps
pm2 logs           # View logs
sudo nginx -t      # Check nginx config syntax
sudo service nginx restart
🙌 Done!
Now your apps are live at:

https://project1.yourdomain.com

https://project2.yourdomain.com

https://project3.yourdomain.com

All served securely from one EC2 instance with Nginx and SSL 🚀

✍️ Author
Crafted with care by Your Name """

output_path = Path("/mnt/data/EC2-NodeJS-MultiApp-Hosting-Guide.md") output_path.write_text(readme_content.strip())

output_path.name

Always show details

Copy
Result
'EC2-NodeJS-MultiApp-Hosting-Guide.md'
