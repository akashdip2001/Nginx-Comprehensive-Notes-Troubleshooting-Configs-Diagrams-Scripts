# 🧭 **Linux & Nginx — Master Notes Index (Beginner → Advanced)**

| Level               | Category                              | Topic / Description                                                                                                                     | Link                                                                                                                                                                             |
| :------------------ | :------------------------------------ | :-------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 🟢 **Beginner**     | **Linux Basics**                      | 🔹 **Linux File Permissions** – Explanation of `rwx`, `chmod`, `chown`, `umask`, symbolic and numeric modes with examples and diagrams. | [View Notes](https://github.com/akashdip2001/linux-all-commands/blob/main/00%20Linux%20File%20Permission.md)                                                                     |
| 🟢 **Beginner**     | **Shell Scripting**                   | 🔹 **Bash Scripting Basics** – Variables, loops, conditionals, functions, I/O redirection, and automation fundamentals.                 | [View Notes](https://github.com/akashdip2001/linux-all-commands/blob/main/01%20Bash%20Scripting.md)                                                                              |
| 🟢 **Beginner**     | **Linux Commands Collection**         | 🔹 **Kali Linux All Commands** – Essential Kali commands for penetration testing, system checks, and network tools.                     | [View Notes](https://akashdip2001.github.io/kali-all-commands/#my-website-https-akashdip2001-github-io-linktree)                                                                 |
| | | 🔹 Ubuntu Commands Reference | [View](https://akashdip2001.github.io/ubuntu-all-commaands/) |
| | | 🔹 Fedora Commands Reference | [View](https://akashdip2001.github.io/fedora-all-imp-commands/) |
| 🟡 **Intermediate** | **Web Server Setup**                  | 🔹 **Nginx Setup & Configuration** – Step-by-step guide for installing and configuring Nginx, directory structure, and virtual hosts.   | [View Notes](https://github.com/akashdip2001/Nginx-Comprehensive-Notes-Troubleshooting-Configs-Diagrams-Scripts/blob/main/00%20setup%20Nginx.md)                                 |
| 🟠 **Advanced**     | **Network Troubleshooting**           | 🔹 **Nginx Troubleshooting Guide** – Diagnosing connection issues, DNS, reverse proxy problems, and HTTP response code analysis.        | [View Notes](https://github.com/akashdip2001/Nginx-Comprehensive-Notes-Troubleshooting-Configs-Diagrams-Scripts/blob/main/01%20Nginx%20Troubleshooting.md)                       |
| 🟠 **Advanced**     | **Network Forensics**                 | 🔹 **Network Forensics & Packet Analysis** – Using tools like Wireshark, tcpdump, and analyzing ICMP, HTTP, and TCP behavior.           | [View Notes](https://github.com/akashdip2001/Nginx-Comprehensive-Notes-Troubleshooting-Configs-Diagrams-Scripts/blob/main/02%20Network%20Forensics%20%26%20Packet%20Analysis.md) |
| 🔴 **Expert**       | **Nmap & Security Analysis**          | 🔹 **IP Not Found in Nmap** – Steps to troubleshoot unreachable IPs, ICMP filters, firewall rules, routing, and port scans.             | [View Notes](https://github.com/akashdip2001/Nginx-Comprehensive-Notes-Troubleshooting-Configs-Diagrams-Scripts/blob/main/03%20IP%20not%20found%20nMap.md)                       |
| 🔴 **Expert**       | **Firewall & Security Configuration** | 🔹 **Allow Firewall Rules for Nginx** – Configure UFW, firewalld, iptables to allow ports and IP-specific access in Linux or cloud.     | [View Notes](https://github.com/akashdip2001/Nginx-Comprehensive-Notes-Troubleshooting-Configs-Diagrams-Scripts/blob/main/04%20allow%20Firewall.md)                              |

---

### 🧩 **Progress Flow (Mermaid Diagram)**

```mermaid
graph TD
A[Linux Basics] --> B[Bash Scripting]
B --> C[Ubuntu & Fedora Commands]
C --> D[Nginx Setup]
D --> E[Nginx Troubleshooting]
E --> F[Network Forensics]
F --> G[Firewall & Security Configuration]
G --> H[Nmap & Network Analysis]
```

</br>
</br>

![photo_2025-10-19_19-33-51](https://github.com/user-attachments/assets/e3a83868-e83c-4e29-84ca-edbd69daa176)

> [`Apple clint evolution in TCS`](). TCS ILP ending date `2 Nov 2025`. But the `delivery manager` call for apple project on `15 Oct 2025` before I meet with RMG. Interview on Network + Cloud + Security + Linux. </br>

> TCS offer letter recive `14 Jul 2025`. & Joinning letter come `07 Aug 2025` for The Joinning Date `21 Aug 2025`. from TCS NQT 2025 ninja profile.

</br>

<p align="center">
  <img src="https://github.com/user-attachments/assets/ce44013f-89cc-4e4f-b932-d6fb8b773a4c" alt="Image 1" width="44%" style="margin-right: 10px;"/>
  <img src="https://github.com/user-attachments/assets/08216f7b-8e6a-499f-a1ea-2cb34020d17d" alt="Image 2" width="45%" style="margin-right: 10px;"/>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/415c03f4-1519-4366-9bb7-ee29bdc29a13" alt="Image 1" width="45%" style="margin-right: 10px;"/>
  <img src="https://github.com/user-attachments/assets/4156fdbc-9bf9-4c2d-875a-69e1c8c00028" alt="Image 2" width="45%" style="margin-right: 10px;"/>
</p>

---

**Single-page Nginx + Networking Troubleshooting Cheat Sheet** — concise, command-focused, and optimized for quick recall in interviews.

---

# 🧠 [NGINX](https://youtu.be/QkbPPAnC5dg) + NETWORKING TROUBLESHOOTING CHEAT SHEET

## 🔍 1. Quick Diagnosis Flow

```bash
ping -c 4 <IP>                # Check ICMP reachability
curl -I http://<IP>           # HTTP headers (expect 200/301/403/404/5xx)
curl -vk https://<IP>         # HTTPS + TLS debug
ss -tunlp | grep -E ':80|:443'  # Check if Nginx is listening
sudo systemctl status nginx     # Nginx running?
sudo tail -F /var/log/nginx/error.log  # Live logs
```

🧭 **Logic**

* Ping ✅, HTTP ❌ → web service/firewall/DNS issue
* 403 → permission/security rule
* 404 → wrong root/server_name
* 5xx → upstream/downstream failure

---

## 🌐 2. DNS & Network Checks

```bash
dig example.com +short        # DNS record
dig +trace example.com         # Full resolution chain
nslookup example.com
ip addr show                  # Interface IPs
ip route show                 # Default gateway
cat /etc/resolv.conf          # DNS servers
```

---

## 🔒 3. Firewall / Cloud Security

```bash
sudo ufw status verbose
sudo ufw allow 80,443/tcp
sudo iptables -L -n -v
sudo nft list ruleset
```

**AWS**
✅ Security Group → inbound 80/443 open
✅ NACL → allow inbound/outbound
✅ Target group health checks (ELB/ALB)

---

## 🧱 4. Nmap / Port Scanning

```bash
sudo nmap -Pn -p 22,80,443 <IP>     # No ping discovery
sudo nmap -sV -p 80,443 <IP>        # Version detection
sudo nmap -A <IP>                   # Aggressive (permission needed)
```

🗝️ **Interpret**

* `open` → service reachable
* `filtered` → firewall blocking
* `closed` → host reachable but no listener

---

## 🧾 5. Packet Capture / Wireshark

```bash
sudo tcpdump -i any host <IP> and '(port 80 or 443)' -vv
sudo tcpdump -w /tmp/capture.pcap
```

🧩 **Check**

* SYN → SYN/ACK → ACK = handshake OK
* SYN only → inbound blocked
* SYN/ACK no ACK → outbound blocked

Wireshark filter:
`ip.addr == <IP> && tcp.port == 443`

---

## ⚙️ 6. Nginx Key Files

```
/etc/nginx/nginx.conf
/etc/nginx/sites-enabled/*.conf
/var/log/nginx/access.log
/var/log/nginx/error.log
```

**Check listening & server blocks**

```nginx
listen 80;
listen 443 ssl;
server_name example.com;
root /var/www/html;
```

---

## 🔑 7. SSL / Encryption

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
```

🛡️ Use TLS 1.3, strong ciphers, HSTS, OCSP stapling.

---

## 🧰 8. Useful One-Liners

```bash
curl -s -o /dev/null -w "%{http_code}\n" http://example.com
sudo journalctl -u nginx -n 30 --no-pager
sudo systemctl reload nginx
sudo nginx -t && sudo systemctl reload nginx
```

---

## 🗂️ 9. Linux Network Config Paths

| OS               | Config File                                 |
| ---------------- | ------------------------------------------- |
| Ubuntu (Netplan) | `/etc/netplan/*.yaml`                       |
| Debian (Legacy)  | `/etc/network/interfaces`                   |
| RHEL/CentOS      | `/etc/sysconfig/network-scripts/ifcfg-eth0` |
| Common           | `/etc/hosts`, `/etc/resolv.conf`            |

---

## 💡 10. Interview Sound Bites

* “Ping works but browser fails → I check if Nginx listens on 80/443 and inspect HTTP status with curl.”
* “If `nmap` shows filtered, firewall or AWS SG likely drops TCP.”
* “I verify DNS mapping, then look at `/var/log/nginx/error.log` and proxy upstreams.”
* “I use `tcpdump` to confirm whether packets hit the server or get dropped.”
* “TLS issues? I run `curl -vk https://domain` to debug handshake and cert.”






