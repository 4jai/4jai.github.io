---
title: Installing Domain Controller
date: 2025-03-02 14:00:00 +0800
categories:
  - active directory
tags:
  - tutorial
---
Hello guys, just my notes in setup Windows Active Directory 2019 Domain Controller. We will be using VMware Workstation pro. First of all, let's setup fresh Windows 2019 server. Our planning is just to create simple architecture like below.

![](/assets/img/2025-03-02-Installing-Domain-Controller/AD_draw.jpg)

Active Directory (AD) is a Microsoft directory service that manages and organizes users, computers, and resources in a network. It provides authentication, authorization, and centralized management through domain controllers, enabling IT administrators to enforce security policies, manage access, and streamline user account management. AD is commonly used in enterprise environments to facilitate single sign-on (SSO), group policies, and role-based access control (RBAC) across an organization's networked devices and applications.
# First Setup

![](/assets/img/2025-03-02-Installing-Domain-Controller/fresh_install.png)

Now with fresh baked Windows 2019 Server. Change the **Computer Name** and set the IP address.

![](/assets/img/2025-03-02-Installing-Domain-Controller/rename_pc.png)

![](/assets/img/2025-03-02-Installing-Domain-Controller/ip_addr.png)

After setting up PC name and IP address, we need to restart our server.

# Installing AD services

Open Server Manager and click Add Roles and Features

![](/assets/img/2025-03-02-Installing-Domain-Controller/add_roles.png)

Just click next in first page, choose Role-based or feature-based installation.

![](/assets/img/2025-03-02-Installing-Domain-Controller/role_based.png)

Click next and select our server to install the roles and features. Choose **Active Directory Domain Services** and click next.

![](/assets/img/2025-03-02-Installing-Domain-Controller/role_select.png)

We don't choose any features to be added, so we can just click next. Click next one more time in AD DS informational page. Then click Install to install our active directory services. Now the server has Active Directory services installed.

![](/assets/img/2025-03-02-Installing-Domain-Controller/confirm_install.png)

# Promote Server to Domain Controller

In server management, there will be notification that we need to do Post-Deployment configuration. We need to promote the server to Domain Controller.

![](/assets/img/2025-03-02-Installing-Domain-Controller/promote_server.png)

Let's create new forest for our AD environment. 

In **Active Directory (AD)**, a **forest** is the highest level of the logical structure and consists of one or more **domains** that share a common **schema, configuration, and Global Catalog** but operate independently. A forest provides a security boundary, meaning objects in one forest are not accessible to another forest unless explicitly allowed through **trust relationships**.

A forest typically contains:
- **Domains** (e.g., `company.com`, `sales.company.com`)
- **Trees** (hierarchically structured domains)
- **Global Catalog** (a searchable index of directory objects across domains)

I create forest with domain name **training.local**. Then click Next.

![](/assets/img/2025-03-02-Installing-Domain-Controller/create_forest.png)

Next, Set DSRM password. Then click Next.

The **Directory Services Restore Mode (DSRM) password** is a special password set during the installation of a **Domain Controller (DC)** in Active Directory. It is used to log in to the server when it is booted into **DSRM**, a special troubleshooting mode that allows administrators to repair or restore the **Active Directory database (NTDS.dit)** if it becomes corrupted or inaccessible.

This password is crucial for disaster recovery scenarios and should be securely stored, as it provides direct access to restore or modify Active Directory offline.

![](/assets/img/2025-03-02-Installing-Domain-Controller/dsrm.png)

Confirm our NetBIOS name. Then click Next.

In an **Active Directory (AD) environment**, the **NetBIOS name** is often a **shortened version of the domain name** (e.g., for `company.com`, the NetBIOS name might be `COMPANY`). It is primarily used for backward compatibility with legacy systems and applications that rely on **NetBIOS over TCP/IP (NBT)**.

![](/assets/img/2025-03-02-Installing-Domain-Controller/netbios.png)

For the Paths, we will stay with default options, click Next. Review our options then click Next. Finally, we can confirm our prerequisites check and install Domain Controller.

![](/assets/img/2025-03-02-Installing-Domain-Controller/install_dc.png)

Our server will restart and our admin already in our domain. Now, login to our Domain Controller and check out many administrative tools for our Active Directory training.

![](/assets/img/2025-03-02-Installing-Domain-Controller/admin_login.png)

