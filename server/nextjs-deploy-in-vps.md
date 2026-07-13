# Deploy Next.js on a VPS

Video reference: [Deploy NextJS len VPS (+Auto deploy)](https://www.youtube.com/watch?v=XNq_QSdafic) by HoleTex

This note starts from the video's approach, then adds the extra production details you usually need when serving a real Next.js app from a VPS.

## What This Guide Covers

- basic deployment from GitHub to a VPS
- running Next.js with PM2
- using Nginx as a reverse proxy
- setting up a domain and SSL
- handling environment variables
- automatic deployment with GitHub Actions
- common troubleshooting and production notes

## Recommended Architecture

For a real deployment, a practical setup looks like this:

```text
Browser
  -> HTTPS (443)
  -> Nginx
  -> Next.js app on localhost:3000
  -> PM2 keeps the app running
```

This is better than exposing port `3000` directly to the internet.

## 1. Prepare Your Next.js App

Make sure your `package.json` includes the normal production scripts:

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
}
```

Optional but recommended for VPS deployments: use standalone output.

In `next.config.js`:

```js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: "standalone",
};

module.exports = nextConfig;
```

Why this helps:

- creates a smaller production bundle
- makes deployment cleaner
- avoids shipping unnecessary files

## 2. Push the Code to GitHub

```bash
git init
git add .
git commit -m "initial commit"
git branch -M main
git remote add origin <your-repo-url>
git push -u origin main
```

If the repo is private, use SSH keys so the server can pull it securely.

## 3. Create and Prepare the VPS

Use Ubuntu if you want the easiest path.

You should have:

- VPS public IP
- SSH access
- a domain name if you want a proper public site

After logging in, update the system:

```bash
sudo apt update
sudo apt upgrade -y
```

Install useful packages:

```bash
sudo apt install -y curl git ufw nginx
```

## 4. Create a Non-Root Deploy User

Do not run everything as `root` in production.

Create a dedicated user:

```bash
sudo adduser deploy
sudo usermod -aG sudo deploy
```

If you use SSH keys:

```bash
su - deploy
mkdir -p ~/.ssh
chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

Then paste your public key into `~/.ssh/authorized_keys`.

Connect as:

```bash
ssh deploy@<your_vps_ip>
```

## 5. Install Node.js and npm

