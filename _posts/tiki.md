---
title: Vulnhub Tiki 1
author: tactinix
date: 2021-01-17
categories: [Walk-throughs]
tags: [Vulnhub-Boxes]
math: true
mermaid: true
---



This box centers around a remote authentication bypass in Tiki Wiki CMS Groupware 21.1.

## Enumeration

Starting of with nmap -

```
nmap -sC -sV -oA nmap ip-adress
```
![](/images/img/tiki/nmap_results.png)

## Services running on the box
On port 80 is the default Ubuntu Apache "It works" page.

![](/images/img/tiki/firefox1.png)

The robots.txt file points to the /tiki installation 

![](/images/img/tiki/robots.txt.png)

Looking at that presents the tiki deafalt page
![](/images/img/tiki/tiki_homepage.png)

Trying the usual admin:admin fails;
![](/images/img/tiki/admin_admin.png)

 So onwards to Google, which points to the exploit listed on exploitdb found at https://www.exploit-db.com/exploits/48927 reported in August 2020.

Before running the exploit, let's take a look at the smb share on the box with smbmap.

![](/images/img/tiki/smbm.png)

On the ```Notes``` share we can find credentials for the ```silky``` user;
![](/images/img/tiki/smb_cat_Mail.txt.png)

But before the found credentials are put into use, let have a look at the exploit.
![](/images/img/tiki/exploit_output.png)

As the output suggested, login with ```admin``` and no password. In Zap, which is my preferred tool, this can easily be done by initiating a login in the proxied Firefox session, setting breakpoints in Zap and editing the input as desired.

![](/images/img/tiki/zap_clear_pass2.png)

Stepping through the breakpoints, we hit the admin page.
![](/images/img/tiki/gain_admin.png)

Browsing the interface, a page listing credentials are found;
![](/images/img/tiki/credential_page.png)

## Capture the flag
As the box is also running Openssh, might be worth a try;
![](/images/img/tiki/silky_home.png)
Whaddayaknow, the user ```silky``` is in the ```sudo``` group.

![](/images/img/tiki/root_flag.png)
 

Have a great day!  
tactinix
