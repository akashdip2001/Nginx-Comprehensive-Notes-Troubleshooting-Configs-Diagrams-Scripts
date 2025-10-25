The situation: **you know an IP address but `nmap` doesn't show it**. 

---

# Problem statement

You have an IP (e.g. `203.0.113.10`) but `nmap` reports the host as not found/“down” or shows no open ports. Possible causes: host is unreachable from your vantage point, ICMP/scan probes are blocked or filtered by firewall/IDS, host uses a private network or different subnet, or you’re being rate-limited/black-holed. Here’s what to do next.

---

# 1 — Quick recon (do this first)

```bash
# 1) Confirm the IP is correct
echo "203.0.113.10"

# 2) Simple reachability
ping -c 4 203.0.113.10

# 3) Try nmap without host discovery
sudo nmap -Pn -p 80,443,22 203.0.113.10

# 4) Probe all TCP ports (slow) if permitted
sudo nmap -Pn -p- --min-rate=1000 203.0.113.10
```

Interpretation:

* `ping` replies → host is ICMP-reachable.
* `nmap -Pn` shows open/filtered/closed: `open` = service; `filtered` = firewall; `closed` = reachable but no service.

---

# 2 — Use different probe types (nmap flags)

```bash
# TCP SYN scan (stealth)
sudo nmap -sS -Pn -p 1-1000 203.0.113.10

# TCP connect (if SYN blocked)
sudo nmap -sT -Pn 203.0.113.10

# UDP probe (may need privileges)
sudo nmap -sU -Pn -p 53,123 203.0.113.10

# Send SYN to specific ports (useful against some firewalls)
sudo nmap -PS22,80,443 203.0.113.10

# Send ACK probes (see if stateful firewall allows established-like traffic)
sudo nmap -PA80,443 203.0.113.10

# Ask for reason and traceroute
sudo nmap -Pn --reason --traceroute 203.0.113.10
```

Why: different networks/filters look at different probes. `-PS` or `-PA` often elicit responses where ICMP/ping is blocked.

---

# 3 — Check routing & ARP (are packets getting there?)

If you have local network access (on same LAN or a router you control):

```bash
# See if kernel has ARP entry
arp -n 203.0.113.10    # or
ip neigh show 203.0.113.10

# Traceroute to see the path
traceroute 203.0.113.10
# Or for TCP
sudo traceroute -T -p 80 203.0.113.10
```

Interpretation:

* ARP entry present → same L2, host responded to ARP.
* Traceroute stalls at a hop → indicates where packets are dropped.

---

# 4 — Capture traffic to prove whether probes arrive / responses leave

(Use only if you have access to the network interface nearest the target.)

```bash
# Capture SYNs to target port 80
sudo tcpdump -i any host 203.0.113.10 and 'tcp port 80' -vv

# Save to file while you run nmap from your machine
sudo tcpdump -i eth0 host 203.0.113.10 and '(port 80 or port 443)' -w /tmp/cap.pcap
```

What to look for:

* You see SYN from your scanner → firewall/router forwarded it.
* You see no SYN arrival → packets are blocked before reaching that interface (routing issue, upstream firewall).
* You see SYN arrive but no SYN/ACK → host not listening or host firewall blocking outbound response.

---

# 5 — Try application-level checks

```bash
# Simple HTTP/TLS probe (bypasses some middleboxes)
curl -I --max-time 5 http://203.0.113.10
curl -Iv --max-time 5 https://203.0.113.10
# Or use telnet/netcat for port handshake test
nc -vz 203.0.113.10 80
telnet 203.0.113.10 443
```

If `curl` connects but `nmap` didn't: some middleboxes detect/alter scans but allow real HTTP.

---

# 6 — Try alternative vantage points

If your local network is blocking or rate-limiting scans:

* Use another network (mobile hotspot, different cloud VM, colleague’s machine).
* Use an external port-check service or online scanner (with permission).
  Why: confirms whether the problem is local or global.

---

# 7 — Check DNS & reverse mapping (maybe IP moved)

```bash
dig -x 203.0.113.10 +short
# or
nslookup 203.0.113.10
```

Also verify the hostname you were expecting resolves to that IP:

```bash
dig example.com +short
```

If hostname points elsewhere, you might be scanning wrong target.

---

# 8 — Cloud & infra checks (if you control the host)

If the IP belongs to an instance you manage (AWS/GCP/Azure):

* Check instance state (running?).
* Check cloud firewall/security group rules (inbound rules, NACL).
* Check the host OS firewall (`iptables`, `nft`, `ufw`) and `ss -tunlp`.

Commands (on host):

```bash
sudo ss -tunlp | egrep ':80|:443'
sudo iptables -L -n -v
sudo ufw status verbose
sudo systemctl status nginx  # or your service
```

---

# 9 — Possible causes & how to act

* **ICMP allowed but TCP blocked** → host/network firewall blocking TCP. Action: open ports in firewall/Security Group or request whitelisting.
* **All probes blocked (filtered)** → midbox (WAF, IDS, upstream router) dropping or blackholing. Action: contact network admins or use allowed channel.
* **Host down / powered off** → start instance, fix OS.
* **Host on private subnet / NAT** → cannot reach directly; use bastion or VPN.
* **Rate-limited / scan-detection** → perform less aggressive probes; coordinate with admins.
* **ARP/No route** → fix routing or ensure host on same L2, proper gateway configured.

---

# 10 — Legal / ethical note (what to say in interview)

* Always have authorization before scanning or probing networks you don't own.
* In an interview explain that you’d get permission or use sanctioned tooling/environments before aggressive checks.

---

# Short interview-ready script (copyable)

> “If `nmap` doesn’t show an IP, I first confirm the IP and ping it. I rerun `nmap -Pn` and use alternative probes (`-PS`, `-PA`, `-sT`) to rule out ping-based discovery or ICMP filtering. I run `traceroute` and `arp` to determine routing/ARP reachability. If I control the host, I capture packets with `tcpdump` to see if SYNs arrive and check `ss -tunlp` and host firewalls. If not, I test from another vantage point (mobile hotspot or a cloud VM) to see whether the issue is local or global, and I coordinate with networking/cloud admins when firewall or upstream devices block traffic.”

---