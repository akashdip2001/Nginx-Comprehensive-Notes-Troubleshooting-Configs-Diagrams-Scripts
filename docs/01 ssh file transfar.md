You’ve got HTML files on your **Windows machine**, and you want to upload them to your **WSL2 Kali Linux’s Nginx web root** (`/var/www/html`) using **FileZilla**.

<img src="../public/img/Screenshot (1273).png">

---

## 🧠 First — Understand the problem

FileZilla connects over **SFTP (SSH)**.
But by default, **WSL2 doesn’t enable an SSH server (sshd)** — so FileZilla can’t connect until you enable it.

So we’ll do:

1. Install & enable SSH server inside WSL2
2. Find your WSL2 IP
3. Use that IP + username + password in FileZilla

---

## ⚙️ Step 1 — Install SSH server inside WSL2 Kali

In your WSL2 Kali terminal:

```bash
sudo apt update
sudo apt install openssh-server -y
```

Then start the SSH service:

```bash
sudo service ssh start
```

You can verify it’s running:

```bash
sudo service ssh status
```

✅ It should say:

```
Active: active (running)
```

<img width="100%" alt="Screenshot (1275)" src="https://github.com/user-attachments/assets/e01319c7-245d-4e31-8116-1cef4f2e3b6b" />

---

## 🌐 Step 2 — Find your WSL2 IP address

Run:

```bash
hostname -I
```

Example output:

```
172.30.43.62
```

Remember this IP — you’ll use it in FileZilla.

<img width="100%" alt="Screenshot 2025-10-19 143210" src="https://github.com/user-attachments/assets/f7c6cd2c-353f-46b2-b4af-bf524f3551fb" />

---

## 🧩 Step 3 — Allow SSH through firewall (UFW)

```bash
sudo ufw allow OpenSSH
sudo ufw reload
sudo ufw status
```

You should see:

```
22/tcp ALLOW Anywhere
```

<img width="100%" alt="Screenshot (1276)" src="https://github.com/user-attachments/assets/f64a57af-1c03-41cd-a38e-22821d32a554" />

---

## 💻 Step 4 — Connect from FileZilla (on Windows)

1. **Open FileZilla**
2. Go to **File → Site Manager → New Site**
3. Set the following:

| Field      | Value                                 |
| ---------- | ------------------------------------- |
| Protocol   | SFTP – SSH File Transfer Protocol     |
| Host       | *(your WSL2 IP, e.g. `172.30.43.62`)* |
| Port       | 22                                    |
| Logon Type | Normal                                |
| User       | `akash2001` *(your Kali username)*    |
| Password   | *(your Kali user password)*           |

4. Click **Connect**

<img width="100%" alt="Screenshot (1277)" src="https://github.com/user-attachments/assets/ca4624c7-495b-4520-804e-921bc19df69d" />


✅ You should now see your Kali filesystem (`/home/akash2001`, `/etc`, `/var`, etc.)

---

## 📁 Step 5 — Navigate to Nginx root

In the **remote (right) pane**:

```
/var/www/html
```

In the **local (left) pane**, select your Windows folder that contains your HTML files.

Then **drag and drop** your files from left (Windows) → right (Kali `/var/www/html`).

---

## 🔐 Step 6 — Fix permissions (optional but safe)

After uploading, in Kali WSL2 terminal:

```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

---

## 🌍 Step 7 — Test in browser

Open:

```
http://localhost
```

or

```
http://<your_WSL_IP>
```

You should now see **your uploaded HTML site!** 🎉

---

## ⚡ Optional — Auto-start SSH server on WSL2 launch

By default, SSH stops when you close WSL.
To make it start automatically, edit your `.bashrc` or create `/etc/wsl.conf` to autostart it.

### Option A: Add this to your `.bashrc`

```bash
sudo service ssh status > /dev/null || sudo service ssh start
```

### Option B: Add to `/etc/wsl.conf`

```ini
[boot]
command="service ssh start"
```

Then restart WSL:

```bash
wsl --shutdown
```

and reopen Kali.

---

✅ **Now you can use FileZilla anytime** to manage `/var/www/html` like a remote web server — directly from your Windows machine.

---

## *Easier method* (without FileZilla) — where you can **map your WSL2 `/var/www/html` folder directly in Windows Explorer** like a local drive (`\\wsl$\kali-linux\var\www\html`)? It’s super handy for quick file edits. [`HOW?`](/docs/02%20map%20wsl%20in%20windows.md)
