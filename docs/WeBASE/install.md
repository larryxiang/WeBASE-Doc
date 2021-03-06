# 一键部署

​	一键部署可以在 **同机** 快速搭建WeBASE管理台环境，方便用户快速体验WeBASE管理平台。

​	一键部署会搭建：节点（FISCO-BCOS 2.0+）、管理平台（WeBASE-Web）、节点管理子系统（WeBASE-Node-Manager）、节点前置子系统（WeBASE-Front）、签名服务（WeBASE-Sign）。其中，节点的搭建是可选的，可以通过配置来选择使用已有链或者搭建新链。一键部署架构如下：

![[]](../../images/WeBASE/one_click_struct.png)


## 前提条件

| 环境   | 版本                   |
| ------ | ---------------------- |
| Java   | JDK8或以上版本 |
| MySQL | MySQL-5.6或以上版本 |
| Python | Python3.5+ |
| PyMySQL | 使用python3时需安装 |

### 检查环境

#### 平台要求

推荐使用CentOS 7.2+, Ubuntu 16.04及以上版本, 一键部署脚本将自动安装`openssl, curl, wget, git`相关依赖项。

其余系统可能导致安装依赖失败，可自行安装`openssl, curl, wget, git`依赖项后重试

#### 检查Java

JDK8或以上版本：
```
java -version
```

