---
title: Active Directory - Configure Login Banner
date: 2025-03-12 21:00:00 +0800
categories:
  - Active Directory
tags:
  - tutorial
---
Woah stop there, you cannot enter restricted area as you like. That's what you want to tell a person who try to login into your domain right? Less talking, let's configure this.

First, let's open GPO management.

![](/assets/img/2025-03-12-Configure-Login-Banner/gpmgmt.png)

In GPO management, select our Domain Forest >> Select our Domain, you may choose if you GPO want to be configure to entire objects in your domain or just Organizational Units (OU). For this time, I configure for domain leet.local. 

![](/assets/img/2025-03-12-Configure-Login-Banner/gpmgmt2.png)

Right click on the domain and choose "Create a GPO in this domain, and Link it here...", this will create new GPO that will link to our domain.

![](/assets/img/2025-03-12-Configure-Login-Banner/creategp.png)

Rename your GPO with proper name. Then click OK

![](/assets/img/2025-03-12-Configure-Login-Banner/gpname.png)

You will see new GPO created under your domain. Right click the GPO and choose "Edit". It will show Group Policy Management Editor.

![](/assets/img/2025-03-12-Configure-Login-Banner/gpedit.png)

Now for the tricky part. To navigate to the banner options, in your GPO, select:

**Computer Configuration >> Policies >> Windows Settings >> Security Settings >> Local Policies >> Security Options**

![](/assets/img/2025-03-12-Configure-Login-Banner/gptree.png)

You will see many list of Not Defined policy. To configure Login Banner, you need to configure two of below policies:

- Interactive logon: Message text for users attempting to log on
- Interactive logon: Message title for users attempting to log on

![](/assets/img/2025-03-12-Configure-Login-Banner/gpbanner.png)

Right click the policy and choose "Properties". Tick "Define this policy setting" and write your message and title for the banner.

**Example Title:**
![](/assets/img/2025-03-12-Configure-Login-Banner/titleedit.png)

**Example Text:**
![](/assets/img/2025-03-12-Configure-Login-Banner/textedit.png)

Save up the policy and walla.

![](/assets/img/2025-03-12-Configure-Login-Banner/successbanner.png)