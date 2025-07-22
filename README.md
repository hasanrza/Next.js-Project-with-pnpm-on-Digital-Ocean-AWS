# Setup Next.js Project with pnpm on Digital Ocean & AWS

## Prerequisites
Ensure the following before proceeding:
- A Digital Ocean Droplet or an AWS EC2 instance running Ubuntu
- SSH access to your server
- A GitHub or Bitbucket repository with your Next.js project
- `pnpm` and Node.js installed locally for development

## Step 1: Connect to Your Server via SSH
```sh
ssh root@your_server_ip
```

## Step 2: Install Git and Set Up SSH Key
```sh
sudo apt update && sudo apt install git -y
ssh-keygen -t rsa -b 4096
cat ~/.ssh/id_rsa.pub
```

### Add SSH Key to GitHub & Bitbucket

#### GitHub
1. Go to **Settings** → **SSH and GPG Keys**
2. Click **New SSH Key**, paste the key, and save.

#### Bitbucket
1. Go to **Personal Settings** → **SSH Keys**
2. Click **Add Key**, paste the key, and save.

## Step 3: Install Node.js & pnpm

### Install Node.js
```sh
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

### Install pnpm
```sh
curl -fsSL https://get.pnpm.io/install.sh | sh -
```

> You may need to restart your shell or source your profile:
```sh
source ~/.bashrc
```

## Step 4: Clone Your Next.js Project
```sh
git clone git@github.com:yourusername/your-nextjs-repo.git /var/www/nextjs-app
cd /var/www/nextjs-app
```

## Step 5: Install Dependencies with pnpm
```sh
pnpm install
```

## Step 6: Build and Start the Next.js Project
```sh
pnpm build
pnpm start
```

## Step 7: Install and Configure PM2
```sh
sudo npm install -g pm2
pm2 start "pnpm start" --name nextjs-app
pm2 save
pm2 startup
```

## Step 8: Configure Nginx as a Reverse Proxy

### Install Nginx
```sh
sudo apt install nginx -y
```

### Create Nginx Config for Next.js
```sh
sudo nano /etc/nginx/sites-available/nextjs
```

Paste this configuration:
```nginx
server {
    listen 80;
    server_name your_domain_or_ip;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### Enable the Config
```sh
sudo ln -s /etc/nginx/sites-available/nextjs /etc/nginx/sites-enabled/
sudo rm /etc/nginx/sites-enabled/default
sudo systemctl restart nginx
```

## Step 9: Setup Firewall (UFW)
```sh
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
```

## Step 10: Monitor & Logs

### PM2 Logs
```sh
pm2 logs
```

### Nginx Logs
```sh
sudo journalctl -u nginx --no-pager | tail -n 50
sudo tail -f /var/log/nginx/error.log
```

---

## Deploy Next.js on AWS

### Connect to AWS EC2 Instance
```sh
ssh -i /path/to/key.pem ubuntu@your-aws-instance-ip
```

### Repeat the Same Setup
- Git + SSH key setup  
- Node.js & pnpm install  
- Clone + Build + Start project  
- PM2 + Nginx setup  

---

## Conclusion
Your Next.js project is now deployed on Digital Ocean and AWS using `pnpm`, PM2, and Nginx. Make sure to enable auto-deploy pipelines and security patches for production.

---

## Author
Developed by Hasan Raza

📧 Contact: hasanraz562@gmail.com

---

## Tags
Next.js, Digital Ocean, AWS, pnpm, Node.js, PM2, Nginx, Deployment, SSH, GitHub, Bitbucket, Server Setup
