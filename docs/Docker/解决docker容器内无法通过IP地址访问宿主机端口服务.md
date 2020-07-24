解决docker容器无法通过IP地址访问宿主机端口的问题

### 场景描述

开发环境中，为了便于便于开发人员本地服务注册调试，通过容器HOST环境变量将容器以宿主机地址和容器内使用的端口注册到eureka上，但是在出现容器间通过宿主机IP地址无法正常访问服务。



### 问题解决

服务器防火墙未授权docker0网卡访问权限

```bash
firewall-cmd --permanent --zone=trusted --change-interface=docker0
firewall-cmd --reload

# 重启 docker 服务
systemctl restart docker
```

