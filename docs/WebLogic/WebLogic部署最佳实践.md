WebLogic Server 11g (10.3.6) 安装配置的最佳实践

### 一、概述

#### 1.1 环境描述

硬件配置：2CPU/4GB/80GB （虚拟机）

操作系统版本：RHEL/CentOS Linux 6.5 （x86_64）

WebLogic版本：11g（10.3.6）

JAVA运行时版：Oracle JRockit 1.6.0.31-R28.2.3-4.1.0-linux-x64

#### 1.2 部署任务清单

1. 操作系统准备
2. JDK安装及JVM环境变量配置
3. WebLogic Server 安装
4. WebLogic Server 配置域创建
5. WebLogic Server 节点管理器（Node Manager）配置
6. 配置开机服务启动

### 二、详细部署步骤

#### 2.1 操作系统配置

##### 2.1.1 设置系统的主机名

```bash
[root@localhost ~]# 
```

##### 2.1.2 在/etc/hosts中配置地址解析

```bash
[root@wlsrv ~]# echo "192.168.200.100"
```

注意：这步很重要，后面WebLogic安装过程中会使用主机名，一旦设置不正确会导致安装失败。

##### 2.1.3 规划软件安装目录

| 目录                                | 目录说明                     |      |
| ----------------------------------- | ---------------------------- | ---- |
| /u01/weblogic                       | WebLogic Server 安装根目录   |      |
| /u01/weblogic/wlserver_10.3         | WebLogic Server 软件安装目录 |      |
| /u01/weblogic/user_projects/domains | WebLogic Server 域工作目录   |      |
|                                     |                              |      |

#### 2.2 JDK 安装及JVM环境变量配置

WebLogic Server 11g(10.3.6) 版本，官方推荐使用 Oracle JRockit 。

##### 2.2.1 Oracle JRockit 安装

```bash
# 将下载好的二进制安装包上传至服务器 /u01/install 目录
[root@wlsrv ~]# sh /u01/install/jrockit-jdk1.6.0_31-R28.2.3-4.1.0-linux-x64.bin
# 根据安装提示，将JDK安装至指定目录
# 本环境安装在/u01/jdk/jrockit-jdk1.6.0_31-R28.2.3-4.1.0
```

##### 2.2.2  JVM环境配置





#### 2.3 WebLogic Server 配置域创建

