WebLogic Server 应用发布最佳实践

### 一、WebLogic Server 应版本发布方案介绍

#### 1.1 简介

WebLogic应用版本常用发布方式有三种：

1. 控制台版本发布
2. 后台缓存版本发布
3. 后台脚本发布。

下面我们提供了三种方式优缺点的对比。

#### 1.2 发布方案对比

| 发  布  方  式   | 优      点                          | 缺        点                                                |
| ---------------- | ----------------------------------- | ----------------------------------------------------------- |
| 控制台版本发布   | 1）页面操作，简单不易出错           | 1）删除旧版本，安装新版本的过程中，会造成页面服务中断几分钟 |
|                  | 2）无需手动清除缓存及重启           | 2）由于会中断服务，所以每次版本更新要在夜里进行             |
| 后台缓存版本发布 | 1）多台机器时可以分开执行           | 1）易错删文件或目录                                         |
|                  | 2)	版本发布时间较快              | 2）服务器需重启，应用有中断。                               |
| 后台脚本版本发布 | 1）发布过程无应用中断，对用户透明。 | 1）首次部署需要修改实施脚本                                 |
|                  | 2）脚步命令执行，提高发版效率。     | 2）部署前后需要人为检测服务器及运行健康状态                 |
|                  | 3）对用户透明，发版本时间灵活       |                                                             |

### 二、发布环境准备

#### 2.1 创建版本发布目录

```bash
#创建版本发布目录
[root@wlsrv ~]# mkdir /u01/deploy

# 创建版本发布应用存储目录
[root@wlsrv ~]# mkdir /u01/deploy/apps

#创建版本发布脚本目录
[root@wlsrv ~]# mkdir /u01/deploy/scripts
```

#### 2.2 修改环境变量

```bash
# 环境变量修改 （根据WebLogic Server 安装时配置环境变量基础上进行调整）
[root@wlsrv ~]# vim /etc/profile
JAVA_HOME=/u01/jdk/jrockit-jdk1.6.0_31-R28.2.3-4.1.0
PATH=$JAVA_HOME/bin:$PATH
WLS_HOME=/u01/weblogic
DEPLOY_SCRIPTS=/u01/deploy/scripts
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$WLS_HOME/wlserver_10.3/server/lib/weblogic.jar
export JAVA_HOME PATH WLS_HOME CLASSPATH DEPLOY_SCRIPTS

# 应用环境变量
[root@wlsrv ~]# source /etc/profile
```

#### 2.3 版本发布脚本

将下面下面三个脚本保存至 /u01/deploy/scripts 目录

##### 2.3.1  应用发布脚本 Deploy.sh

```bash
export NLS_LANG=AMERICAN_AMERICA
$WLS_HOME/wlserver_10.3/server/bin/setWLSEnv.sh $*
java weblogic.Deployer -adminurl t3://$1  -username $2 -password $3  -name $4 -targets $5 -deploy  -source $6  -appversion $7
exit 0
```

##### 2.3.2 应用卸载脚本 UnDeploy.sh

```bash
export NLS_LANG=AMERICAN_AMERICA
$WLS_HOME/wlserver_10.3/server/bin/setWLSEnv.sh $*
java weblogic.Deployer -adminurl t3://$1  -username $2 -password $3 -name $4 -targets $5 -undeploy -appversion $6
exit 0
```

##### 2.3.3 应用回滚/重发脚本 ReDeploy.sh

```bash
export NLS_LANG=AMERICAN_AMERICA
$WLS_HOME/wlserver_10.3/server/bin/setWLSEnv.sh $*
java weblogic.Deployer -adminurl t3://$1  -username $2 -password $3  -name $4 -targets $5 -redeploy  -source $6 -appversion $7
exit 0
```

#### 2.4 应用发布脚本执行说明

##### 2.4.1 发布新版本（Deploy）

```bash
[root@wlsrv ~]# DEPLOY_SCRIPTS/Deploy.sh 192.168.100.100:7001 weblogic password appName ManagedServer /u01/deploy/app/v1.0.1/appName.war v1.0.1

# 参数一：192.168.100.100:7001为Weblogic控制台console IP及端口
# 参数二、三：分别为Weblogic Console的用户名及密码
# 参数四：appName为部署目标应用名称
# 参数五：ManagedServer为目标服务器，如果有多个服务器用逗号分隔，如果有集群，则为集群的名称。
# 参数六：为应用路径，即版本所在位置
# 参数七：新版本版本号
# 此命令第一次执行成功后，第二次再执行将最后一个版本号加1，其它参数保持不变（应用路径可根据实际情况调整
```

##### 2.4.2卸载旧版本（UnDeploy）

```bash
[root@wlsrv ~]# DEPLOY_SCRIPTS/UnDeploy.sh 192.168.100.100:7001 weblogic password appName ManagedServer  v1.0.1

# 参数一：192.168.100.100:7001为Weblogic控制台console IP及端口
# 参数二、三：分别为Weblogic Console的用户名及密码
# 参数四：appName为部署目标应用名称
# 参数五：ManagedServer为目标服务器，如果有多个服务器用逗号分隔，如果有集群，则为集群的名称。
# 参数六：最后一个参数则为执行deploy命令时的版本号，一定要保持一致（老版本的版本号）
```

##### 2.4.3重发版本/回滚旧版本（ReDeploy）

```bash
[root@wlsrv ~]# DEPLOY_SCRIPTS/ReDeploy.sh 192.168.100.100:7001 weblogic password appName ManagedServer /u01/deploy/app/v1.0.1/appName.war v1.0.1

# 参数一：192.168.100.100:7001为Weblogic控制台console IP及端口
# 参数二、三：分别为Weblogic Console的用户名及密码
# 参数四：appName为部署目标应用名称
# 参数五：ManagedServer为目标服务器，如果有多个服务器用逗号分隔，如果有集群，则为集群的名称。
# 参数六：为应用路径，即版本所在位置
# 参数七：为执行deploy命令时的版本号，一定要保持一致（部署已经部署过的版本，使用此命令，版本号为do.sh里面的版本号，不需要加一，因为是重新部署，所以版本号不变）
```

#### 3. 应用部署执行

##### 3.1 服务器状态检查

* 通过WebLogic Console查看需要进行部署版本的服务器运行状态是否正常。
* 若状态不为运行状态，则需要先将服务器置为运行状态才可以进行以下步骤。

##### 3.2 应用包准备

* 备份旧版本应用包
* 修改应用包相关环节配置参数
* 上传新版本应用包至服务器部署目录



©匠芯猿人  Date：2020年07月25日16:20:36