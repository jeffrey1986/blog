# 如何在Ubuntu 1804上安装Oracle JDK

## 下载JDK安装文件

首先从[oracle官方网站](http://www.oracle.com/technetwork/java/javase/downloads/index.html)下载JDK安装文件，这里我们安装的文件为：jdk-10.0.2_linux-x64_bin.tar.gz

## 解压JDK

这里我们安装在 ~/Tools/下面

```
tar zxf jdk-10.0.2_linux-x64_bin.tar.gz -C ~/Tools/
```

## 配置环境变量

修改文件~/.bashrc文件, 在文件末尾加上：

```
# jdk environment
JAVA_HOME=~/Tools/jdk-10.0.2
export PATH=$PATH:$JAVA_HOME/bin
```

重启系统后输入

```f
java -version
```

来查询刚配置的环境变量是否生效。

