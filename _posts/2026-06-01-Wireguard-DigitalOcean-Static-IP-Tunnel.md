---
title: WireGuard Tunnel — Static IPv4 via DigitalOcean Droplet
date: 2026-06-01 08:00:00 +0100
categories: [Networking, WireGuard]
tags: [wireguard, digitalocean, nginx, vpn, tunnel, self-hosted, networking]
image:
  path: /assets/img/posts/WireguardTunnel.png
  alt: WireGuard Tunnel Setup
---

A complete guide to exposing your home server to the internet using a DigitalOcean droplet as a relay — no DDNS, no port forwarding, no dynamic IP headaches.

## What You Will Build

- A static public IP via a DigitalOcean droplet
- A secure encrypted tunnel using **WireGuard**
- A reverse proxy using **Nginx**
- HTTPS via **Certbot / Let's Encrypt**

---

## Prerequisites

- A DigitalOcean droplet running Ubuntu
- A domain pointed at your droplet's public IP
- A home server running Linux

---

## Step 1 — Point Your Domain

In your DNS provider, add an A record:

```
A record → yourdomain.com → YOUR_DROPLET_PUBLIC_IP
```

---

## Step 2 — Install WireGuard

Run this on **both the droplet and your home server**:

```bash
sudo apt update
sudo apt install wireguard -y
```

---

## Step 3 — Generate Keys

Run this on **both machines** and save the output:

```bash
wg genkey | tee privatekey | wg pubkey > publickey
```

You will need:
- Droplet private key + public key
- Home server private key + public key

---

## Step 4 — Configure WireGuard

We will assign these tunnel IPs:

```
Droplet:     10.0.0.1
Home server: 10.0.0.2
```

### Droplet config

```bash
sudo nano /etc/wireguard/wg0.conf
```

```ini
[Interface]
PrivateKey = DROPLET_PRIVATE_KEY
Address = 10.0.0.1/24
ListenPort = 51820

[Peer]
PublicKey = HOME_PUBLIC_KEY
AllowedIPs = 10.0.0.2/32
```

### Home server config

```bash
sudo nano /etc/wireguard/wg0.conf
```

```ini
[Interface]
PrivateKey = HOME_PRIVATE_KEY
Address = 10.0.0.2/24

[Peer]
PublicKey = DROPLET_PUBLIC_KEY
Endpoint = YOUR_DROPLET_IP:51820
AllowedIPs = 10.0.0.1/32
PersistentKeepalive = 25
```

---

## Step 5 — Start WireGuard

Run on **both machines**:

```bash
sudo wg-quick up wg0
sudo wg
```

---

## Step 6 — Test the Tunnel

From the droplet, ping the home server:

```bash
ping 10.0.0.2
```

If you get replies the tunnel is working.

---

## Step 7 — Install Nginx on the Droplet

```bash
sudo apt install nginx -y
```

---

## Step 8 — Configure Nginx Reverse Proxy

```bash
sudo nano /etc/nginx/sites-available/home
```

```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://10.0.0.2:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

Enable the config:

```bash
sudo ln -s /etc/nginx/sites-available/home /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

---

## Step 9 — Enable HTTPS with Let's Encrypt

```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com
```

Certbot automatically sets up SSL, configures the HTTPS redirect, and enables auto-renewal.

---

## Step 10 — Run Your Home Service

On your home server, start whatever service you want to expose. For a quick test:

```bash
python3 -m http.server 8080
```

Then restart Nginx on the droplet:

```bash
sudo systemctl restart nginx
```

---

## Step 11 — Firewall

On the droplet, open only the required ports:

```bash
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw allow 51820/udp
sudo ufw enable
```

---

## Step 12 — Test

Open your browser and navigate to:

```
https://yourdomain.com
```

You should see your home server's response.

---

## Optional — Multiple Services

To expose multiple services on different subdomains, add separate server blocks in Nginx:

```nginx
server {
    server_name app1.yourdomain.com;
    location / {
        proxy_pass http://10.0.0.2:3000;
    }
}

server {
    server_name app2.yourdomain.com;
    location / {
        proxy_pass http://10.0.0.2:5000;
    }
}
```

Run `sudo certbot --nginx -d app1.yourdomain.com -d app2.yourdomain.com` to add HTTPS for each subdomain.

---

## Summary

| Component | Role |
|---|---|
| DigitalOcean droplet | Static public IP relay |
| WireGuard | Encrypted tunnel between droplet and home |
| Nginx | Reverse proxy routing traffic to home server |
| Certbot | Free HTTPS via Let's Encrypt |