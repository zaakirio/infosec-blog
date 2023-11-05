---
title: "CozyHosting | HTB Write-Up"
summary: Write-up for the 'CozyHosting' HackTheBox
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

 `nnmap -sC -sV -oA cozyhosting-nmap 10.10.11.224`

> sC : default scripts

> sV : enumerate versions

> -oA : output all formats


### Initial Foothold

We are met with a login page. Inspecting the pages source we learn that it uses a bootstrap template. Searching the version and template name leads us to the discovery that it uses the spring boot framework. At this point I decided to perform directory enumeration using a springboot supported wordlist. Fortunately, SecLists has a pretty robust wordlist for this
```bash
gobuster dir -w /usr/share/wordlists/seclists/Discovery/Web-Content/spring-boot.txt -u http://cozyhosting.htb
```
We have now revealed several endpoints. Immediately I am intrigued by the ```/actuator/sessions``` endpoint
Sending a curl request reveals a user and a session token
```bash
CURL -x get /actuator/sessions
```
Replacing our cookie with the newly discovered token allows us to access the admin panel
Playing arounddd with te input filds we see that the connection settings sends a direct request tot he server. We now need to figure out a way to escape the ssh command and setup a reverse shell. revee shell website was useful in crafting a endpoint. I setup a netcat listener and add the port to the reverse shell payload.

### User Flag
We now have access to the machine. First, I like to check if the server has python so that we can setup an elevated shell. This is a useful reference for elevatred shells:<>link
I then start a python server and download the file. Opening the .jar we discover a few .properties files and and some credentials to a psql server. Logging into the PSQL we can search through the tables to find a user hash. Popping this string into john allows us to crack the bcrypt hash, we use the rockyou.txt wordlist for this.
User flag obtained
### Privilege escalation
Let's check our permissions on the user
```bash
sudo -l
```
It appears the user has sudo permissions on ssh. Querying this on gtfobins we can find a payload to obtain root user.
```bash
sudo ssh -o ProxyCommand=';sh 0<&2 1>&2' x
```
---
