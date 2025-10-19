This is the **easiest and fastest way** to move/edit files between Windows and your **WSL2 Kali Linux** without using [`FileZilla`](/docs/01%20ssh%20file%20transfar.md) or SSH at all.

You can access your WSL filesystem **directly from File Explorer** — just like a regular Windows folder.

---

## 🌐 Access Your WSL2 Files from Windows Explorer

### ✅ Step 1 — Make sure WSL2 is running

Open your Kali WSL terminal:

```bash
pwd
```

If it shows something like `/home/akash2001`, it’s active — good!
You don’t even need to be root for this.

---

### 🪟 Step 2 — Open File Explorer in Windows

Now, in your Windows File Explorer address bar, type:

```
\\wsl$
```

and hit **Enter**.

✅ You’ll see a list of all running WSL distributions, like:

```
kali-linux
Ubuntu-22.04
Debian
```

---

### 📁 Step 3 — Navigate into Kali’s filesystem

Double-click:

```
\\wsl$\kali-linux
```

You’ll now see the Linux root folder (`/`):

```
bin
etc
home
var
usr
...
```

---

### 📂 Step 4 — Go to the Nginx web root

Navigate just like in Linux:

```
\\wsl$\kali-linux\var\www\html
```

This is **the same as** `/var/www/html` inside your WSL Kali.

You can also paste that directly into the address bar:

```
\\wsl$\kali-linux\var\www\html
```

---

### 🚀 Step 5 — Drag and drop files

Now, just drag your HTML/CSS/JS files from your **Windows desktop** or any folder **into** that location.

✅ Windows → WSL file transfer — instantly, no SSH, no FileZilla, no config.

---

### 🧰 Step 6 — Adjust permissions (optional)

If you face permission issues (e.g., Nginx can’t read them), open your Kali terminal and run:

```bash
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

---

## 💡 Bonus — Create a Desktop Shortcut

To make it one click:

1. In File Explorer, navigate to
   `\\wsl$\kali-linux\var\www\html`
2. Right-click the address bar → **Copy address**
3. On your Windows Desktop → Right-click → **New → Shortcut**
4. Paste:

   ```
   \\wsl$\kali-linux\var\www\html
   ```
5. Name it: `Kali Web Root`

Now, double-click that shortcut anytime to instantly open your Nginx folder. ⚡

---

### 🧠 Summary

| Task                  | Method                                          |
| --------------------- | ----------------------------------------------- |
| Upload HTML files     | Drag & drop in Explorer                         |
| Path                  | `\\wsl$\kali-linux\var\www\html`                |
| Equivalent Linux path | `/var/www/html`                                 |
| Permissions fix       | `sudo chown -R www-data:www-data /var/www/html` |

---

## **Edit HTML files directly in VS Code on Windows** — but they actually save inside your Kali `/var/www/html`? (It’s the preferred way for web dev + Nginx on WSL.). [`How ?`](/docs/03%20edit%20files%20directly%20in%20VS%20Code%20on%20Windows.md)
