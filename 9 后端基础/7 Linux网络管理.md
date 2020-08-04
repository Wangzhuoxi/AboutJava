# 1 Linux网络

Linux支持各种协议类型的网络

- TCP/IP、NetBIOS/NetBEUI、IPX/SPX、AppleTake等
- 在网络底层也支持Ethernet、Token Ring、ATM、PPP（PPPoE）、FDDI、Frame Relay等网络协议。
- 这些网络协议是Linux内核提供的功能，具体的支持情况由内核编译参数决定。

![img](https://segmentfault.com/img/remote/1460000015280395?w=919&h=630)

配置网络参数有两种方式：

- 临时性网络配置：通过命令修改当前内核中的网络相关参数实现，配置后立即生效，重新开机后失效
- 永久性网络配置：通过直接修改网络相关的配置文件实现，需要重启服务，重新开机后保留所有配置



##### 1.1.2关于NAT

NAT英文全称是“Network Address Translation”，中文意思是**“网络地址转换”**，它是一个IETF(Internet Engineering Task Force, Internet工程任务组)标准，允许一个整体机构以一个公用IP（Internet Protocol）地址出现在Internet上。顾名思义，它是一种把内部私有网络地址（IP地址）翻译成合法网络IP地址的技术，如下图所示。因此我们可以认为，NAT在一定程度上，能够有效的解决公网地址不足的问题。

[![clip_image002](https://images0.cnblogs.com/blog/337581/201410/261726025908105.jpg)](https://images0.cnblogs.com/blog/337581/201410/261726021525406.jpg)

# VMWare三种网络连接方式：

　　

如果你想利用VMWare在局域网内新建一个虚拟服务器，为局域网用户提供网络服务，就应该选择桥接模式。

## 桥接：

交换机是VMnet0,还要通过虚拟网桥连接主机网卡。和主机DNS信息完全相同。主机是宿主。可以上网。

![image-20200705181432841](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200705181432841.png)

## Host-only:

无法上网。只能访问主机和其他虚拟机。通过虚拟机VMNet1来连接主机的虚拟网卡（vmnet1，在安装软件时安装的虚拟网卡）

![image-20200705181441912](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200705181441912.png)

## NAT模式：

NAT给分配ip，可以上网。虚拟交换机（VMNet8连接NAT直接连接网络）

![image-20200705181459652](C:\Users\wangdamei\AppData\Roaming\Typora\typora-user-images\image-20200705181459652.png)

网络接口相关**：

- 查看网络接口配置：`ifconfig [ethX]`
- 网络接口的启用与停用：使用 `ifup ethX` 命令来启用指定的接口，使用 `ifdown ethX` 命令来禁用指定的接口

**临时配置相关**：

- `ifconfig`命令可以临时地设置网络接口的IP参数
- `route`命令可以临时地设置内核路由表
- 使用`hostname`命令可以临时地修改主机名
- 使用`sysctl`命令可以临时地开启内核的包转发

使用命令来做网络的临时配置，要做到永久配置就需要直接修改文件的方式了！

![img](https://segmentfault.com/img/remote/1460000015280396)

**网络检测的常用工具：**

- ifconfig 检测网络接口配置
- route 检测路由配置
- ping 检测网络连通性
- netstat 查看网络状态
- lsof 查看指定IP 和/或 端口的进程的当前运行情况
- host/dig/nslookup 检测DNS解析
- traceroute 检测到目的主机所经过的路由器
- tcpdump 显示本机网络流量的状态

# 2 安装软件

一般我们的**Centos下安装软件可以直接使用yum命令来安装**，非常方便。在yum之前还有一个RPM，来看看它的区别：

- rpm是由红帽公司开发的软件包管理方式，使用rpm我们可以方便的进行软件的安装、查询、卸载、升级等工作。但是rpm软件包之间的依赖性问题往往会很繁琐,尤其是软件由多个rpm包组成时。
- Yum（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及SUSE中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软体包，无须繁琐地一次次下载、安装。

## 2.1 yum使用

```
yum  [全局参数] 命令 [命令参数]
```

![img](https://segmentfault.com/img/remote/1460000015280438?w=523&h=357)

常用的全局参数：

- `-y`：对yum命令的提问回答“是（yes）”
- `-C`：只利用本地缓存，不从远程仓库下载文件
- `--enablerepo=REPO`：临时启用指定的名为REPO的仓库
- `--disablerepo=REPO`：临时禁用指定的名为REPO的仓库
- `--installlroot=PATH`：指定安装软件时的根目录，主要用于为chroot环境安装软件

![img](https://segmentfault.com/img/remote/1460000015280439?w=905&h=650)

![img](https://segmentfault.com/img/remote/1460000015280440?w=897&h=593)

## 2.2 常用网络工具

![img](https://segmentfault.com/img/remote/1460000015280441?w=913&h=641)

![img](https://segmentfault.com/img/remote/1460000015280442?w=915&h=637)

