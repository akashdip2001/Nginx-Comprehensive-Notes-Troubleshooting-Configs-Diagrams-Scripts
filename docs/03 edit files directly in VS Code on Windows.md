**WSL2 + Nginx + VS Code**.

```mermaid
graph TD

A[Windows 11 or 10 OS] --> B[VS Code with Remote-WSL Extension];
A --> C[Browser: Edge, Chrome, or Firefox];

B --> D[WSL2: Kali Linux Environment];
D --> E[/var/www/html Directory];
E --> F[Nginx Web Server on Port 80];
F --> G[Localhost or WSL IP (http://localhost)];

B -.-> |Edits Files| E;
C --> |HTTP Request| F;
F --> |HTML/CSS/JS Response| C;
D --> |Runs Services (nginx, ssh, ufw)| F;

subgraph Windows_Layer
A;
B;
C;
end

subgraph Linux_Layer_WSL2
D;
E;
F;
end

style A fill:#9ec8ff,stroke:#0066cc,stroke-width:2px,color:#003366;
style B fill:#b5f5dc,stroke:#008066,stroke-width:2px,color:#004d33;
style C fill:#ffd9b3,stroke:#e68a00,stroke-width:2px,color:#663300;
style D fill:#f0e68c,stroke:#b3a100,stroke-width:2px,color:#4d4d00;
style E fill:#ffe6ff,stroke:#993399,stroke-width:2px,color:#4d004d;
style F fill:#ffcccc,stroke:#cc0000,stroke-width:2px,color:#660000;
style G fill:#d9d9d9,stroke:#595959,stroke-width:2px,color:#1a1a1a;
```

You’ll be able to **edit files in VS Code on Windows**, but they’ll actually live and run inside your **Kali Linux web root (`/var/www/html`)** — so Nginx serves them instantly.

---

## 🧰 Prerequisites

Make sure you have:

1. **VS Code** installed on Windows → [https://code.visualstudio.com](https://code.visualstudio.com)
2. The **WSL extension** installed in VS Code:

   * Open VS Code → Press `Ctrl + Shift + X`
   * Search for **“WSL”**
   * Install **“Remote - WSL”** by Microsoft ✅

---

## 🧑‍💻 Step 1 — Open WSL from VS Code directly

In your **Kali terminal (inside WSL2)**, run:

```bash
code .
```

👉 The first time you do this, VS Code will install a **small server inside WSL** automatically.

After that, you’ll see a green indicator at the bottom-left:

```
>< WSL: Kali-Linux
```

That means VS Code is running **inside your Kali environment** — not on Windows.
(Any terminal inside VS Code will be your Kali shell.)

---

## 📂 Step 2 — Open your Nginx web root

In your Kali terminal or VS Code terminal:

```bash
cd /var/www/html
code .
```

✅ This opens `/var/www/html` in VS Code.
You can now drag in, edit, and save your HTML/CSS/JS/PHP files directly.

No permissions or sync issues — they’re stored natively in Linux.

---

## 🧱 Step 3 — Check permissions (if needed)

If VS Code can’t save files due to permission errors:

```bash
sudo chown -R $USER:$USER /var/www/html
sudo chmod -R 755 /var/www/html
```

Then reopen the folder.

---

## 🌍 Step 4 — Preview in Browser

Now open your browser and visit:

```
http://localhost
```

or

```
http://<your_WSL_IP>
```

Every time you save a file in VS Code, you’ll see the changes instantly reflected in the browser.

---

## ⚙️ Bonus Tips

### 💡 Run Nginx directly from VS Code terminal

Inside VS Code’s terminal (bottom panel):

```bash
sudo service nginx restart
```

You can even test config:

```bash
sudo nginx -t
```

---

### 💡 Live reload preview

If you install the **Live Server** extension in VS Code, you can use it for static files (HTML/CSS/JS) — but note:

* It runs from Windows, not Nginx.
* For real deployment testing, use Nginx URLs (your `http://localhost` page).

---

### 💡 Git integration (optional)

You can use Git directly inside WSL (and VS Code):

```bash
git init
git add .
git commit -m "Initial commit"
```

VS Code will detect Git and show changes automatically.

---

## ✅ Summary

| Step                          | Command / Action                        |
| ----------------------------- | --------------------------------------- |
| Install VS Code WSL extension | `Ctrl + Shift + X → “Remote - WSL”`     |
| Open Kali from terminal       | `code .`                                |
| Open Nginx folder             | `cd /var/www/html && code .`            |
| Test Nginx config             | `sudo nginx -t`                         |
| Restart Nginx                 | `sudo service nginx restart`            |
| Browser preview               | `http://localhost` or `http://<WSL_IP>` |

---

### 🎯 Result:

* You edit in **VS Code on Windows**
* Files live in **Kali `/var/www/html`**
* Nginx serves them directly
* You test instantly in browser
  No FileZilla, no copying — pure integrated dev environment. 🚀

---

Would you like me to include a **Mermaid diagram** showing how Windows ↔ VS Code ↔ WSL2 ↔ Nginx ↔ Browser all connect? It’ll help you visualize how the layers talk to each other (perfect for interview explanations).