This guide uses Node.js 20.

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs
```

Verify:

```bash
node -v
npm -v
```

You can also install `pnpm` or `yarn` if your project uses them, but keep the guide consistent with your actual lockfile.

## 6. Clone the Project on the Server

As the deploy user:

```bash
cd ~
git clone <your-repo-url>
cd <your-project-folder>
```

If your repo is private, configure SSH auth for GitHub first.

## 7. Set Environment Variables

Before building, make sure the server has the correct environment values.

Typical examples:

- `NODE_ENV=production`
- `PORT=3000`
- `DATABASE_URL=...`
- `NEXTAUTH_URL=...`
- `NEXTAUTH_SECRET=...`
- `OPENAI_API_KEY=...`

Simple approach:

```bash
cp .env.example .env
nano .env
```

Important notes:

- never commit `.env` to Git
- server env values may differ from local values
- if your app uses server-side rendering, missing env vars can break build or runtime

## 8. Install Dependencies and Build

If you use npm:

```bash
npm ci
npm run build
```

Why `npm ci` is often better than `npm install` on servers:

- faster and more reproducible
- uses `package-lock.json` exactly
- better for CI/CD

If your project does not have a lockfile yet, use:

```bash
npm install
npm run build
```

## 9. Start the App Manually First

Before introducing PM2 and Nginx, confirm the app works:

```bash
npm run start
```

By default, Next.js runs on port `3000`.

Quick test:

```bash
curl http://localhost:3000
```

If that works, stop it and continue with PM2.

## 10. Run the App with PM2

Install PM2 globally:

```bash
sudo npm install -g pm2
pm2 -v
```

Start the app:

```bash
pm2 start "npm run start" --name next-app
```

Useful commands:

```bash
pm2 list
pm2 logs next-app
pm2 restart next-app
pm2 reload next-app
pm2 stop next-app
pm2 delete next-app
```

Save the PM2 process list:

```bash
pm2 save
```

Enable PM2 on reboot:

```bash
pm2 startup
```

PM2 will print another command. Run that command too.

## 11. Configure the Firewall

Only open the ports you need:

```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
sudo ufw status
```

If you are testing direct access to port `3000`, you can temporarily allow it:

```bash
sudo ufw allow 3000
```

But for production, prefer this setup:

- public traffic on `80` and `443`
- app traffic only on `localhost:3000`

## 12. Configure Nginx as a Reverse Proxy

Create a site config:

```bash
sudo nano /etc/nginx/sites-available/next-app
```

Example config:

```nginx
server {
    server_name example.com www.example.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable it:

```bash
sudo ln -s /etc/nginx/sites-available/next-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

If the default site conflicts, remove it:

```bash
sudo rm /etc/nginx/sites-enabled/default
sudo nginx -t
sudo systemctl restart nginx
```

## 13. Point Your Domain to the VPS

In your DNS provider:

- create an `A` record for `example.com` -> your VPS IP
- create another `A` record for `www.example.com` -> your VPS IP

Then wait for DNS propagation.

Test:

```bash
ping example.com
```

## 14. Add HTTPS with Let's Encrypt

Install Certbot:

```bash
sudo apt install -y certbot python3-certbot-nginx
```

Request certificates:

```bash
sudo certbot --nginx -d example.com -d www.example.com
```

Verify auto-renewal:

```bash
sudo systemctl status certbot.timer
sudo certbot renew --dry-run
```

After this, your app should be available at:

```text
https://example.com
```

## 15. Optional: Use Next.js Standalone Output in Production

If you enabled:

```js
output: "standalone"
```

Then after build you can run the standalone server directly:

```bash
node .next/standalone/server.js
```

Or with PM2:

```bash
pm2 start .next/standalone/server.js --name next-app
```

This can be cleaner than `npm run start` for some deployments.

If you use this approach, make sure static assets are also available:

- `.next/static`
- `public`

## 16. Set Up Auto Deploy with GitHub Actions

Store these in GitHub Actions secrets:

- `HOST`
- `USERNAME`
- `SSH_KEY`
- optionally `PORT`

Using SSH keys is better than using a password.

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy Next.js to VPS

on:
  push:
    branches:
      - production

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy over SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.SSH_KEY }}
          port: 22
          script: |
            set -e
            cd /home/deploy/your-project-folder
            git fetch origin
            git checkout production
            git pull origin production
            npm ci
            npm run build
            pm2 reload next-app || pm2 start "npm run start" --name next-app
            pm2 save
```

Release flow:

1. Push code to `main`
2. Create a pull request from `main` to `production`
3. Merge into `production`
4. GitHub Actions connects to the VPS
5. The server pulls the latest code
6. The server installs dependencies
7. The server builds the app
8. PM2 reloads the running service

## 17. Better Deploy Strategy for Real Projects

For a more robust setup, consider:

- building in CI first
- running tests before deployment
- deploying only if the build succeeds
- using a release directory strategy such as `/releases/<timestamp>`
- keeping a symlink like `/current`
- rolling back by pointing `/current` to the previous release

That is more advanced than the video, but much safer for production systems.

## 18. Common Problems

### `npm: command not found` in GitHub Actions SSH

Check:

```bash
which node
which npm
echo $PATH
```

Why it happens:

- non-interactive shells may not load the same PATH as your normal login shell

Possible fix:

- use absolute paths
- ensure Node is installed in a system path
- load your shell profile explicitly if needed

Example:

```bash
export PATH=$PATH:/usr/local/bin
```

### `pm2: command not found`

Check:

```bash
which pm2
```

Install globally if missing:

```bash
sudo npm install -g pm2
```

### Nginx returns `502 Bad Gateway`

Usually means:

- Next.js is not running
- Nginx points to the wrong port
- the app crashed during startup

Check:

```bash
pm2 logs next-app
curl http://127.0.0.1:3000
sudo systemctl status nginx
```

### App works locally but fails on server

Common causes:

- missing `.env` values
- build-time vs runtime env confusion
- database/network access issues
- file path assumptions that only work locally

### Static files or images are missing

Check:

- `public/` exists on the server
- `.next/static` exists after build
- if using standalone mode, required assets were copied correctly

## 19. Security Notes

- do not deploy as `root`
- disable password auth if possible and use SSH keys
- keep the server updated
- only open required firewall ports
- never commit secrets into the repo
- use HTTPS in production
- consider `fail2ban` for brute-force protection

## 20. Monitoring and Maintenance

Useful commands:

```bash
pm2 logs next-app
pm2 monit
df -h
free -h
top
sudo systemctl status nginx
```

Things to monitor:

- memory usage
- CPU usage
- disk space
- PM2 process restarts
- Nginx errors
- SSL renewal

## 21. Quick Start Checklist

```text
1. Push your Next.js app to GitHub
2. Create a VPS
3. Install git, nginx, ufw, and Node.js
4. Create a deploy user
5. Clone the repo
6. Add environment variables
7. Run npm ci
8. Run npm run build
9. Start the app with PM2
10. Configure Nginx to proxy to localhost:3000
11. Point your domain DNS to the VPS
12. Add HTTPS with Let's Encrypt
13. Add GitHub Actions auto deploy
14. Test logs, restart flow, and reboot persistence
```

## 22. Minimal Commands Reference

```bash
# app
npm ci
npm run build
npm run start

# pm2
pm2 start "npm run start" --name next-app
pm2 reload next-app
pm2 restart next-app
pm2 logs next-app
pm2 save

# nginx
sudo nginx -t
sudo systemctl restart nginx
sudo systemctl status nginx

# firewall
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw status
```
