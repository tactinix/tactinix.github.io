---
title: Vulnhub Hemisphere Gemini
author: tactinix
date: 2020-11-21
categories: [Walk-throughs]
tags: [Vulnhub-Boxes]
math: true
mermaid: true
---

Gemini is part of the Hemisphere series of VM's by d4t4s3c found on Vulnhub. It's classed as a easy machine with an emphasis on enumeration of a web application followed by some Linux privilege escalation.

I'm running the VM in Virtualbox on a Kali host with a bridged network.

## Enumeration

Starting with nmap:
```bash
# Nmap 7.91 scan initiated Fri Nov 20 04:54:04 2020 as: nmap -p- -sV -sC -oA nmap.ap 192.168.178.75
Nmap scan report for gemini.############ (192.168.178.83)
Host is up (0.000085s latency).
Not shown: 65530 closed ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 3.0.3
22/tcp  open  ssh         OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 a3:38:0e:b6:a1:b8:49:b1:31:a0:43:3e:61:c3:26:37 (RSA)
|   256 fc:40:6c:0b:7b:f0:03:6e:2e:ef:2d:60:b5:96:01:b6 (ECDSA)
|_  256 90:ed:89:27:9d:65:ea:80:54:79:65:af:2c:d7:80:43 (ED25519)
80/tcp  open  http        Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Gemini Corp
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.9.5-Debian (workgroup: WORKGROUP)
MAC Address: 08:00:27:5E:09:52 (Oracle VirtualBox virtual NIC)
Service Info: Host: GEMINI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -19m59s, deviation: 34m38s, median: 0s
|_nbstat: NetBIOS name: GEMINI, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.9.5-Debian)
|   Computer name: gemini
|   NetBIOS computer name: GEMINI\x00
|   Domain name: \x00
|   FQDN: gemini
|_  System time: 2020-11-20T03:54:18+01:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-11-20T02:54:18
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Nov 20 04:54:20 2020 -- 1 IP address (1 host up) scanned in 15.96 seconds
```
As port 80 is open, I fire up ZAP to poke at Apache and see what it has to offer.
The website is a bit barren with none of the buttons and links really doing anything.


![](/images/img/website.png)

Letting gobuster do its thing in the background with ```raft-medium-directories.txt``` from Seclists, I can start looking for ```robots.txt``` or ```sitemap.xml``` files that might indicate something. The ```robots.txt``` file is there, but not of much use.

![](/images/img/robots.png)

Going back to the gobuster scan, it shows a ```/Portal/``` directory that I can load up in ZAP.
```bash
$ gobuster dir -f -t 100 -x .php -u http://192.168.178.83 -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
/assets/ (Status: 200)
/icons/ (Status: 403)
/images/ (Status: 200)
/Portal/ (Status: 200)
/server-status/ (Status: 403)
```

This brings up a page that, with a little help from Google translate, turns out to be a maintenance notice.

![](/images/img/maintenance.png)

## Foothold

The links at the top of the page don't lead anywhere useful, they just load and display a ```.html``` file. Bingo!

![](/images/img/links.png)

Back in ZAP I open the PHP request in the request editor to look for LFI's like ```bash../../../../../etc/passwd```. 
Sure enough, it's a hit.

![](/images/img/passwd.png)

There seems to be only one user on the box.

```bash
william:x:1000:1000:william,,,:/home/william:/bin/bash
```

Now we have a potential username to enumerate the other services on the box with.

To go after SSH with only a username and not wiff of a valid password, I've only found frustrating in the past. Bruteforcing with something like Hydra will take **a.very.long.time**, and then some, so giving that a skip, the SMB service is next.


![](/images/img/smbmap.png)

Unfortunately that does not turn up much, so it looks like it's going the be SSH then. Like I said, bruteforcing SSH with a list like ```bash rockyou.txt``` will take forever. There is also not enough "meat" on the site's pages to use a tool like ```cewl``` to put a password list together. So, going back to the LFI in ZAP, perhaps there are ssh keys lying around in the user's ```~/```.

![](/images/img/keys.png)

Perfect!
Using the above key, I ssh into the ```william``` account.

![](/images/img/contact.png)

## Privilege Escalation

First thing to do is get LinPeas running

![](/images/img/lp.png)

The output from LinPeas shows me the path to root.


![](/images/img/root.png)

Thanks for reading and have loads of fun!

tactinix
