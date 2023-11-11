---
title: "CozyHosting | HTB Write-Up"
summary: Write-up for the 'CozyHosting' HackTheBox machine
date: 2023-11-05
series: ["HTB"]
weight: 1
aliases: ["/cozyhosting"]
tags: ["HTB"]
author: "Zaki"
---

## Intro

Write-up for the CozyHosting HackTheBox machine

---

## Walkthrough

Once connected to the box I started by running an nmap scan to identify any active services on the machine

```bash
 nnmap -sC -sV -oA cozyhosting-nmap 10.10.11.224
```

> sC : default scripts

> sV : enumerate versions

> -oA : output all formats

### Initial Foothold

We are met with a login page.
![a](https://raw.githubusercontent.com/swisskyrepo/PayloadsAllTheThings/master/.github/banner.png)
Inspecting the pages source we learn that it uses a bootstrap template. Searching the version and template name leads us to the discovery that it uses the spring boot framework. At this point I decided to perform directory enumeration using a springboot supported wordlist. Fortunately, SecLists has a pretty robust wordlist for this

```bash
gobuster dir -w /usr/share/wordlists/seclists/Discovery/Web-Content/spring-boot.txt -u http://cozyhosting.htb
```

We have now revealed several endpoints. Immediately I am intrigued by the `/actuator/sessions` endpoint
Sending a curl request reveals a user and a session token

```bash
CURL -x get /actuator/sessions
```

Replacing our cookie with the newly discovered token allows us to access the admin panel
Playing around with the input fields, we receive an error regarding improper ssh syntax. This tells us that there may be a command injection vulnerability. We now need to figure out a way to escape the ssh command and setup a reverse shell. [Revshells](https://www.revshells.com) is an invaluable resource in crafting reverse shells. Following this, I setup a netcat listener and add the port to the reverse shell payload.

```bash
nc -lvnp 4444
```



### User Flag

We now have access to the machine. First, I like to check if the server has python- one reason being so that we can upgrade our shell pretty easily.

```python
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

Listing the files in the directory reveals a .jar executable. I then start a python server and download the file

```python
python3 -m http.server 8083
```

Opening the .jar we discover a few .properties files and and some credentials to a psql server. After logging into the PSQL we can search through the tables where we eventually find a name and hash, key:pair value. Passing the hash into johntheripper, it tells us that it is of type bcrypt. I then attempt to crack the hash by passing it into john and using the rockyou.txt wordlist.
User flag obtained

### Privilege escalation

Let's check our permissions on the user

```bash
sudo -l
```

It appears the user has sudo permissions on ssh. Querying this on [GTFOBins](https://gtfobins.github.io/gtfobins/ssh/) we can find a payload to obtain root user.

```bash
sudo ssh -o ProxyCommand=';sh 0<&2 1>&2' x
```

---
