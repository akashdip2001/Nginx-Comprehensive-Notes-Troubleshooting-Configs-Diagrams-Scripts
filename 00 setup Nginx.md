<h1 align="center">Set-up Nginx</h1>

</br>

<p align="center"><img src="public/public-img/nginx.jpg"></p>
<h3 align="center">Install process <a herf="https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04">link</a> in Ubuntu.<h3>

</br>
</br>


</br>
</br>

<p align="center">
  <img src="public/img/Screenshot (1238).png" alt="Image 1" width="45%" style="margin-right: 10px;"/>
  <img src="public/img/Screenshot (1240).png" alt="Image 2" width="45%" style="margin-right: 10px;"/>
</p>

---

# Allow Nginx Traffic

<img src="public/img/Screenshot (1241).png">

That’s a **very common situation** when working on a fresh Linux installation or a cloud VM (like AWS EC2, DigitalOcean, etc.) where `ufw` (Uncomplicated Firewall) isn’t preinstalled.

Let’s go step by step:

---

### 🔧 What happened

Your system said:

```
sudo: ufw: command not found
```

and

```
Command 'ufw' not found, but can be installed with:
sudo apt install ufw
```

✅ This means **`ufw` is not installed** on your system.

---

### 🧩 Step 1 — Install UFW (Debian/Ubuntu)

```bash
sudo apt update
sudo apt install ufw -y
```

Then enable and start it:

```bash
sudo ufw enable
sudo ufw status verbose
```

---

### 🧱 Step 2 — Allow Nginx Traffic

If your Nginx is installed via apt, Ubuntu usually creates profiles in `/etc/ufw/applications.d/`.

You can list them:

```bash
sudo ufw app list
```

Output might show:

```
Available applications:
  Nginx Full
  Nginx HTTP
  Nginx HTTPS
```

Then allow as needed:

```bash
sudo ufw allow 'Nginx HTTP'     # Port 80
sudo ufw allow 'Nginx HTTPS'    # Port 443
sudo ufw reload
sudo ufw status verbose
```
<img src="public/img/Screenshot (1244).png">

---

### ⚙️ Step 3 — If you prefer not to use `ufw`

You can directly use `iptables` or `nftables` to open ports:

#### Using `iptables`

```bash
sudo iptables -I INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -I INPUT -p tcp --dport 443 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

#### Using `nftables`

```bash
sudo nft add rule inet filter input tcp dport {80,443} accept
sudo nft list ruleset
```

---

# Access through Windows Browser

<img src="public/img/Screenshot (1246).png">

</br>

- ✅ nginx -t says the configuration is OK
- ✅ ss -tunlp shows Nginx is listening on 0.0.0.0:80 (so it’s reachable inside WSL)
- ✅ UFW is allowing HTTP traffic.

<img src="public/img/Screenshot (1247).png">

> I dont do anything in my Laptop, But if the windows Browser not able to connect with WSL internal network, You have to check - The [`firewall`](), [`port forwarding`](), and `networking`.

---