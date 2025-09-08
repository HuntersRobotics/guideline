---
title: "[[ROS2常用配置]]"
tags:
  - ros2
author: kaylor
date_creation: 2025-09-08
aliases:
---
## 更换DDS为cycloneDDS
- 安装cyclonedds
```bash
sudo apt install ros-humble-rmw-cyclonedds-cpp ros-humble-cyclonedds
```

- 配置cyclonedds
```bash
sudo mkdir -pv /etc/ros/dds/
sudo touch /etc/ros/dds/dds/cyclonedds.xml
sudo touch /etc/ros/dds/dds/service-environment.conf
```

使用vim或者gedit编辑配置文件，xml配置如下
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<CycloneDDS xmlns="" xmlns:xsi="" xsi:schemaLocation=" ">
  <Domain id="any">
    <General>
      <Interfaces>
        <NetworkInterface name="eth0" />
      </Interfaces>
      <AllowMulticast>spdp</AllowMulticast>
      <DontRoute>true</DontRoute>
    </General>
  </Domain>
</CycloneDDS>
```
> 注意网络接口这里，需要这是一个你需要在这个网口上使用ROS2的接口，并且这个接口需要时up的状态，否则会报错

service-environment.conf的作用是我们使用service服务的时候的公共配置，内容如下：
```config
[Unit]
After=network.target
Requires=systemd-networkd.service
After=systemd-networkd.service

[Service]
Environment=ROS_HOME=/root/.ros
Environment=ROS_DISTRO=humble
Environment=ROS_LOCALHOST_ONLY=0
Environment=ROS_PYTHON_VERSION=3
Environment=ROS_VERSION=2
Environment=PYTHONPATH=/opt/ros/humble/lib/python3.8/site-packages:/opt/ros/humble/lib/python3.10/site-packages:/opt/ros/humble/local/lib/python3.10/dist-packages
Environment=AMENT_PREFIX_PATH=/opt/ros/humble
Environment=PATH=/opt/ros/humble/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
Environment=LD_LIBRARY_PATH=/opt/ros/humble/opt/rviz_ogre_vendor/lib:/opt/ros/humble/lib/aarch64-linux-gnu:/opt/ros/humble/lib:/usr/local/lib/:/opt/ros/humble/lib/x86_64-linux-gnu
Environment=RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
Environment=CYCLONEDDS_URI=file:///etc/ros/dds/cyclonedds.xml
Environment=ROS_LOG_DIR=/tmp/ros
# Enable coredump
LimitCORE=infinity
Environment=ROS_DOMAIN_ID=101

Environment=ROS2_SYSTEMD_LOG_ENABLE=true
Environment=SPDLOG_STDOUT=false
Environment=SPDLOG_SYSTEMD=true
Environment=SPDLOG_LEVEL=info
Environment=RCUTILS_CONSOLE_OUTPUT_FORMAT="[{severity}] [{name}]:{message}"
StandardError=null
StandardOutput=null
```

> 请注意这里的ROS DOMAIN ID，短ID的上限就是101

终端中设置环境变量
```bash
export RMW_IMPLEMENTATION=rmw_cyclonedds_cpp
export CYCLONEDDS_URI=file:///etc/ros/dds/cyclonedds.xml
export ROS_DOMAIN_ID=101
```

