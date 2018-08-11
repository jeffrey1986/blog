# Ubuntu 1804上安装搜狗拼音输入法

文章转自[http://blog.sciencenet.cn/blog-3308665-1114148.html](http://blog.sciencenet.cn/blog-3308665-1114148.html)

[TOC]

## 安装简体中文语言

如果系统是英文系统，请确保安装了简体中文语言。安装后系统语言不用切换到中文。

Setting -> Region & Language -> Manage Installed Languages

## 下载搜狗拼音输入法安装文件

下载地址：[https://pinyin.sogou.com/linux/](https://pinyin.sogou.com/linux/)

比如我下载的文件为：sogoupinyin_2.2.0.0108_amd64.deb

## 安装搜狗输入法

```
sudo dpkg -i sogoupinyin_2.2.0.0108_amd64.deb
```

在新安装的ubuntu系统上，一般会遇到下面提示错误：

```
jeffrey@jeffrey-1804:~/Downloads$ sudo dpkg -i sogoupinyin_2.2.0.0108_amd64.deb 
[sudo] password for jeffrey: 
Selecting previously unselected package sogoupinyin.
(Reading database ... 174567 files and directories currently installed.)
Preparing to unpack sogoupinyin_2.2.0.0108_amd64.deb ...
Unpacking sogoupinyin (2.2.0.0108) ...
dpkg: dependency problems prevent configuration of sogoupinyin:
 sogoupinyin depends on fcitx (>= 1:4.2.8.3-3~); however:
  Package fcitx is not installed.
 sogoupinyin depends on fcitx-frontend-gtk2; however:
  Package fcitx-frontend-gtk2 is not installed.
 sogoupinyin depends on fcitx-frontend-gtk3; however:
  Package fcitx-frontend-gtk3 is not installed.
 sogoupinyin depends on fcitx-frontend-qt4; however:
  Package fcitx-frontend-qt4 is not installed.
 sogoupinyin depends on libfcitx-qt0 | fcitx-libs-qt; however:
  Package libfcitx-qt0 is not installed.
  Package fcitx-libs-qt is not installed.
 sogoupinyin depends on fcitx-module-kimpanel; however:
  Package fcitx-module-kimpanel is not installed.
 sogoupinyin depends on libopencc2 | libopencc1; however:
  Package libopencc2 is not installed.
  Package libopencc1 is not installed.
 sogoupinyin depends on fcitx-libs (>= 4.2.7); however:
  Package fcitx-libs is not installed.
 sogoupinyin depends on libqt4-dbus (>= 4:4.8.0); however:
  Package libqt4-dbus is not installed.
 sogoupinyin depends on libqt4-declarative (>= 4:4.8.0); however:
  Package libqt4-declarative is not installed.
 sogoupinyin depends on libqt4-network (>= 4:4.8.0); however:
  Package libqt4-network is not installed.
 sogoupinyin depends on libqtcore4 (>= 4:4.8.0); however:
  Package libqtcore4 is not installed.
 sogoupinyin depends on libqtgui4 (>= 4:4.8.0); however:
  Package libqtgui4 is not installed.
 sogoupinyin depends on libqtwebkit4 (>= 2.1.0~2011week13); however:
  Package libqtwebkit4 is not installed.

dpkg: error processing package sogoupinyin (--install):
 dependency problems - leaving unconfigured
Processing triggers for mime-support (3.60ubuntu1) ...
Processing triggers for libglib2.0-0:amd64 (2.56.1-2ubuntu1) ...
No such key 'Gtk/IMModule' in schema 'org.gnome.settings-daemon.plugins.xsettings' as specified in override file '/usr/share/glib-2.0/schemas/50_sogoupinyin.gschema.override'; ignoring override for this key.
Processing triggers for gnome-menus (3.13.3-11ubuntu1.1) ...
Processing triggers for desktop-file-utils (0.23-1ubuntu3.18.04.1) ...
Processing triggers for shared-mime-info (1.9-2) ...
Processing triggers for hicolor-icon-theme (0.17-2) ...
Errors were encountered while processing:
 sogoupinyin
```

这个时候可以输入下面指令来安装缺失的依赖：

```
sudo apt --fix-broken install
```

或者使用

```
sudo apt install xxx
```

来一个一个安装。理论上执行了sudo apt --fix-broken install就可以了。

然后再执行安装搜狗拼音输入发的指令：

```
sudo dpkg -i sogoupinyin_2.2.0.0108_amd64.deb
```

重启系统。

## 配置输入法

Setting -> Region & Language -> Manage Installed Languages

把Keboard input method system从IBus修改为fcitx

在系统程序中搜索打开fcitx configuration，右下角点击添加按钮，把 Only Show Current Language取消勾选，然后选择Sougou Pinyin，然后添加进去。

最后重启系统应该就可以选到搜狗拼音输入法了。

