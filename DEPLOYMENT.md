# Deployment Guide

This guide covers deploying the alfa-leetcode-api to various free hosting platforms and setting up custom domains.

## Prerequisites

- GitHub account (recommended for auto-deployment)
- Git repository pushed to GitHub
- Custom domain (e.g., nur.shuvo) and access to DNS settings

## Platform Comparison

| Platform | Free Tier | Custom Domain | Auto-Deploy | Docker Support |
|----------|-----------|---------------|-------------|----------------|
| **Render** | 750 hrs/mo | ✅ Free | ✅ | ✅ |
| **Railway** | $5 credit/mo | ✅ Free | ✅ | ✅ |
| **Vercel** | Unlimited | ✅ Free | ✅ | ❌ (Serverless) |
| **Fly.io** | Free tier | ✅ Free | ✅ | ✅ |

---

## Option 1: Render (Recommended)

### Deploy Steps

1. **Sign up at [render.com](https://render.com)**

2. **Create New Web Service**
   - Click "New +" → "Web Service"
   - Connect your GitHub repository
   - Or use "Deploy from GitHub URL"

3. **Configure Service**
   - Name: `alfa-leetcode-api`
   - Environment: `Node`
   - Build Command: `npm install && npm run build`
   - Start Command: `npm start`
   - Plan: `Free`

4. **Deploy**
   - Click "Create Web Service"
   - Wait for deployment (2-3 minutes)
   - Get your URL: `https://alfa-leetcode-api.onrender.com`

### Custom Domain Setup (nur.shuvo)

1. **In Render Dashboard**
   - Go to your service → Settings → Custom Domain
   - Click "Add Custom Domain"
   - Enter: `nur.shuvo`
   - Copy the CNAME/A records shown

2. **In Your DNS Provider**
   - Add CNAME record:
     ```
     Type: CNAME
     Name: @  (or nur.shuvo)
     Value: [your-app].onrender.com
     TTL: 3600
     ```
   - Or A record if CNAME not supported for root domain

3. **Verify**
   - Wait 5-60 minutes for DNS propagation
   - Render will auto-verify and issue SSL certificate

---

## Option 2: Railway

### Deploy Steps

1. **Sign up at [railway.app](https://railway.app)**

2. **Deploy from GitHub**
   - Click "New Project"
   - Select "Deploy from GitHub repo"
   - Choose your repository
   - Railway auto-detects Node.js

3. **Configure (Optional)**
   - Railway uses `railway.toml` config automatically
   - Environment variables: Set `NODE_ENV=production`

4. **Get URL**
   - Railway provides a URL like: `https://[project].railway.app`
   - Enable public domain in settings

### Custom Domain Setup (nur.shuvo)

1. **In Railway Dashboard**
   - Go to your service → Settings
   - Scroll to "Domains"
   - Click "Custom Domain"
   - Enter: `nur.shuvo`

2. **In Your DNS Provider**
   - Add CNAME record:
     ```
     Type: CNAME
     Name: @
     Value: [shown by Railway]
     TTL: 3600
     ```

3. **Verify**
   - Click "Verify" in Railway
   - SSL certificate issued automatically

---

## Option 3: Vercel

### Deploy Steps

1. **Install Vercel CLI** (Optional)
   ```bash
   npm install -g vercel
   ```

2. **Deploy via Web**
   - Go to [vercel.com](https://vercel.com)
   - Import your GitHub repository
   - Vercel auto-detects `vercel.json`
   - Click "Deploy"

3. **Or Deploy via CLI**
   ```bash
   vercel login
   vercel --prod
   ```

4. **Get URL**
   - Default: `https://[project].vercel.app`

### Custom Domain Setup (nur.shuvo)

1. **In Vercel Dashboard**
   - Go to Project → Settings → Domains
   - Enter: `nur.shuvo`
   - Click "Add"

2. **In Your DNS Provider**
   - Follow Vercel's instructions (usually):
     ```
     Type: A
     Name: @
     Value: 76.76.21.21

     Type: CNAME
     Name: www
     Value: cname.vercel-dns.com
     ```

3. **Verify**
   - Vercel auto-verifies and issues SSL

---

## Option 4: Fly.io

### Deploy Steps

1. **Install flyctl**
   ```bash
   # macOS
   brew install flyctl

   # Linux
   curl -L https://fly.io/install.sh | sh

   # Windows
   iwr https://fly.io/install.ps1 -useb | iex
   ```

2. **Login**
   ```bash
   flyctl auth login
   ```

3. **Deploy**
   ```bash
   # First time
   flyctl launch --config fly.toml

   # Follow prompts:
   # - Choose app name
   # - Choose region (closest to users)
   # - Don't create Postgres database
   # - Don't deploy now (will deploy next)

   # Deploy
   flyctl deploy
   ```

4. **Get URL**
   - Default: `https://[app-name].fly.dev`

### Custom Domain Setup (nur.shuvo)

1. **Add Certificate**
   ```bash
   flyctl certs add nur.shuvo
   ```

2. **Get DNS Records**
   ```bash
   flyctl certs show nur.shuvo
   ```

3. **In Your DNS Provider**
   - Add the A/AAAA records shown:
     ```
     Type: A
     Name: @
     Value: [shown by Fly.io]

     Type: AAAA
     Name: @
     Value: [shown by Fly.io]
     ```

4. **Verify**
   ```bash
   flyctl certs check nur.shuvo
   ```
   - Wait for DNS propagation
   - SSL issued automatically

---

## Environment Variables

For all platforms, set these environment variables if needed:

```env
NODE_ENV=production
PORT=3000  # Usually auto-set by platform
```

---

## Custom Domain DNS Setup for nur.shuvo

### If using Namecheap, GoDaddy, Cloudflare, etc.

1. **Login to your DNS provider**
2. **Navigate to DNS Management**
3. **Add records as instructed by your chosen platform**
4. **Common settings:**
   ```
   Type: CNAME (or A)
   Host: @ (root domain)
   Value: [platform-provided-url]
   TTL: Automatic or 3600
   ```

### Using Cloudflare (Recommended for free SSL + CDN)

1. **Add site to Cloudflare**
2. **Update nameservers at your domain registrar**
3. **In Cloudflare DNS:**
   ```
   Type: CNAME
   Name: @
   Target: [your-deployment-url]
   Proxy status: Proxied (orange cloud)
   ```
4. **Benefits:**
   - Free SSL certificate
   - DDoS protection
   - CDN caching
   - Analytics

---

## Monitoring & Logs

### Render
- Dashboard → Logs tab
- Real-time logs available

### Railway
- Dashboard → Deployments → View logs
- Metrics available in free tier

### Vercel
- Dashboard → Deployments → Function logs
- Real-time logs in CLI: `vercel logs`

### Fly.io
```bash
flyctl logs
flyctl status
flyctl monitor
```

---

## Troubleshooting

### Build Fails
- Ensure `package.json` has correct scripts
- Check Node.js version compatibility
- Verify TypeScript compiles: `npm run build`

### API Not Responding
- Check logs for errors
- Verify PORT environment variable
- Test locally: `npm run dev`

### Custom Domain Not Working
- Wait 24-48 hours for full DNS propagation
- Use `dig nur.shuvo` or `nslookup nur.shuvo` to verify DNS
- Check SSL certificate status in platform dashboard
- Ensure DNS records exactly match platform instructions

### Rate Limiting Issues
- Current limit: 1000 requests/hour
- Modify in `src/app.ts` line 13 if needed

---

## Recommended: Render with Cloudflare

**Best setup for production:**

1. Deploy to **Render** (reliable, 750 free hours)
2. Use **Cloudflare** for DNS (free SSL, CDN, DDoS protection)
3. Point `nur.shuvo` to Render via Cloudflare CNAME

**Steps:**
1. Deploy to Render (get URL)
2. Add nur.shuvo to Cloudflare
3. In Cloudflare DNS: CNAME @ → your-app.onrender.com
4. Enable proxy (orange cloud icon)
5. Done! Your API at `https://nur.shuvo` with free SSL + CDN

---

## Free Tier Limitations

| Platform | Limitation |
|----------|------------|
| **Render** | Sleeps after 15 min inactivity, cold start ~30s |
| **Railway** | $5 credit/month (~500 hours) |
| **Vercel** | 100GB bandwidth, serverless functions |
| **Fly.io** | 3 shared-cpu VMs, 160GB/month transfer |

**To avoid cold starts on Render:**
- Use a free uptime monitor: [UptimeRobot](https://uptimerobot.com) to ping every 5 minutes
- Or upgrade to paid tier ($7/month for 24/7 uptime)

---

## Questions?

- Render Docs: https://render.com/docs
- Railway Docs: https://docs.railway.app
- Vercel Docs: https://vercel.com/docs
- Fly.io Docs: https://fly.io/docs