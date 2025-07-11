---
title: 'Secure Dedicated Ubuntu Server Setup with SSH Keys, Firewall, DDoS Protection, and Network Optimization'
seoTitle: Secure and Optimize Your Dedicated Ubuntu Server for Production"
date: 2025-07-11T10:00:00+05:30
description: Dedicated Ubuntu server setup with SSH key login, firewall rules, DDoS protection, and network tuning for secure, high-performance deployments.
slug: secure-dedicated-ubuntu-server-setup
categories:
  - Ubuntu
tags:
  - ubuntu
  - dedicated server
  - hetzner
---

## Table of Contents

1. [Create a Dedicated User Account](#1-create-a-dedicated-user-account)
2. [Generate SSH Key Pair](#2-generate-ssh-key-pair)
3. [Configure Key-Based SSH Login](#3-configure-key-based-ssh-login)
4. [Disable Root Login and Password Authentication](#4-disable-root-login-and-password-authentication)
5. [Enable and Configure UFW Firewall](#5-enable-and-configure-ufw-firewall)
6. [Install fail2ban for Brute-Force Protection](#6-install-fail2ban-for-brute-force-protection)
7. [Enable Automatic Security Updates](#7-enable-automatic-security-updates)
8. [Scan for Rootkits and Audit Security](#8-scan-for-rootkits-and-audit-security)
9. [Accept Unlimited Number of Requests](#9-accept-unlimited-number-of-requests)
10. [Boost Network Throughput with TCP BBR](#10-boost-network-throughput-with-tcp-bbr)
11. [Mitigate DDoS Attacks](#11-mitigate-ddos-attacks)
12. [Use HAProxy for Advanced Layer 7 Filtering](#12-use-haproxy-for-advanced-layer-7-filtering)

Provisioning a fresh Ubuntu server, especially on dedicated hardware like Hetzner,requires immediate hardening to prevent compromise and support scale. This guide walks through all essential steps: creating a secure user, enforcing SSH key-only login, disabling root and password authentication, locking down open ports via UFW, and tuning system limits for high concurrency. This guide also includes protections against brute-force attempts, SYN flood attacks, and other common threats, and applies TCP BBR to maximize sustained throughput. This leaves no open surfaces beyond what you explicitly allow.

> **Assumption**: You are working with a freshly provisioned dedicated Ubuntu server (such as from Hetzner) that initially allows direct `root` login via SSH.

## 1. Create a Dedicated User Account

Use `useradd` to create a user with a home directory, user group, bash shell, and system-level privileges:

```bash
sudo useradd --create-home --system --shell /bin/bash --user-group ashish
```

Replace `ashish` with your desired login name if you want to use a different user.

### Add User to `sudoers`

To give the user admin privileges:

```bash
sudo usermod -aG sudo ashish
```

This allows the user to run commands with `sudo`.

## 2. Generate SSH Key Pair

### On Your Local Machine (Recommended):

Generate the key pair outside the server, on your own system:

```bash
ssh-keygen -t rsa -b 4096 -f ~/ashish_key.pem -C "ashish@server"
mv ~/ashish_key.pem.pub ~/ashish_key.pub
```

This generates:

- `~/ashish_key.pem` (private key on your local machine)
- `~/ashish_key.pub` (public key on your local machine)


## 3. Configure Key-Based SSH Login

Copy the public key from your local machine to the server as `root`:

```bash
scp ~/ashish_key.pub root@your-server-ip:/tmp/

# Now, on the server:
sudo mkdir -p /home/ashish/.ssh
sudo cp /tmp/ashish_key.pub /home/ashish/.ssh/authorized_keys
sudo chmod 700 /home/ashish/.ssh
sudo chmod 600 /home/ashish/.ssh/authorized_keys
sudo chown -R ashish:ashish /home/ashish
sudo rm /tmp/ashish_key.pub
```

You already have the private key (`~/ashish_key.pem`) on your local machine. No need to download anything from the server.

Use it to login:

```bash
ssh -i ~/ashish_key.pem ashish@your-server-ip
````

## 4. Disable Root Login and Password Authentication

Edit SSH configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

Set the following:

```bash
PermitRootLogin no
PasswordAuthentication no
PermitEmptyPasswords no
ChallengeResponseAuthentication no
UsePAM yes
UseDNS no
AllowUsers ashish
```

Then restart SSH:

```bash
sudo systemctl restart ssh
```


## 5. Enable and Configure UFW Firewall

To harden your firewall:

1. Deny all incoming and outgoing traffic by default
2. Allow only essential ports for your use case (SSH, HTTP, HTTPS, 8080)

```bash
sudo apt install ufw -y

# Set default policies
sudo ufw default deny incoming
sudo ufw default deny outgoing

# Allow essential inbound ports
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 8080/tcp

# Allow necessary outbound connections (optional, adjust per app needs)
sudo ufw allow out 53        # DNS resolution
sudo ufw allow out 80/tcp    # HTTP client
sudo ufw allow out 443/tcp   # HTTPS client

# Finally, enable the firewall
sudo ufw enable
```

## 6. Install fail2ban for Brute-Force Protection

```bash
sudo apt install fail2ban -y
sudo systemctl enable fail2ban --now
```

Check status:

```bash
sudo fail2ban-client status sshd
```


## 7. Enable Automatic Security Updates

```bash
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure --priority=low unattended-upgrades
```


## 8. Scan for Rootkits and Audit Security

### rkhunter:

```bash
sudo apt install rkhunter -y
sudo rkhunter --update
sudo rkhunter --propupd
sudo rkhunter --check
```

### lynis:

```bash
sudo apt install lynis -y
sudo lynis audit system
```

## 9. Accept Unlimited Number of Requests

To configure your Ubuntu server to accept a high volume of incoming connections (e.g., from web clients or load balancers), tune the system-level limits and network settings:

### Run This Script

Create and run the following setup script:

```bash
cat <<EOF | sudo tee /usr/local/bin/unlock-ubuntu-capacity.sh
#!/bin/bash

# Set file limits
cat <<LIMITS | sudo tee -a /etc/security/limits.conf
* soft nofile 1048576
* hard nofile 1048576
LIMITS

# Ensure pam_limits is active
if ! grep -q pam_limits.so /etc/pam.d/common-session; then
  echo 'session required pam_limits.so' | sudo tee -a /etc/pam.d/common-session
fi

# Set sysctl values
cat <<SYSCTL | sudo tee -a /etc/sysctl.conf
fs.file-max = 2097152
net.core.somaxconn = 65535
net.core.netdev_max_backlog = 65535
net.ipv4.tcp_max_syn_backlog = 65535
net.ipv4.ip_local_port_range = 1024 65535
net.ipv4.tcp_tw_reuse = 1
SYSCTL

# Apply immediately
sudo sysctl -p
EOF

# Make it executable and run it
sudo chmod +x /usr/local/bin/unlock-ubuntu-capacity.sh
sudo /usr/local/bin/unlock-ubuntu-capacity.sh
```


## 10. Boost Network Throughput with TCP BBR

To improve sustained network throughput and prevent post-burst throttling (common on Hetzner servers), enable TCP BBR congestion control:

```bash
sudo tee /etc/sysctl.d/99-bbr.conf <<EOF
net.core.default_qdisc = fq
net.ipv4.tcp_congestion_control = bbr
EOF

sudo sysctl --system
```

Verify BBR is enabled:

```bash
sysctl net.ipv4.tcp_congestion_control
lsmod | grep bbr
```

Expected output:

```bash
net.ipv4.tcp_congestion_control = bbr
bbr    20480  0
```

This change helps maintain high transfer speeds after the initial bandwidth burst period, especially under Hetzner's default network shaping.

After enabling BBR and applying all sysctl changes, a reboot is required for full activation of kernel-level parameters.

> **Important:** On both Hetzner Dedicated and Hetzner Cloud servers, **do not reboot using ****\`\`**** or any CLI command**. Always use the Hetzner Web Console to send the reboot signal. Rebooting via CLI can result in permanent network loss due to DHCP or NIC reinitialization failures specific to Hetzner infrastructure.


## 11. Mitigate DDoS Attacks

While Hetzner provides upstream filtering against volumetric attacks, on-host protection is still necessary to guard against smaller floods or malformed traffic.

### Block SYN Flood

```bash
# Drop packets that are new but not SYN (possible invalid requests)
sudo iptables -A INPUT -p tcp ! --syn -m state --state NEW -j DROP

# Drop all fragmented packets (used in evasion techniques)
sudo iptables -A INPUT -f -j DROP

# Drop packets with all flags set (malformed)
sudo iptables -A INPUT -p tcp --tcp-flags ALL ALL -j DROP

# Drop packets with no flags (null scans)
sudo iptables -A INPUT -p tcp --tcp-flags ALL NONE -j DROP

# Drop SYN+FIN combinations (invalid)
sudo iptables -A INPUT -p tcp --tcp-flags SYN,FIN SYN,FIN -j DROP

# Drop SYN+RST combinations (conflicting state)
sudo iptables -A INPUT -p tcp --tcp-flags SYN,RST SYN,RST -j DROP
```

### Limit Connections Per IP

```bash
# Limit incoming TCP connections to port 80 (HTTP) from a single IP
# If a single IP tries to establish more than 50000 concurrent TCP connections, drop new ones
sudo iptables -A INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 50000 -j REJECT

# Same limit for HTTPS (port 443)
sudo iptables -A INPUT -p tcp --syn --dport 443 -m connlimit --connlimit-above 50000 -j REJECT
```

To persist:

```bash
sudo apt install iptables-persistent -y
sudo netfilter-persistent save
```

### Kernel Tweaks to Support TCP Flood Resistance

Also consider enabling the following kernel parameters to strengthen TCP connection handling during floods:

```bash
# Enables SYN cookies to handle half-open connections without reserving memory
net.ipv4.tcp_syncookies = 1

# Reduces the number of retries for completing the handshake
net.ipv4.tcp_synack_retries = 2
```

Add these to `/etc/sysctl.conf` and reload:

```bash
echo -e "net.ipv4.tcp_syncookies=1
net.ipv4.tcp_synack_retries=2" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

### Why This Works

After this threshold, additional new connection attempts from that IP will be temporarily dropped. This mechanism primarily protects against automated connection floods and port scanners.

This limit applies specifically to new TCP connections, not individual HTTP requests. In modern web applications, most browsers and HTTP clients use connection reuse (keep-alive) and multiplexing (especially with HTTP/2), allowing multiple resources to be fetched over a single TCP connection. So while each static asset could technically initiate a new HTTP request, it does not create a new TCP connection every time. Therefore, browsers can load many resources in parallel without triggering this connection rate limit—unless they open many distinct TCP sessions in rapid succession.

This strategy won’t block typical browser behavior but will throttle abusive clients trying to overwhelm your server with excessive TCP handshakes.

## 12. Use HAProxy for Advanced Layer 7 Filtering

While iptables protects at the TCP layer, you can use [HAProxy](https://www.haproxy.org/) as a reverse proxy to enforce HTTP-layer restrictions and mitigate abusive clients based on their request behavior — regardless of exact path.

Install HAProxy:

```bash
sudo apt update && sudo apt install haproxy -y
```

A sample rule to automatically track high-rate request senders without hardcoding any path:

```bash
frontend http-in
    bind *:80
    stick-table type ip size 1m expire 5s store http_req_rate(5s)
    http-request track-sc0 src
    http-request deny if { sc_http_req_rate gt 50 }
    default_backend servers

backend servers
    server app1 127.0.0.1:8080 check
```

This blocks clients sending more than 50 HTTP requests in 5 seconds, regardless of the path or headers. You can further customize based on method, user-agent, or headers if needed.


## Summary

Your Ubuntu server is now production-grade secure and tuned for high load:

- You have a dedicated sudo-enabled user with SSH key-only authentication
- Root login and all password-based SSH logins are fully disabled
- Only required inbound ports (22, 80, 443, 8080) are open via UFW; everything else is denied
- Fail2ban protects against brute-force login attempts
- Unattended upgrades ensure the system stays patched
- Kernel and user-level limits have been increased for high concurrency
- TCP BBR congestion control ensures reliable high-speed throughput under Hetzner's network shaping
- Intrusion detection tools (rkhunter, lynis) are available for auditing and anomaly detection

This setup is suitable for handling production workloads with a strong focus on security and scalability.
