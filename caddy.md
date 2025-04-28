
# 🛡️ Caddy Installation and Reverse Proxy Setup

## 1. Install Required Packages
```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
```

## 2. Add Caddy GPG Key and Repository
```bash
# Add the GPG key
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg

# Add the Caddy repository
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
```

## 3. Install Caddy
```bash
sudo apt update
sudo apt install caddy
```

## 4. Configure the Caddyfile
```bash
sudo nano /etc/caddy/Caddyfile
```

Paste the following into the Caddyfile:

```plaintext
# Replace yourdomain.com with your actual domain name

yourdomain.com {
    reverse_proxy localhost:3000
}

yourdomain.com/api/* {
    reverse_proxy localhost:8000
}

yourdomain.com/ws {
    reverse_proxy localhost:8000
}
```

> **Important:**  
> 🔵 Update `yourdomain.com` with your **actual custom domain** before saving!

> **Shortcut keys:**  
> - `Ctrl + S` → Save  
> - `Ctrl + X` → Exit

## 5. Restart Caddy Service
```bash
sudo systemctl restart caddy
```

## 6. Start Docker Compose
```bash
sudo docker compose up --build -d
```

---

### ✅ Notes
- Make sure your domain's DNS is pointed correctly to your server's IP.
- Ports 80 and 443 (HTTP/HTTPS) must be open in your firewall settings.

---
