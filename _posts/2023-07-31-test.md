---
title: Docker Installation In Ubuntu
date: 2023-07-31 13:30:00 +0800
categories: [Tutorial]
tags: [docker]
---

1. Update repo and install dependencies
```sh
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg lsb-release
```

2. Add docker official gpg key
```sh
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

3. Setup docker repo
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

4. Update repo
```bash
sudo apt update
```

5. Install docker
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin docker-compose
```

6. Check if docker work
```bash
sudo docker run hello-world
```

***
# Manage docker as un-root

1. Create docker group
```bash
sudo groupadd docker
```

2. Add user to group
```bash
sudo usermod -aG docker $USER
```

3. Restart VM

***
# Docker on Startup

```bash
sudo systemctl enable docker.service
sudo systemctl enable containerd.service
```

***
