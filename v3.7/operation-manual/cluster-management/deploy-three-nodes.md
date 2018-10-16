---
title: 三节点高可用部署方案
summary: 介绍三节点的高可用部署方案，达到第二级高可用
toc: true
toc_not_nested: true
asciicast: true
---

<div id="toc"></div>

##一、基本平台部署

部署单管理节点、双计算节点的rainbond平台：

- 安装单节点云帮平台：

```bash
# 公网环境(阿里云，腾讯云等云上环境)可以指定公网ip grctl init --eip <公网ip>
wget https://pkg.rainbond.com/releases/common/v3.7.2/grctl
chmod +x ./grctl
./grctl init --role master
```

- 扩容计算节点：

```bash
grctl node add --host compute01 --iip <internal ip> -p <root pass> -r worker
grctl node add --host compute02 --iip <internal ip> -p <root pass> -r worker
```

## 二、部署Glusterfs

在计算节点部署双节点GFS集群：

- 安装GFS：

  - 详情参见：[GlusterFS安装]( https://www.rainbond.com/docs/stable/operation-manual/storage/GlusterFS/install.html)

> 注意：将计算节点作为存储节点，需要将上方文档中的 server1、server2 更换为 compute01、compute02

- 切换存储

为管理节点安装GFS文件系统

```bash
yum install -y centos-release-gluster
yum install -y glusterfs-fuse
```
将管理节点的/grdata目录写入GFS

```bash
mount -t glusterfs compute01:data /mnt
cp -a /grdata/. /mnt
umount /mnt
```
编辑所有节点的/etc/fstab,新增一行：

manage01 & compute01:

```bash
compute01:/data	/grdata	glusterfs	backupvolfile-server=compute02,use-readdirp=no,log-level=WARNING,log-file=/var/log/gluster.log 0 0
```

compute02:

```bash
compute02:/data	/grdata	glusterfs	backupvolfile-server=compute01,use-readdirp=no,log-level=WARNING,log-file=/var/log/gluster.log 0 0
```

重新挂载

```bash
mount -a
```
## 三、配置本地主机解析

- 修改三个节点的/etc/hosts

  ```bash
  vi /etc/hosts
  #修改如下
  <manage01 IP> manage01 <host_UUID> kubeapi.goodrain.me region.goodrain.me console.goodrain.me
  
  <VIP> goodrain.me lang.goodrain.me maven.goodrain.me
  ```

## 四、配置VIP

在两个计算节点配置VIP，搭建基于keepalived软件的主备机制

- 安装keepalived

```bash
yum install -y keepalived
```


- 修改配置文件

```bash
#备份原有配置文件
cp /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf.bak
#编辑配置文件如下
#compute01
! Configuration File for keepalived

global_defs {
   router_id LVS_DEVEL
}

vrrp_instance VI_1 {
    state MASTER   #角色，当前为主节点
    interface ens6f0  #网卡设备名，通过 ifconfig 命令确定
    virtual_router_id 51
    priority 100   #优先级，主节点大于备节点
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        <VIP>
    }
}

#compute02
! Configuration File for keepalived

global_defs {
   router_id LVS_DEVEL
}

vrrp_instance VI_1 {
    state BACKUP   #角色，当前为备节点
    interface ens6f0   #网卡设备名，通过 ifconfig 命令确定
    virtual_router_id 51
    priority 50   #优先级，主节点大于备节点
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        <VIP>
    }
}


#启动服务，设置开机自启动
systemctl start keepalived
systemctl enable keepalived
```

- 切换应用域名解析IP到VIP

```bash
#在manage01节点执行
grctl domain --ip <VIP>
```

- 修改tcpdomain

```bash
#在manage01节点执行
din rbd-db   #进入rbd-db组件
mysql        #进入数据库
use console; #使用console数据库
UPDATE region_info set tcpdomain="<VIP>"; #更新tcpdomain
```

## 五、部署完成后的引导

平台部署完成后，下面的文章可以引导你快速上手Rainbond。

<div class="btn-group btn-group-justified">
<a href="/docs/stable/getting-started/quick-learning.html" class="btn" style="background-color:#F0FFE8;border:1px solid #28cb75">快速上手</a>
</div>