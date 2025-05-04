---
title: TryHackMe - Attacktive Directory
date: 2025-04-26 01:00:00 +0800
categories:
  - Active Directory
  - TryHackMe
tags:
  - writeups
---
みなさんこんにちは

Today I in the mood to learn Active Directory attacking. So, let's go. We will do Attacktive Directory room in TryHackMe today.

https://tryhackme.com/room/attacktivedirectory

Let's try to answer all the question

> What tool will allow us to enumerate port 139/445?

**Answer:** enum4linux

I actually thinking what tools that we need to answer, it actually **enum4linux**. Enum4linux is an enumeration tool capable of detecting and extracting data from Windows and Linux operating systems, including those that are Samba (SMB) hosts on a network.

But it seems I don't find anything useful using enum4linux. Let just nmap.

![](/assets/img/2025-04-26-THM-Attacktive-Directory/nmap.png)

From the nmap scan we know few useful information:
- NetBIOS_Domain_Name: THM-AD
- DNS_Domain_Name: spookysec.local
- DNS_Computer_Name: AttacktiveDirectory.spookysec.local

> What is the NetBIOS-Domain Name of the machine?

**Answer:** THM-AD

> What invalid TLD do people commonly use for their Active Directory Domain?

**Answer:** .local

***

Next we given list of username and password. In this task, we need to enumerate domain to find available user. First, we may add computer name in /etc/hosts. We need to find the kerberos hash for user.

> What command within Kerbrute will allow us to enumerate valid usernames?

**Answer:** userenum

![](/assets/img/2025-04-26-THM-Attacktive-Directory/kerbrute.png)

> What notable account is discovered? (These should jump out at you)

**Answer:** svc-admin

> What is the other notable account is discovered? (These should jump out at you)

**Answer:** backup

![](/assets/img/2025-04-26-THM-Attacktive-Directory/kerbrute_user.png)

***

Now we got our target, next we need to retrieve the Kerberos ticket from the user. We will use Impacket GetNPUsers tool.

![](/assets/img/2025-04-26-THM-Attacktive-Directory/impacketgetnpusers.png)

> We have two user accounts that we could potentially query a ticket from. Which user account can you query a ticket from with no password?

**Answer:** svc-admin

![](/assets/img/2025-04-26-THM-Attacktive-Directory/getnpuserssvcadmin.png)

We got the Kerberos hash. Now put in the txt file for crack. Based on research we know that the hash is Kerberos 5 AS-REP etype 23, same the format as hash that we retrieve.

> Looking at the Hashcat Examples Wiki page, what type of Kerberos hash did we retrieve from the KDC? (Specify the full name)

**Answer:** Kerberos 5 AS-REP etype 23

> What mode is the hash?

**Answer:** 18200

![](/assets/img/2025-04-26-THM-Attacktive-Directory/hashtype.png)

> Now crack the hash with the modified password list provided, what is the user accounts password?

**Answer:** management2005

![](/assets/img/2025-04-26-THM-Attacktive-Directory/crackkerberos.png)

***

> What utility can we use to map remote SMB shares?

**Answer:** smbclient

**smbclient** is a command-line tool that lets you access shared folders and files on a Windows machine (or any server using SMB/CIFS protocol) from Linux or Unix systems.

Basic Usage:

List share

```
smbclient -L //server -U username
```

Browse the share

```
smbclient //server/share -U username
```

![](/assets/img/2025-04-26-THM-Attacktive-Directory/smbclient.png)

> Which option will list shares?

**Answer:** -L

> How many remote shares is the server listing?

**Answer:** 6

> There is one particular share that we have access to that contains a text file. Which share is it?

**Answer:** backup

> What is the content of the file?

**Answer:** YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw

![](/assets/img/2025-04-26-THM-Attacktive-Directory/smbclientgetfile.png)

> Decoding the contents of the file, what is the full contents?

**Answer:** backup@spookysec.local:backup2517860

![](/assets/img/2025-04-26-THM-Attacktive-Directory/backupcontent.png)

***

Next we will use Impacket secretsdump to retrieve all of the password hashes that this user account (that is synced with the domain controller) has to offer.

![](/assets/img/2025-04-26-THM-Attacktive-Directory/secretsdump.png)

> What method allowed us to dump NTDS.DIT?

**Answer:** DRSUAPI

> What is the Administrators NTLM hash?

**Answer:** 0e0363213e37b94221497260b0bcb4fc

> What method of attack could allow us to authenticate as the user without the password?

**Answer:** Pass The Hash

https://www.crowdstrike.com/en-us/cybersecurity-101/cyberattacks/pass-the-hash-attack/

A **Pass-the-Hash (PtH) attack** is when a hacker steals a **password hash** and uses it to log into a system without knowing the real password. Instead of trying to crack the password, they just send the hash to the server to get access. This works because some systems accept the hash as proof of identity. It’s a quick way for attackers to move around a network once they get a hash from a hacked computer.

> Using a tool called Evil-WinRM what option will allow us to use a hash?

**Answer:** -H

![](/assets/img/2025-04-26-THM-Attacktive-Directory/evilwinrm.png)

[Evil-WinRM](https://github.com/Hackplayers/evil-winrm) is a **hacking tool** used to **connect to Windows machines remotely** using **WinRM** (Windows Remote Management).

***

> svc-admin

**Answer:** TryHackMe{K3rb3r0s_Pr3_4uth}

![](/assets/img/2025-04-26-THM-Attacktive-Directory/svcadminflag.png)

> backup

**Answer:** TryHackMe{B4ckM3UpSc0tty!}

![](/assets/img/2025-04-26-THM-Attacktive-Directory/backupflag.png)

>Administrator

**Answer:** TryHackMe{4ctiveD1rectoryM4st3r}

![](/assets/img/2025-04-26-THM-Attacktive-Directory/adminflag.png)

***

And that how we attack active directory, from this room, I got to learn many flucking tools. so GG.

![](/assets/img/2025-04-26-THM-Attacktive-Directory/completebanner.png)