- Java推荐使用[OpenJDK](#id10 ) ，建议从[OpenJDK网站](https://jdk.java.net/java-se-ri/11) 自行下载（CentOS的yum仓库的OpenJDK缺少JCE(Java Cryptography Extension)，导致Web3SDK无法正常连接区块链节点）

#### 检查mysql

MySQL-5.6或以上版本：
```
mysql --version
```

- Mysql安装部署可参考[数据库部署](#id14)

#### 检查Python

使用Python3.5或以上版本：
```
python --version
```

- Python安装部署可参考[Python部署](#id17)

#### PyMySQL部署（Python3.5+）

Python3.5及以上版本，需安装`PyMySQL`依赖包

python3.5+版本安装`PyMysql`依赖包方法：

- CentOS

  ```
  sudo pip3 install PyMySQL
  ```

  不支持pip命令的话，可以使用以下方式：

  ```
  git clone https://github.com/PyMySQL/PyMySQL
  cd PyMySQL/
  python3 setup.py install
  ```

- Ubuntu

  ```
  sudo apt-get install -y python3-pip
  sudo pip3 install PyMySQL
  ```


## 拉取部署脚本

获取部署安装包：
```shell
wget https://github.com/WeBankFinTech/WeBASELargeFiles/releases/download/v1.4.0/webase-deploy.zip
```
解压安装包：
```shell
unzip webase-deploy.zip
```
进入目录：
```shell
cd webase-deploy
```

## 修改配置

① mysql数据库需提前安装，已安装直接配置即可，还未安装请参看[数据库部署](#id14)；

② 修改配置文件（vi common.properties），没有变化的可以不修改；

③ 一键部署支持使用已有链或者搭建新链。通过参数"if.exist.fisco"配置是否使用已有链，以下配置二选一即可：

- 当配置"yes"时，需配置已有链的路径
- 当配置"no"时，需配置节点fisco版本和节点安装个数，搭建的新链默认两个群组

​    如果不使用一键部署搭建新链，可以参考FISCO BCOS官方文档搭建 [FISCO BCOS部署流程](https://fisco-bcos-documentation.readthedocs.io/zh_CN/latest/docs/installation.html#fisco-bcos)；

④ 服务端口不能小于1024。

⑤ 部署时，支持**一键部署**或者**可视化部署**两种方式，**二者选择其一即可**

下面分别介绍**一键部署和可视化部署**的两种配置示例：
1. 采用一键部署，则根据说明修改 `common.properties` 文件中的配置；
2. 采用可视化部署，则根据说明修改 `visual-deploy.properties` 文件中的配置；

- **一键部署**方式时，修改 `common.properties` 配置文件

```shell
# WeBASE子系统的最新版本(v1.1.0或以上版本)
webase.web.version=v1.4.0
webase.mgr.version=v1.4.0
webase.sign.version=v1.4.0
webase.front.version=v1.4.0

# 节点管理子系统mysql数据库配置
mysql.ip=127.0.0.1
mysql.port=3306
mysql.user=dbUsername
mysql.password=dbPassword
mysql.database=webasenodemanager

# 签名服务子系统mysql数据库配置
sign.mysql.ip=localhost
sign.mysql.port=3306
sign.mysql.user=dbUsername
sign.mysql.password=dbPassword
sign.mysql.database=webasesign

# 节点前置子系统h2数据库名
front.h2.name=webasefront

# WeBASE管理平台服务端口
web.port=5000
# 节点管理子系统服务端口
mgr.port=5001
# 节点前置子系统端口
front.port=5002
# 签名服务子系统端口
sign.port=5004


# 节点监听Ip
node.listenIp=127.0.0.1
# 节点p2p端口
node.p2pPort=30300
# 节点链上链下端口
node.channelPort=20200
# 节点rpc端口
node.rpcPort=8545

# 是否使用国密（0: standard, 1: guomi）
encrypt.type=0

# 是否使用已有的链（yes/no）
if.exist.fisco=no

# 使用已有链时需配置
# 已有链的路径，start_all.sh脚本所在路径
# 路径下要存在sdk目录，sdk里存放sdk证书（ca.crt、node.crt和node.key）
fisco.dir=/data/app/nodes/127.0.0.1
# 前置所连接节点的绝对路径
# 路径下要存在conf文件夹，conf里存放节点证书（ca.crt、node.crt和node.key）
node.dir=/data/app/nodes/127.0.0.1/node0

# 搭建新链时需配置
# FISCO-BCOS版本
fisco.version=2.4.1
# 搭建节点个数（默认两个）
node.counts=nodeCounts
```


- **可视化方式**时，修改 `visual-deploy.properties` 文件。
<span id="visual-deploy-config"></span>


```eval_rst
.. important::
    注意： `sign.ip` 配置的 IP 是WeBASE-Sign签名服务对外提供服务访问的 IP 地址，供其他部署节点主机访问。
```

```shell
# WeBASE子系统的最新版本(v1.1.0或以上版本)
webase.web.version=v1.4.0
webase.mgr.version=v1.4.0
webase.sign.version=v1.4.0
# Docker 镜像版本，默认不需要修改
fisco.webase.docker.cdn.version=v1.4.0

# 节点管理子系统mysql数据库配置
mysql.ip=127.0.0.1
mysql.port=3306
mysql.user=dbUsername
mysql.password=dbPassword
mysql.database=webasenodemanager

# 签名服务子系统mysql数据库配置
sign.mysql.ip=localhost
sign.mysql.port=3306
sign.mysql.user=dbUsername
sign.mysql.password=dbPassword
sign.mysql.database=webasesign

# WeBASE管理平台服务端口
web.port=5000

# 节点管理子系统服务端口
mgr.port=5001

# 签名服务子系统端口
sign.port=5004

# 是否使用国密（0: standard, 1: guomi）
encrypt.type=0

# WeBASE-Sign 对外提供服务的访问 IP 地址
# 部署在其它主机的节点，需要使用此 IP 访问 WeBASE-Sign 服务
# 不能是 127.0.0.1 或者 localhost
sign.ip=

# SSH 免密登录账号
mgr.ssh.user=root
# SSH 访问端口
mgr.ssh.port=22
# 部署节点服务的主机，存放链数据的目录
mgr.ssh.rootDirOnHost=/opt/fisco
```

## 部署

* 部署服务

**部署方式：**

| 部署方式  | 参数  | 说明  |
|---|---|---|
|  一键部署 |  installAll | 部署 WeBASE 中间件服务，包括底层节点  |
|  可视化部署 |  installWeBASE | 部署 WeBASE 中间件服务，<br />然后通过**界面操作的方式部署底层节点**，参考文档 [可视化部署](../WeBASE-Install/visual_deploy.html) |

根据上文已选择的部署方式完成配置文件的修改后，执行下面对应的部署、停止、启动命令即可

* **一键部署**方式，则执行：
```shell

# 1. 部署并启动所有服务
python deploy.py installAll

# 2. 停止一键部署的所有服务
python deploy.py stopAll

# 3. 启动一键部署的所有服务
python deploy.py startAll

```

* **可视化部署**方式，则执行：
```shell

# 1. 部署并启动可视化部署的所有服务
python deploy.py installWeBASE

# 2. 停止可视化部署的所有服务
python deploy.py stopWeBASE

# 3. 启动可视化部署的所有服务
python deploy.py startWeBASE

```

* 服务部署后，如果需要单独启停，可以使用以下命令：

```shell
启动FISCO-BCOS节点:      python deploy.py startNode
停止FISCO-BCOS节点:      python deploy.py stopNode
启动WeBASE-Web:          python deploy.py startWeb
停止WeBASE-Web:          python deploy.py stopWeb
启动WeBASE-Node-Manager: python deploy.py startManager
停止WeBASE-Node-Manager: python deploy.py stopManager
启动WeBASE-Sign:        python deploy.py startSign
停止WeBASE-Sign:        python deploy.py stopSign
启动WeBASE-Front:        python deploy.py startFront
停止WeBASE-Front:        python deploy.py stopFront
```

**备注：** 

- 部署脚本会拉取相关安装包进行部署，需保持网络畅通。
- 首次部署需要下载编译包和初始化数据库，重复部署时可以根据提示不重复操作
- 部署过程出现问题可以查看[常见问题解答](#id19)

## 访问

WeBASE管理平台：

* 一键部署完成后，打开浏览器访问
```
http://{deployIP}:{webPort}
示例：http://localhost:5000
```

**备注：** 

- 部署服务器IP和管理平台服务端口需对应修改，网络策略需开通
- WeBASE管理平台使用说明请查看[使用手册](../WeBASE-Console-Suit/index.html#id13)（获取WeBASE管理平台默认账号和密码，并初始化系统配置）
  - 默认账号为`admin`，默认密码为`Abcd1234`。首次登陆要求重置密码
  - 添加节点前置WeBASE-Front到WeBASE管理平台；一键部署时，节点前置与节点管理服务默认是同机部署，添加前置则填写IP为`127.0.0.1`，默认端口为`5002`。参考上文中`common.properties`的配置项`front.port={frontPort}`


* 若选择 **可视化部署**
    - 请参见[可视化部署](../WeBASE-Install/visual_deploy.html#id12) ，部署底层节点

## 日志路径

```
|-- webase-deploy # 一键部署目录
|--|-- log # 部署日志目录
|--|-- webase-web # 管理平台目录
|--|--|-- log # 管理平台日志目录
|--|-- webase-node-mgr # 节点管理服务目录
|--|--|-- log # 节点管理服务日志目录
|--|-- webase-sign # 签名服务目录
|--|--|-- log # 签名服务日志目录
|--|-- webase-front # 节点前置服务目录
|--|--|-- log # 节点前置服务日志目录
|--|-- nodes # 一件部署搭链节点目录
|--|--|-- 127.0.0.1
|--|--|--|-- node0 # 具体节点目录
|--|--|--|--|-- log # 节点日志目录
```

**备注：** 当前节点日志路径为一件部署搭链的路径，使用已有链请在相关路径查看日志。

## 附录

### 1. Java环境部署

此处给出OpenJDK安装简单步骤，供快速查阅。更详细的步骤，请参考[官网](https://openjdk.java.net/install/index.html)。

#### ① 安装包下载

从[官网](https://jdk.java.net/java-se-ri/11)下载对应版本的java安装包，并解压到服务器相关目录

```shell
mkdir /software
tar -zxvf openjdkXXX.tar.gz /software/
```

#### ② 配置环境变量

- 修改/etc/profile

```
sudo vi /etc/profile
```

- 在/etc/profile末尾添加以下信息

```shell
JAVA_HOME=/software/jdk-11
PATH=$PATH:$JAVA_HOME/bin
CLASSPATH==.:$JAVA_HOME/lib
export JAVA_HOME CLASSPATH PATH
```

- 重载/etc/profile

```
source /etc/profile
```

#### ③ 查看版本

```
java -version
```

### 2. 数据库部署

此处以Centos安装*MariaDB*为例。*MariaDB*数据库是 MySQL 的一个分支，主要由开源社区在维护，采用 GPL 授权许可。*MariaDB*完全兼容 MySQL，包括API和命令行。其他安装方式请参考[MySQL官网](https://dev.mysql.com/downloads/mysql/)。

#### ① 安装MariaDB

- 安装命令

```shell
sudo yum install -y mariadb*
```

- 启停

```shell
启动：sudo systemctl start mariadb.service
停止：sudo systemctl stop  mariadb.service
```

- 设置开机启动

```
sudo systemctl enable mariadb.service
```

- 初始化

```shell
执行以下命令：
sudo mysql_secure_installation
以下根据提示输入：
Enter current password for root (enter for none):<–初次运行直接回车
Set root password? [Y/n] <– 是否设置root用户密码，输入y并回车或直接回车
New password: <– 设置root用户的密码
Re-enter new password: <– 再输入一次你设置的密码
Remove anonymous users? [Y/n] <– 是否删除匿名用户，回车
Disallow root login remotely? [Y/n] <–是否禁止root远程登录，回车
Remove test database and access to it? [Y/n] <– 是否删除test数据库，回车
Reload privilege tables now? [Y/n] <– 是否重新加载权限表，回车
```

#### ② 授权访问和添加用户

- 使用root用户登录，密码为初始化设置的密码

```
mysql -uroot -p -h localhost -P 3306
```

- 授权root用户远程访问

```sql
mysql > GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123456' WITH GRANT OPTION;
mysql > flush PRIVILEGES;
```

- 创建test用户并授权本地访问

```sql
mysql > GRANT ALL PRIVILEGES ON *.* TO 'test'@localhost IDENTIFIED BY '123456' WITH GRANT OPTION;
mysql > flush PRIVILEGES;
```

**安全温馨提示：**

- 例子中给出的数据库密码（123456）仅为样例，强烈建议设置成复杂密码
- 例子中root用户的远程授权设置会使数据库在所有网络上都可以访问，请按具体的网络拓扑和权限控制情况，设置网络和权限帐号

#### ③ 测试连接和创建数据库

- 登录数据库

```shell
mysql -utest -p123456 -h localhost -P 3306
```

- 创建数据库

```sql
mysql > create database webasenodemanager;
```

### 3. Python部署

- CentOS

  ```
  sudo yum install -y python-requests
  ```

- Ubuntu

  ```
  sudo apt-get install -y python-requests
  ```

### 4. 安装MySql python依赖包（使用于python2.7+）

#### 查看python版本

```
python --version
```
python2.7+版本，需要安装`MySQL-python`，安装方法如下：

*注*：同时支持python3.5+，python3.5+时需要安装`Py-MySQL`依赖包，可参考 [检查python环境-PyMysql](#pymysql-python3-5) 

python2.7+版本安装`MySQL-python`依赖包方法：

#### 4.1 MySQL-python部署（Python2.7）

- CentOS

  ```
  sudo yum install -y MySQL-python
  ```

- Ubuntu

  ```
  sudo apt-get install -y python-pip
  sudo pip install MySQL-python
  ```



## 常见问题

### 1. Python命令出错

```
  File "deploy.py", line 62
    print helpMsg
                ^
SyntaxError: Missing parentheses in call to "print". Did you mean print(helpMsg)?
```

答：检查Python版本

### 2. 使用Python2时找不到MySQLdb

```
Traceback (most recent call last):
...
ImportError: No module named MySQLdb
```

答：需要安装MySQL-python，安装请参看 [附录](#mysql-python)

### 3. 使用Python3时找不到pymysql

```
Traceback (most recent call last):
...
ImportError: No module named 'pymysql'
```

答：需要安装PyMySQL，安装请参看 [pymysql](#pymysql-python3-5)

### 4. 安装MySQL-python遇到问题

```
Command "python setup.py egg_info" failed with error code 1
```

答：运行下面两个命令
```
pip install --upgrade setuptools
python -m pip install --upgrade pip
```

### 5. 部署时某个组件失败，重新部署提示端口被占用问题

答：因为有个别组件是启动成功的，需先执行“python deploy.py stopAll”将其停止，再执行“python deploy.py installAll”部署全部。

### 6. 管理平台启动时Nginx报错

```
...
==============      WeBASE-Web      start...  ==============
Traceback (most recent call last):
...
Exception: execute cmd  error ,cmd : sudo /usr/local/nginx/sbin/nginx -c /data/app/webase-deploy/comm/nginx.conf, status is 256 ,output is nginx: [emerg] open() "/etc/nginx/mime.types" failed (2: No such file or directory) in /data/app/webase-deploy/comm/nginx.conf:13
```

答：缺少/etc/nginx/mime.types文件，建议重装nginx。

### 7. 部署时数据库访问报错

```
...
checking database connection
Traceback (most recent call last):
  File "/data/temp/webase-deploy/comm/mysql.py", line 21, in dbConnect
    conn = mdb.connect(host=mysql_ip, port=mysql_port, user=mysql_user, passwd=mysql_password, charset='utf8')
  File "/usr/lib64/python2.7/site-packages/MySQLdb/__init__.py", line 81, in Connect
    return Connection(*args, **kwargs)
  File "/usr/lib64/python2.7/site-packages/MySQLdb/connections.py", line 193, in __init__
    super(Connection, self).__init__(*args, **kwargs2)
OperationalError: (1045, "Access denied for user 'root'@'localhost' (using password: YES)")
```

答：确认数据库用户名和密码

### 8. 节点sdk目录不存在

```
...
======= FISCO-BCOS sdk dir:/data/app/nodes/127.0.0.1/sdk is not exist. please check! =======
```

答：确认节点安装目录下有没有sdk目录（企业部署工具搭建的链可能没有），如果没有，需手动创建"mkdir sdk"，并将节点证书（ca.crt、node.crt、node.key）复制到该目录，再重新部署。

### 9. 前置启动报错“nested exception is javax.net.ssl.SSLException”

```
...
nested exception is javax.net.ssl.SSLException: Failed to initialize the client-side SSLContext: Input stream not contain valid certificates.
```

答：CentOS的yum仓库的OpenJDK缺少JCE(Java Cryptography Extension)，导致Web3SDK无法正常连接区块链节点，因此在使用CentOS操作系统时，推荐从[OpenJDK网站](https://jdk.java.net/java-se-ri/11)自行下载。


### 10.前置启动报错“Processing bcos message timeout”

```
...
[main] ERROR SpringApplication() - Application startup failed
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'contractController': Unsatisfied dependency expressed through field 'contractService'; nested exception is org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'contractService': Unsatisfied dependency expressed through field 'web3jMap'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'web3j' defined in class path resource [com/webank/webase/front/config/Web3Config.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [java.util.HashMap]: Factory method 'web3j' threw exception; nested exception is java.io.IOException: Processing bcos message timeout
...
```

答：一些Oracle JDK版本缺少相关包，导致节点连接异常。推荐使用OpenJDK，从[OpenJDK网站](https://jdk.java.net/java-se-ri/11)自行下载。

### 11. 服务进程起来了，服务不正常

```
...
======= WeBASE-Node-Manager  starting . Please check through the log file (default path:./webase-node-mgr/log/). =======
```

答：查看日志，确认问题原因。确认后修改重启，如果重启提示服务进程在运行，先执行“python deploy.py stopAll”将其停止，再执行“python deploy.py startAll”重启。

### 12. WeBASE-Web登录页面的验证码加载不出来

答：检查WeBASE-Node-Manager后台服务是否已启动成功：在webase-node-mgr目录下，运行`bash status.sh`或者查看目录中`log/WeBASE-Node-Manager.log`日志文件，查看是否启动失败；

### 13. WeBASE CDN加速服务

答：WeBASE CDN 加速服务提供 WeBASE 各子系统安装包的下载服务。

为了提供更稳定的下载服务，从 WeBASE v1.4.0 开始使用新的 CDN 下载地址。具体使用方法如下：

CDN 下载地址：
```Bash
https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBASE/releases/download/{release_version}/webase-{subsystem}.zip
```

其中`{release_version}`为`v1.x.x`格式，`{subsystem}`则是子系统名字，支持下载`sign, front, node-mgr, web`子系统的zip安装包（全小写），暂不支持webase-transaction的安装包下载。

可以直接通过`wget`或者`curl -O`命令直接获取安装包。比如：

- 获取WeBASE-Node-Manager v1.4.0的安装包`webase-node-mgr.zip`

```
wget https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBASE/releases/download/v1.4.0/webase-node-mgr.zip
// 或
curl -O https://osp-1257653870.cos.ap-guangzhou.myqcloud.com/WeBASE/releases/download/v1.4.0/webase-node-mgr.zip
```


### 14. WeBASE代码仓库国内gitee镜像

答：WeBASE代码仓库在国内的gitee镜像地址为`https://gitee.com/WeBank/WeBASE`，WeBASE其他子系统的仓库则是`https://gitee.com/WeBank/`+ `WeBASE-XXX`，如WeBASE-Front的gitee代码仓库为`https://gitee.com/WeBank/WeBASE-Front`


