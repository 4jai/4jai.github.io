---
title: Open Lite Speed Web Server Installation In Ubuntu 22.04
date: 2023-12-31 03:36:00 +0800
categories: [Tutorial]
tags: [sysadmin]
---

![icon](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/openlitespeed/openlitespeed.png)

# Update & Upgrade System
```
sudo apt update && sudo apt upgrade
```

# Add repository
```
sudo wget -O - https://repo.litespeed.sh | sudo bash
```

# Update repository
```
sudo apt update
```

# Install OpenLiteSpeed
```
sudo apt install openlitespeed lsphp81
```

# Setup OpenLiteSpeed admin password
```
sudo /usr/local/lsws/admin/misc/admpass.sh
```

# Check services status
```
sudo systemctl status lsws
```

***

## Example Site
```
http://server_domain_or_IP:8088
```

![welcome_page](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/openlitespeed/welcome.png)

## Admin Dashboard
```
https://server_domain_or_IP:7080
```

![admin_page](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/openlitespeed/admin.png)

***

# Additional Configuration

## Changing Default Port

1. Find listeners on the dashboard

![listeners](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/openlitespeed/listeners.png)

2. On listener list, click magnifying glass icon at actions section
![magnifier](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/openlitespeed/magnifier.png)

3. Click edit and change the port number to 80
![edit](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/openlitespeed/edit.png)

![port_change](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/openlitespeed/port_change.png)

4. Restart the OpenLiteSpeed in server to fully reload
```
sudo systemctl lsws restart
```

***

## Default Root Folder Located At **/usr/local/lsws/Example/html**
![root_folder](https://raw.githubusercontent.com/4jai/4jai.github.io/main/_posts/imgs/openlitespeed/root_folder.png)




