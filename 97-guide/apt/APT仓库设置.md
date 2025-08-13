---
title: "[[APT仓库设置]]"
tags: ["apt"]
author: kaylor
date_creation: 2025-08-13
aliases:
---
## 添加实验室的apt源
```bash
wget https://apt.hunters-tech.com/kaylor-siat-pub.gpg
sudo mkdir -pv /etc/apt/keyrings
sudo mv kaylor-siat-pub.gpg /etc/apt/keyrings/kaylor-siat-pub.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/kaylor-siat-pub.gpg] https://apt.hunters-tech.com/siat siat main" | sudo tee /etc/apt/sources.list.d/siat.list > /dev/null
```


## 安装软件包
### LIO-SAM_MID360_ROS2
```bash
sudo apt update
sudo apt install -y libgtsam-dev libgtsam-unstable-dev liblivox-sdk2-dev ros-humble-lio-sam
```