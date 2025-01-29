---
title: TryHackMe - BoilerCTF
date: 2023-09-25 17:00:00 +0800
categories: [TryHackMe]
tags: [writeups]
---


![icon](/assets/image/2023-09-25-THM-boilerctf/icon.jpeg)

This is the medium TryHackMe room that really test my enumeration skills. Let's start by connecting the VPN and look at the machine.

## Enumeration

We start our enum like always by using Nmap to scan the open port. At first scan we get 3 common port that are 21 (FTP), 80 (HTTP), and 10000 (HTTP).

![nmapscan](/assets/image/2023-09-25-THM-boilerctf/nmapscan.png)

But based on TryHackMe question, there are one more port that available on high port, so to make scanning faster, we will use Rustscan. It seems there are open port on 55007. Let's check the version.

`rustscan 10.10.208.180 -- -p- -Pn`

![rustscan](/assets/image/2023-09-25-THM-boilerctf/rustscan.png)

![portscan](/assets/image/2023-09-25-THM-boilerctf/portscan.png)

So it was SSH port. Good to know that. Now let's go through all the port and see if there are some info that we can get.

### FTP - Port 21

![ftp](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/ftp.png)

We can login the ftp as anonymous, by listing the file, there are only one hidden file called .info.txt. Now let's get the file and see what inside it.

![info](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/info.txt.png)

Wow, a cipher text. Meh, it was rot13 and rabbit hole for us.

![rh1](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/rh1.png)


### HTTP - Port 80

Next step, let's open the browser and enumerate the website on both port. First we can fuzz the directory of this page.

![page1](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/page1.png)

![dirsearch1](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/dirsearch1.png)

There are robots.txt with some directory that are RABBIT HOLE. In /joomla/ directory, it show that there are Joomla CMS was deploy on this machine. For more info we can fuzz the /joomla/ directory also.

![dirsearch](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/dirsearch2.png)

Wooo, the joomla directory contain many other directory that we need to check. But before that let's check port 10000 first.

### HTTP - Port 10000

This http port serve Webmin for this machine. And it was another RABBIT HOLE for us. Now let just enumerate more on Joomla.

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/webmin.png)

## Joomla Enumeration

Out of all the directory that we fuzz earlier, I found one directory that may be potential to be exploit. It was `/_test/`. The page is sar2html program. So, we can search if there are any exploit available for this applicaiton.

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/sar2html.png)

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/sar2html_exploit.png)

We found the exploit for sar2html and it was RCE https://www.exploit-db.com/exploits/47204. Nyum nyum, so by using parameter ?plot= on index.php, we can inject code to be execute from the server.

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/rce_test.png)

Bingo, so we can get rce from this, but unfortunately, we cannot get reverse shell. So let see if there are file that helpful for us.

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/rce_ls.png)

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/rce_log.png)

Ahh, sweet information we have here. Based on the log.txt, we got some credentials that can be use and its look like SSH as it use on port 22 before. 

## User Privilege Escalation 

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/sshbasterd.png)

Yippie, we got into the shell. Now let's privesc to get better user. In besterd home directory, there are file name backup.sh. In this file, there are leak credentials called stoner and password. So, we can use this creds to escalate our privileges.

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/backupsh.png)


![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/sshstoner.png)

## Root Privilege Escalation 

We successfully SSH into stoner, now it's time for enumeration to get root privilege. First I try using sudo and got smack by Rabbit Hole. Ugh.

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/rh2.png)

Next, I check SUID if there are file that can be use using SUID permission. We can use this command to find the SUID permission that are available in this machine.

`find / -perm -u=s -type f 2>/dev/null`

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/suid.png)

Sweet, there are one SUID permission that can be use to exploit to get root shell. It was /usr/bin/find. By using find, we can abuse the -exec function to execute root shell.

`find . -exec /bin/bash -p \; -quit`

![](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/thm_boilerctf/root.png)

And now we got the root privileges. Yahoo!!!