<h1 align="center">Set-up Nginx</h1>

</br>

[<p align="center"><img src="public/public-img/nginx.jpg"></p>](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-20-04)
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

<img src="public/img/Screenshot (1250).png">

</br>

<p align="center">
  <img src="public/img/Screenshot (1251).png" alt="Image 1" width="32%" style="margin-right: 10px;"/>
  <img src="public/img/Screenshot (1252).png" alt="Image 2" width="32%" style="margin-right: 10px;"/>
  <img src="public/img/Screenshot (1253).png" alt="Image 2" width="32%" style="margin-right: 10px;"/>
</p>

```conf
user www-data;
worker_processes auto;
worker_cpu_affinity auto;
pid /run/nginx.pid;
error_log /var/log/nginx/error.log;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
        # multi_accept on;
}

http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        types_hash_max_size 2048;
        server_tokens off; # Recommended practice is to turn this off

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1.2 TLSv1.3; # Dropping SSLv3 (POODLE), TLS 1.0, 1.1
        ssl_prefer_server_ciphers off; # Don't force server cipher order.

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;

        ##
        # Gzip Settings
        ##

        gzip on;

        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
}


#mail {
#       # See sample authentication script at:
#       # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#       # auth_http localhost/auth.php;
#       # pop3_capabilities "TOP" "USER";
#       # imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#       server {
#               listen     localhost:110;
#               protocol   pop3;
#               proxy      on;
#       }
#
#       server {
#               listen     localhost:143;
#               protocol   imap;
#               proxy      on;
#       }
#}
```

---

</br>
</br>

# Lets find the `Nginx` `default` `html` page.

</br>

<img src="public/img/Screenshot (1254).png">

```bash
conf.d/            # Additional config files
sites-available/   # Virtual host configs (inactive)
sites-enabled/     # Symlinks to active sites
nginx.conf         # Main configuration
mime.types         # Mime type definitions
```

<p align="center">
  <img src="public/img/Screenshot (1255).png" alt="Image 1" width="45%" style="margin-right: 10px;"/>
  <img src="public/img/Screenshot (1256).png" alt="Image 2" width="45%" style="margin-right: 10px;"/>
</p>

---

</br>

<p align="center">
  <img src="public/img/Screenshot (1257).png" alt="Image 1" width="45%" style="margin-right: 10px;"/>
  <img src="public/img/Screenshot (1258).png" alt="Image 2" width="45%" style="margin-right: 10px;"/>
</p>

---

# Now create you own configaration.
## Remove the old one.

#### Test and reload:

```bash
sudo nginx -t
sudo nginx -s reload
```

<img src="public/img/Screenshot (1259).png">

### Use `nano` or [`Vim`](https://youtu.be/sUIoV48rxVE)
<p align="center">
  <img src="public/img/Screenshot (1260).png" alt="Image 1" width="45%" style="margin-right: 10px;"/>
  [<img src="public/img/Screenshot (1263).png" alt="Image 2" width="45%" style="margin-right: 10px;"/>](https://youtu.be/sUIoV48rxVE)
</p>

</br>

### create out own configaration.
<img src="public/img/Screenshot (1265).png">

### creatThen go to the perticuller location what I maintain & create a html file.
<img src="public/img/Screenshot (1267).png">

### Then check the configaration again and Test in browser.
<p align="center">
  <img src="public/img/Screenshot (1269).png" alt="Image 1" width="45%" style="margin-right: 10px;"/>
  <img src="public/img/Screenshot (1270).png" alt="Image 2" width="45%" style="margin-right: 10px;"/>
</p>