# 🚀 Pipecat Quickstart Bot Deployment on Debian

This guide documents how to deploy the [Pipecat](https://github.com/pipecat-ai/pipecat) sample bot with HTTPS access using NGINX and Let's Encrypt on a Debian server.

---

## 🖥️ Server Information

- **IP Address:** `164.90.161.84`
- **Domain:** `pipecat.omid.blog`
- **OS:** Debian (fresh install)

This is just a sample that Im using during the docuemntation so it may not work. Change it with your own info
---

## 📦 1. Install Required Packages

```bash
apt update && apt install -y python3 python3-pip python3-venv curl nginx certbot python3-certbot-nginx
```

---

## 📁 2. Set Up Project Environment

```bash
mkdir -p /usr/local/src/pipecat-quickstart && cd /usr/local/src/pipecat-quickstart
python3 -m venv env
source env/bin/activate
```

---

## 📥 3. Download Example Files

```bash
curl -O https://raw.githubusercontent.com/pipecat-ai/pipecat/main/examples/foundational/01-say-one-thing.py
curl -O https://raw.githubusercontent.com/pipecat-ai/pipecat/main/examples/foundational/run.py
curl -O https://raw.githubusercontent.com/pipecat-ai/pipecat/main/examples/foundational/requirements.txt
```

---

## 📚 4. Install Python Dependencies

```bash
pip install -r requirements.txt
```

---

## 🔑 5. Add Cartesia API Key

Get your API key from [https://cartesia.ai](https://cartesia.ai), then:

```bash
echo "CARTESIA_API_KEY=your_cartesia_api_key" > .env
```

---

## 🌍 6. Set Up NGINX Reverse Proxy

Create NGINX config file:

```bash
nano /etc/nginx/sites-available/pipecat
```

Add:
```nginx
server {
    listen 80;
    server_name pipecat.omid.blog;

    location / {
        proxy_pass http://127.0.0.1:7860;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Enable the config and reload NGINX:

```bash
ln -s /etc/nginx/sites-available/pipecat /etc/nginx/sites-enabled/
nginx -t && systemctl reload nginx
```

---

## 🔐 7. Get SSL Certificate with Let's Encrypt

```bash
certbot --nginx -d pipecat.omid.blog
```

Follow prompts to register and deploy the certificate.

---

## ▶️ 8. Run the Pipecat Bot

```bash
cd /usr/local/src/pipecat-quickstart
source env/bin/activate
python 01-say-one-thing.py
```

Visit: **https://pipecat.omid.blog** to interact with the bot.

---

## 📌 Optional: Keep Bot Running with `tmux`

```bash
apt install tmux
tmux new -s pipecat
cd /usr/local/src/pipecat-quickstart
source env/bin/activate
python 01-say-one-thing.py
# Press Ctrl+B then D to detach
```

---

## ✅ Result

When a user opens the URL, the bot greets them with:

> “Hello there!”

Then ends the session automatically.
