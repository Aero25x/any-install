[![Join our Telegram RU](https://img.shields.io/badge/Telegram-RU-03A500?style=for-the-badge&logo=telegram&logoColor=white&labelColor=blue&color=red)](https://t.me/hidden_coding)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/aero25x)
[![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=x&logoColor=white)](https://x.com/aero25x)
[![YouTube](https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/@flaming_chameleon)
[![Reddit](https://img.shields.io/badge/Reddit-FF3A00?style=for-the-badge&logo=reddit&logoColor=white)](https://www.reddit.com/r/HiddenCode/)
[![Join our Telegram ENG](https://img.shields.io/badge/Telegram-EN-03A500?style=for-the-badge&logo=telegram&logoColor=white&labelColor=blue&color=red)](https://t.me/hidden_coding_en)


# Step-by-Step Guide: Setting Up WireGuard VPN on Ubuntu

Wireguard allow to connect from any types of device like PC (Linux, Mac, Windows) and work good even on mobile phones (Iphone, Android)

## Prerequisites
- Ubuntu server (18.04 or later)
- Root or sudo access
- Basic command line knowledge

## Step 1: Update System
```bash
sudo apt update
sudo apt upgrade -y
```

## Step 2: Install WireGuard
```bash
sudo apt install wireguard -y
```

## Step 3: Generate Server Keys
```bash
cd /etc/wireguard
umask 077
wg genkey | tee server_private.key | wg pubkey > server_public.key
```

## Step 4: Create Server Configuration
```bash
sudo nano /etc/wireguard/wg0.conf
```

Add the following configuration:
```ini
[Interface]
PrivateKey = <SERVER_PRIVATE_KEY>
Address = 10.0.0.1/24
ListenPort = 51820
SaveConfig = true

PostUp = iptables -A FORWARD -i wg0 -j ACCEPT; iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
PostDown = iptables -D FORWARD -i wg0 -j ACCEPT; iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE
```

Replace `<SERVER_PRIVATE_KEY>` with content from `server_private.key` and change `eth0` to your network interface name (check with `ip a`).

## Step 5: Enable IP Forwarding
```bash
sudo nano /etc/sysctl.conf
```

Uncomment or add:
```
net.ipv4.ip_forward=1
```

Apply changes:
```bash
sudo sysctl -p
```

## Step 6: Configure Firewall
```bash
sudo ufw allow 51820/udp
sudo ufw allow OpenSSH
sudo ufw enable
```

## Step 7: Start WireGuard
```bash
sudo systemctl enable wg-quick@wg0
sudo systemctl start wg-quick@wg0
```

Check status:
```bash
sudo systemctl status wg-quick@wg0
```

## Step 8: Generate Client Keys
```bash
wg genkey | tee client_private.key | wg pubkey > client_public.key
```

## Step 9: Add Client to Server
```bash
sudo wg set wg0 peer <CLIENT_PUBLIC_KEY> allowed-ips 10.0.0.2/32
```

Or edit `/etc/wireguard/wg0.conf` and add:
```ini
[Peer]
PublicKey = <CLIENT_PUBLIC_KEY>
AllowedIPs = 10.0.0.2/32
```

Then restart:
```bash
sudo systemctl restart wg-quick@wg0
```

## Step 10: Create Client Configuration File
Create a file `client.conf`:
```ini
[Interface]
PrivateKey = <CLIENT_PRIVATE_KEY>
Address = 10.0.0.2/24
DNS = 8.8.8.8

[Peer]
PublicKey = <SERVER_PUBLIC_KEY>
Endpoint = <SERVER_IP>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
```

Replace:
- `<CLIENT_PRIVATE_KEY>` with client private key
- `<SERVER_PUBLIC_KEY>` with server public key
- `<SERVER_IP>` with your server's public IP

## Step 11: Connect from Client
Transfer `client.conf` to your device and:

**Linux/Mac:**
```bash
sudo wg-quick up ./client.conf
```

**Windows/Mobile:** Import the configuration file into the WireGuard app.

## Verification
Check connection on server:
```bash
sudo wg show
```

Test from client:
```bash
curl ifconfig.me
```

You're done! Your WireGuard VPN is now running.

[![Join our Telegram RU](https://img.shields.io/badge/Telegram-RU-03A500?style=for-the-badge&logo=telegram&logoColor=white&labelColor=blue&color=red)](https://t.me/hidden_coding)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/aero25x)
[![Twitter](https://img.shields.io/badge/Twitter-1DA1F2?style=for-the-badge&logo=x&logoColor=white)](https://x.com/aero25x)
[![YouTube](https://img.shields.io/badge/YouTube-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/@flaming_chameleon)
[![Reddit](https://img.shields.io/badge/Reddit-FF3A00?style=for-the-badge&logo=reddit&logoColor=white)](https://www.reddit.com/r/HiddenCode/)
[![Join our Telegram ENG](https://img.shields.io/badge/Telegram-EN-03A500?style=for-the-badge&logo=telegram&logoColor=white&labelColor=blue&color=red)](https://t.me/hidden_coding_en)

