# Ubuntu配置shadowsocks全局代理

参考文档:

[https://blog.csdn.net/cuipengchong/article/details/64472789](https://blog.csdn.net/cuipengchong/article/details/64472789)

https://blog.csdn.net/zhengchaooo/article/details/80202599

[TOC]

## 安装Python

```
sudo apt install python
sudo apt install python-pip
```

## 安装shadowsocks客户端

```
sudo pip install shadowsocks
```

## 配置shadowsocks客户端

首先要有自己的shadowsocks服务器，比如搬瓦工...

创建shadowsocks.json文件，放在任意位置:

```
{
    "server": "server_ip",
    "server_port": 8443,
    "local_port": 1080,
    "password": "your_password",
    "timeout": 600,
    "method": "AES-256-CFB"
}
```

## 启动shadowsocks客户端

```
sslocal -c shadowsocks.json
```

一般情况下，这里会报个python的错误:

```
jeffrey@jeffrey-1804:~/Tools/shadowsocks$ sudo sslocal -c shadowsocks.json -d start
INFO: loading config from shadowsocks.json
2018-08-14 01:34:11 INFO     loading libcrypto from libcrypto.so.1.1
Traceback (most recent call last):
  File "/usr/local/bin/sslocal", line 11, in <module>
    load_entry_point('shadowsocks==2.8.2', 'console_scripts', 'sslocal')()
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/local.py", line 39, in main
    config = shell.get_config(True)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/shell.py", line 262, in get_config
    check_config(config, is_local)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/shell.py", line 124, in check_config
    encrypt.try_cipher(config['password'], config['method'])
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 44, in try_cipher
    Encryptor(key, method)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 83, in __init__
    random_string(self._method_info[1]))
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/encrypt.py", line 109, in get_cipher
    return m[2](method, key, iv, op)
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py", line 76, in __init__
    load_openssl()
  File "/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py", line 52, in load_openssl
    libcrypto.EVP_CIPHER_CTX_cleanup.argtypes = (c_void_p,)
  File "/usr/lib/python2.7/ctypes/__init__.py", line 379, in __getattr__
    func = self.__getitem__(name)
  File "/usr/lib/python2.7/ctypes/__init__.py", line 384, in __getitem__
    func = self._FuncPtr((name_or_ordinal, self))
AttributeError: /usr/lib/x86_64-linux-gnu/libcrypto.so.1.1: undefined symbol: EVP_CIPHER_CTX_cleanup
```

这个是由于版本兼容性的问题，我们需要修改openssl.py文件，比如上面错误中可以看出openssl.py路径为：

/usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py

打开这个文件，修改其中的EVP_CIPHER_CTX_cleanup为EVP_CIPHER_CTX_reset

```
sudo gedit /usr/local/lib/python2.7/dist-packages/shadowsocks/crypto/openssl.py
```

用sudo权限打开，然后替换。然后再执行开启shadowsocks客户端的命令可以发现成功启动:

```
jeffrey@jeffrey-1804:~/Tools/shadowsocks$ sslocal -c shadowsocks.json
INFO: loading config from shadowsocks.json
2018-08-14 01:40:32 INFO     loading libcrypto from libcrypto.so.1.1
started
```

如果想要停止shadowsocks,可以执行:

```
sslocal -c /etc/shadowsocks.json
```

## 在命令行中配置代理

这个时候我们在命令行中输入

```
wget www.google.com
```

发现并不能连接,我们还需要配置:

```
export http_proxy=http://127.0.0.1:8118/
export https_proxy=http://127.0.0.1:8118/
```

## 在Chromium中配置代理

安装privoxy

```
sudo apt install privoxy
```

配置privoxy

打开/etc/privoxy/config文件,在文件末尾添加:

```
listen-address  127.0.0.1:8118
listen-address  [::1]:8118
forward-socks5t / 127.0.0.1:1080
```

在Chromium中安装插件Proxy SwitchyOmega, 安装好后在Proxy SwitchyOmega设置中,新建一个profile,对应的值如下:

Scheme: default

Protocol: Socks5

Server: 127.0.0.1

Port: 1080

Bypass List:

127.0.0.1
[::1]
localhost

需要翻墙的时候,选择这个profile的就行了.

## 开机启动

ubuntu18.04不再使用initd管理系统，改用systemd. 然而systemd很难用，改变太大，跟之前的完全不同.

为了像以前一样，在/etc/rc.local中设置开机启动程序，需要以下几步：

1. systemd默认读取/etc/systemd/system下的配置文件，该目录下的文件会链接/lib/systemd/system/下的文件。一般系统安装完/lib/systemd/system/下会有rc-local.service文件，即我们需要的配置文件。 

链接过来：

```
ln -fs /lib/systemd/system/rc-local.service /etc/systemd/system/rc-local.service
cat /etc/systemd/system/rc-local.service 
```

rc-local.service内容

```

#  SPDX-License-Identifier: LGPL-2.1+
#
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

# This unit gets pulled automatically into multi-user.target by
# systemd-rc-local-generator if /etc/rc.local is executable.
[Unit]
Description=/etc/rc.local Compatibility
Documentation=man:systemd-rc-local-generator(8)
ConditionFileIsExecutable=/etc/rc.local
After=network.target

[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
RemainAfterExit=yes
GuessMainPID=no

[Install]  
WantedBy=multi-user.target  
Alias=rc-local.service

```

- [Unit] 区块：启动顺序与依赖关系。 

- [Service] 区块：启动行为,如何启动，启动类型。 

- [Install] 区块，定义如何安装这个配置文件，即怎样做到开机启动。默认是没有后面Install模块的,需要自己加上.

2. 创建/etc/rc.local文件

```bash
touch /etc/rc.local
```

3. 赋可执行权限

```bash
chmod 755 /etc/rc.local
```

4. 编辑rc.local，添加需要开机启动的任务

```bash
#!/bin/bash
sslocal -c /home/jeffrey/Tools/shadowsocks/shadowsocks.json &

```

切记加上指令后面的&号让其在后台运行,否则会block住启动界面导致进不了系统...

如果进不了系统了,找个ubuntu安装盘,进入试用版系统,通过这个去方位原系统的硬盘来修改~~~

