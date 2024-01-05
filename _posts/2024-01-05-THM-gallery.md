---
title: TryHackMe - Gallery
date: 2024-01-05 16:00:00 +0800
categories: [TryHackMe]
tags: [writeups]
---

![gallery](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/gallery.png)

Let's begin our hacking with reconnaissance. We use Nmap to scan the open port.

Vulnerable Machine IP: 10.10.124.102

```
nmap -sC -sV 10.10.124.102
```

![nmap](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/nmap.png)

There are 2 open port which both of them are http. The port 80 lead to Apache2 Ubuntu Default Page. The port 8080 redirect us /gallery/login.php.

![login](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/login.png)

We can try simple SQL injection to check if this login page vulnerable.
Payload: `' OR 1=1-- -`

And it seem the login page are vulnerable to SQL injection :D.

![dashboard](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/dashboard.png)

Now let's enumerate to see if there are useful directory here. And we found admin profile form that we can upload avatar. Can we try upload malicious file to make rce? Let's try it.

![form](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/profile_picture.png)

`http://10.10.124.102/gallery/uploads/1629883080_1624240500_avatar.png`

We can access the profile picture in gallery/uploads/. So that's mean the profile picture are accessible. Let's upload .php file.

![upload](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/success_upload.png)

Umm yeah, so we can just upload php file I guess. Is it executable?

![cmd](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/cmd.png)

Walla, we can rce. Now let's upload reverse shell file to get reverse shell. We can use pentest monkey php reverse shell. It should do the work. You know the drill, create listener, edit the php file and upload the file to the server and bam we got reverse shell.

![reverse shell](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/reverse_shell.png)

Now let's stable our shell first. This command will help you to stable the shell.

```
python3 -c 'import pty; pty.spawn("/bin/bash")'
export SHELL=bash
export TERM=xterm

ctrl + z

stty raw -echo;fg
reset
```

The task need us to give hash password for admin user. It take me sometimes to find the password for admin user. The tips is we need to check every file in the /var/www/html to get the information. And in initialize.php there have a credentials that use to connect to database.

![initialize](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/initialize.png)

We can use the username to login into mysql and probably get the admin password hash.

![hash](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/password_hash.png)

Next, let's enumerate to make privilege escalation to higher user. First let's check what user are available in this machine. We can check in /home or /etc/passwd

`mike:x:1001:1001:mike:/home/mike:/bin/bash`

Actually I take a lot of time to find something that can be use to privilege escalation. I even use Linpeas to help the enumeration process. I thought there are nothing to do. After carefully view Linpeas, we can found interesting readable file.

![readable file](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/readable.png)

Looks, mike make backup folder in /var/backups. And we also got his password in .bash_history. It seem we can ssh into mike now using the leak password. Silly mike XD.

![user flag](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/user_txt.png)

Also mike can use sudo, let's check sudo permission given to mike using sudo -l.

```
User mike may run the following commands on gallery:
    (root) NOPASSWD: /bin/bash /opt/rootkit.sh
```

It's look like mike can run bash script at /opt/rootkit.sh. Let's take a look in the file.

![rootkit](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/rootkit.png)

The rootkit.sh will give user 4 choice by insert the answer. But there are something I thing we can use to exploit into root. The read function will open /root/report.txt using nano. And there are ways to privesc using nano. Let's find it using GTFOBins.

We can obtain root shell by run the script **using sudo** and choose read, this action will open nano, to exploit it, type CTRL + R, CTRL + X, then type `reset; bash 1>&0 2>&0`. Now we obtain the root shell.

![get root](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/THM_Gallery/we_root.png)


> What I learn today?
> Check every file and Linpeas output. It may have some useful information.



 


