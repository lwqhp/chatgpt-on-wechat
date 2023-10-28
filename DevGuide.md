# 服务器部署

（1）采用阿里云ECS服务器

（2）版本：centos 7.5

```
centos 6.x没有yum仓库
```



## python环境部署

### 1、安装可能用到的相关依赖

```shell
yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel gcc gcc-c++
```

**安装时报错ModuleNotFoundError: No module named '_ctypes'的解决办法**

```shell
yum install libffi-devel 

从"./configure ..."重新安装
```

**pip3 install时报错pip is configured with locations that require TLS/SSL, however the ssl module in Python is not available.**

**ubuntu：**

```shell
sudo apt-get install libffi-dev
或者：
sudo apt-get update
 
sudo apt-get upgrade
 
sudo apt-get dist-upgrade
 
sudo apt-get install build-essential python-dev python-setuptools python-pip python-smbus
 
sudo apt-get install build-essential libncursesw5-dev libgdbm-dev libc6-dev
 
sudo apt-get install zlib1g-dev libsqlite3-dev tk-dev
 
sudo apt-get install libssl-dev openssl
 
sudo apt-get install libffi-dev
```

**centos7**

```
yum install libffi-devel -y
```



### 2、安装python完整脚本

```shell
#!/bin/bash

if [ ! -d "/data/servers/openssl" ]; then
    yum install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel sqlite-devel gcc gcc-c++ libffi-devel
    cd /root
    wget https://www.openssl.org/source/openssl-1.1.1w.tar.gz --no-check-certificate
    tar -xf openssl-1.1.1w.tar.gz
    cd openssl-1.1.1w
    ./config --prefix=/data/servers/openssl
    make && make install
    mv /usr/bin/openssl /usr/bin/openssl.old
    mv /usr/lib64/openssl /usr/lib64/openssl.old
    ln -s /data/servers/openssl/bin/openssl /usr/bin/openssl
    ln -s /data/servers/openssl/include/openssl /usr/include/openssl
    ln -s /data/servers/openssl/lib/libssl.so /usr/lib64/libssl.so
    echo "/data/servers/openssl/lib" >>/etc/ld.so.conf
	ln -s /data/servers/openssl/lib/libssl.so.1.1 /usr/lib64/libssl.so.1.1
    ln -s /data/servers/openssl/lib/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
    cd /root
    wget https://www.python.org/ftp/python/3.8.10/Python-3.8.10.tgz
    tar -xzf Python-3.8.10.tgz 
    cd Python-3.8.10
    ./configure --prefix=/data/servers/python3.8.10 --with-openssl=/data/servers/openssl
    make && make install
	#chown -R rundeck:rundeck /data/servers/python3.6.1/
    /data/servers/python3.8.10/bin/python -m ensurepip --upgrade
    /data/servers/python3.8.10/bin/pip install --upgrade pip
    cd /root
    rm -rf Python-3.8.10* openssl-1.1.1w*
else
    yum -y install openssl-devel
    cd /root
    wget https://www.python.org/ftp/python/3.8.10/Python-3.8.10.tgz
    tar -xzf Python-3.8.10.tgz
    cd Python-3.8.10
    ./configure --prefix=/data/servers/python3.8.10 --with-openssl=/data/servers/openssl
    make && make install
    #chown -R rundeck:rundeck /data/servers/python3.6.1/
    /data/servers/python3.8.10/bin/python3 -m ensurepip --upgrade
    /data/servers/python3.8.10/bin/pip3 install --upgrade pip -i http://pypi.douban.com/simple --trusted-host pypi.douban.com
    cd /root
    rm -rf Python-3.8.10*
fi

```



### 3、相关软件和检查

```shell
#安装上传工具
yum install lrzsz -y

#安装zip解压工具
yum install unzip

#检查是否安装成功ssl
/data/server/python3.8.10/bin/python3
>> import ssl

#配置python3软链
ln -s /data/servers/python-3.8.10/bin/python3 /usr/bin/python38
ln -s /data/servers/python-3.8.10/bin/pip3 /usr/bin/pip38


```

