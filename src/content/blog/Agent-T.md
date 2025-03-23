---
title: "Agent-T"
description: ""
pubDate: "Jul 15 2022"
heroImage: "/Agent-T/Untitled.png"
---
## Nmap:-

```bash
Nmap scan report for 10.10.101.180
Host is up (0.15s latency).
Not shown: 65534 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
80/tcp open  http?

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 701.06 seconds
```

## Nikto:-

```bash
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.180.150
+ Target Hostname:    10.10.180.150
+ Target Port:        80
+ Start Time:         2022-08-08 13:49:12 (GMT5.5)
---------------------------------------------------------------------------
+ Server: No banner retrieved
+ Retrieved x-powered-by header: **PHP/8.1.0-dev**
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
```

## Exploit:-

PHP/8.1.0-dev vulnerable to remote code execution

```bash
[https://github.com/flast101/php-8.1.0-dev-backdoor-rce/blob/main/revshell_php_8.1.0-de](https://github.com/flast101/php-8.1.0-dev-backdoor-rce/blob/main/revshell_php_8.1.0-dev.py)
```