---
title: "[[在Linux上使用Docker安装Windows]]"
tags: ["Windows"]
author: kaylor
date_creation: 2025-08-12
aliases:
---

## 前置安装
使用docker安装windows，需要先安装Docker。Docker安装如下：
- 更新apt，安装docker需要的依赖
```bash
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release
```
- 添加Docker安装源
```bash
sudo mkdir -p /etc/apt/keyrings  
curl -fsSL https://mirrors.huaweicloud.com/docker-ce/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://mirrors.huaweicloud.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
- 安装docker
```bash
sudo apt update  
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

## 安装Windows
在Docker上安装docker，使用的是开源仓库[dockur/windows](https://github.com/dockur/windows)的方法，需要详细了解可以去访问上述开源仓库
### 拉取镜像
```bash
docker pull dockurr/windows
```
### 配置文件和安装
-  建立一个windows10的文件夹和共享路径
```bash
mkdir windows10
cd windows10
mkdir shared # 这个是宿主机和windows共享的文件夹路径
touch docker-compose.yaml # 生成一个空的配置文件
docker network create --driver=bridge --subnet=192.168.10.0/24 --gateway=192.168.10.1 windows-bridge # 生成一个docker的网桥，专门给windows docker使用
```
- 配置文件说明
```yaml
networks:
  windows-bridge:
    external: true
    name: windows-bridge
services:
  windows:
    image: dockurr/windows
    container_name: windows10
    networks:
      windows-bridge:
        ipv4_address: 192.168.10.2
    entrypoint:
      - /bin/sh
      - -c
      - |
        ip route del default
        tini -s /run/entry.sh
    environment:
      TZ: Asia/Shanghai
      VERSION: "10"
      DISK_SIZE: "256G"
      RAM_SIZE: "20G"
      CPU_CORES: "10"
      USERNAME: "hunter"
      PASSWORD: " " # 密码是空格
    devices:
      - /dev/kvm
      - /dev/net/tun
      - /dev/bus/usb
      - /dev/vfio
    cap_add:
      - NET_ADMIN
      - IPC_LOCK
    ports:
      - 8006:8006
      - 3389:3389/tcp
      - 3389:3389/udp
    volumes:
      - ./windows10:/storage
      - ./shared:/data
    #restart: always
    stop_grace_period: 2m

```

>  1. 上面使用的配置文件设置了windows版本是10， 用户名是hunter， 密码是一个空格
>  2. 注意entrypoint这个选项卡，如果删除那6行，那么windows就可以上网。**第一次启动的时候需要删除这6行**，因为需要联网下载windows镜像
>  3. 8006 是使用 127.0.0.1:8006可以访问安装进度
>  4. 3389是 RDP的端口，可以使用远程访问软件连接

- 启动和关闭Windows
```bash
# 启动之前需要cd到 跟docker-compose.yaml的文件同目录下
docker compose up -d --build # 启动windows10 docker
docker compose down # 关闭清理windows10 docker
```
> 1. 启动之后可以通过浏览器的 127.0.0.1:8006访问windows， 如果是第一次启动，会显示安装进度，但是不建议使用这个界面操作windows，因为会延迟卡顿
> 2. 关闭windows的时候，建议先在windows中关闭，然后再执行关闭的命令
### 安装远程桌面
```bash
sudo apt install remmina remmina-plugin-rdp
```

安装完毕之后，可以在app中找到该软件，然后连接127.0.0.1:3389即可
