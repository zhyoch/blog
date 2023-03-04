---
title: "Debian自用设置"
date: 2020-06-12T20:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Debian,Linux,Profile]
featuredImagePreview: "/2020-4/featuredImagePreview.svg"
summary: 用于系统重装前的备份与重装后的恢复。
---

> 用于系统重装前的备份与重装后的恢复。

## 新建用户

参考[Linux 用户和用户组管理](https://www.runoob.com/linux/linux-user-manage.html)。

1. `-d`: 指定用户文件夹, `-m`: 如果文件夹不存在就新建, `-s`: 指定用户登录时使用指定`shell`。

   ```bash
   sudo useradd -d /home/username -m -s /bin/bash username
   ```

   {{< admonition type=tip title="如有必要" open=true >}}
   递归更改用户文件夹的权限: `sudo chown -R username:usergroup /home/username`
   {{< /admonition >}}

2. 修改用户密码: 

   ```bash
   sudo passwd username
   ```

3. 参考[用户不在sudoers文件中](https://zhyoch.netlify.app/2020-6/#用户不在sudoers文件中), 使新建的用户能够使用`sudo`。

4. 检查所有用户`cat /etc/passwd`, 检查所有用户组`cat /etc/group`。

## 自定义终端

### alias

创建`~/.bash_aliases`文件, 输入以下内容: 

```bash
alias ll='ls -alF --color=auto'
alias mv='mv -i'
alias cp='cp -i'
alias rm='rm -i'
alias aria2c='aria2c -c -s16 -x16 -k1M'
```

在`~/.bashrc`中添加以下内容以加载`~/.bash_aliases`文件: 

```bash
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi
```

手动`source ~/.bashrc`以在当前终端生效, 或者在`~/.profile`中添加: 

```bash
source ~/.bashrc
```

或者

```bash
if [ "$BASH" ]; then
  if [ -f ~/.bashrc ]; then
    . ~/.bashrc
  fi
fi
```

或者

```bash
if [ -n "$BASH_VERSION" ]; then
    if [ -f "$HOME/.bashrc" ]; then
        . "$HOME/.bashrc"
    fi
fi
```

这样每次用户登录时, `~/.bashrc`文件会被自动加载生效。

{{< admonition type=tip title="如果是自己的机器" open=true >}}
自己的机器, 有root权限, 那就把这个效果应用到所有账户吧, 不必像上面那样在每个账户的目录下配置`~/.bashrc`和`~/.profile`, 只需将`aliases`添加到`/etc/profile`即可。
{{< /admonition >}}

### 自定义bash提示符颜色

参考[自定义bash提示符颜色](https://zhyoch.netlify.app/2020-11/)。效果如下: 

普通用户:

<div style="background-color:black;font-weight:bold;">
    <span style="color:white;">20:34:50</span> <span style="color:red;">bhsr</span><span style="color:white;">@</span><span style="color:red;">Debian</span> <span style="color:cyan;">/home/bhsr</span><div></div><span style="color:red;">$</span>
</div>

root用户:

<div style="background-color:black;font-weight:bold;">
    <span style="color:white;">20:34:50</span> <span style="color:red;">root</span><span style="color:white;">@</span><span style="color:red;">Debian</span> <span style="color:cyan;">/root</span><div></div><span style="color:red;">#</span>
</div>

### ls -l时间格式

在`~/.bashrc`文件中添加: 

```bash
export TIME_STYLE='+%Y-%m-%d %H:%M:%S'
```

输出示例: 

```bash
drwxr-xr-x 9 bhsr       bhsr       4096 2022-05-24 23:12:12 bhsr
```

## 修改SSH设置

1. 参考[设置 SSH 通过密钥登录](https://www.runoob.com/w3cnote/set-ssh-login-key.html), 将客户机的公钥内容追加到服务器`~/.ssh/authorized_keys`文件中。

   {{< admonition type=note title="注意权限" open=true >}}
   ```bash
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/authorized_keys
   ```
   {{< /admonition >}}

2. 更改`/etc/ssh/sshd_config`文件内容: 

   ```
   Include /etc/ssh/sshd_config.d/*.conf
   Port 12345                                   #SSH端口号
   LoginGraceTime 3s                           #若用户未成功登录, 服务器将在此时间后断开连接
   PermitRootLogin yes                          #允许root用户登录
   PubkeyAuthentication yes                     #允许使用公钥验证登录
   PasswordAuthentication no                    #禁用密码登录
   PermitEmptyPasswords no                      #禁用空密码登录
   ChallengeResponseAuthentication no           #双重认证需要
   UsePAM no                                    #插入式验证模块, 比如双重认证
   X11Forwarding no                             #禁用远程执行GUI程序
   PrintMotd no                                 #禁用登录后提示信息
   Compression no                               #网速快, 没必要压缩
   ClientAliveInterval 60                       #服务器没有从客户端收到消息后的超时时间
   ClientAliveCountMax 3                        #客户端超时次数
   UseDNS no                                    #用不到的设置, 一般设为no
   AcceptEnv LANG LC_*                          #允许客户端传递区域设置环境变量
   Subsystem sftp /usr/lib/openssh/sftp-server  #sftp
   Banner none                                  #禁用远程登录后提示信息
   AllowUsers root bhsr lighthouse              #允许登录的用户名
   ```

   > 1. ClientAliveInterval乘以ClientAliveCountMax, 即超时60s*3=180s后ssh断开连接。
   > 
   > 2. 如果设置后仍然有登录后提示, 可以清空相应文件`> /etc/motd`。

3. 重启SSH服务: 

   ```bash
   systemctl restart ssh.service
   或者
   /etc/init.d/ssh restart
   ```

4. 在防火墙关闭22端口。

## 系统镜像

安装时可以选择带有**not free firmwork**的系统镜像, 其中包含一些闭源驱动。

## 软件镜像源

替换`/etc/apt/source.list`里的内容: 

### Debian(bullseye)

参考[Debian 全球镜像站](https://www.debian.org/mirror/list), 选择相应镜像源, 比如: 

[清华大学开源软件镜像站使用帮助-Debian](https://mirrors.tuna.tsinghua.edu.cn/help/debian/)

{{< admonition type=quote title="点此展开镜像源" open=false >}}
#默认注释了源码镜像以提高 apt update 速度, 如有需要可自行取消注释

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free

#deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free

#deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free

#deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free

deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free

#deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
{{< /admonition >}}

不太推荐使用[官方源](https://wiki.debian.org/SourcesList): 

{{< admonition type=quote title="点此展开官方源" open=false >}}
#默认注释了源码镜像以提高 apt update 速度, 如有需要可自行取消注释

deb https://deb.debian.org/debian bullseye main contrib non-free

#deb-src https://deb.debian.org/debian bullseye main contrib non-free

deb https://deb.debian.org/debian-security/ bullseye-security main contrib non-free

#deb-src https://deb.debian.org/debian-security/ bullseye-security main contrib non-free

deb https://deb.debian.org/debian bullseye-updates main contrib non-free

#deb-src https://deb.debian.org/debian bullseye-updates main contrib non-free

deb https://deb.debian.org/debian bullseye-backports main contrib non-free

#deb-src https://deb.debian.org/debian bullseye-backports main contrib non-free
{{< /admonition >}}

### Ubuntu(20.04 LTS)

参考[Official Archive Mirrors for Ubuntu](https://launchpad.net/ubuntu/+archivemirrors), 选择相应镜像源, 比如: 

[清华大学开源软件镜像站使用帮助-Ubuntu](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)

{{< admonition type=quote title="点此展开镜像源" open=false >}}
#默认注释了源码镜像以提高 apt update 速度, 如有需要可自行取消注释

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse

#deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse

#deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse

#deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse

deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

#deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
{{< /admonition >}}

### 无法拉取https源

{{< admonition type=failure title="报错" open=true >}}
Certificate verification failed: The certificate is NOT trusted. The certificate chain uses expired certificate. 

Could not handshake: Error in the certificate verification.

E: 仓库 “https://mirrors.tuna.tsinghua.edu.cn/ubuntu focal Release” 没有 Release 文件。

N: 无法安全地用该源进行更新, 所以默认禁用该源。

N: 参见 apt-secure(8) 手册以了解仓库创建和用户配置方面的细节。
{{< /admonition >}}

先使用http源 (将源中的https替换为http) 安装以下软件: 

```bash
sudo apt install apt-transport-https ca-certificates
```

之后再使用https源。

## 安装RTL8821CE无线网卡驱动

可以先检测已安装的驱动: `lspci -knn`

由于Linux内核 (kernel >= 5.9) 自带的**rtw88**对RTL8821CE兼容性极差, 驱动很快就会掉, 基本无法使用, 所以需要手动安装RTL8821CE无线网卡驱动。

1. 电脑插入网线, 或者手机USB网络共享。

2. 编辑`/etc/modprobe.d/blacklist.conf`, 添加: 

   ```
   blacklist rtw88_8821ce
   ```

   这样就禁用了内核自带的**rtw88**驱动。

3. 从tomaspinho的[GitHub仓库](https://github.com/tomaspinho/rtl8821ce)下载驱动。

4. 执行命令: 

   ```bash
   sudo apt install bc module-assistant build-essential dkms
   sudo m-a prepare
   ```

5. `cd`到下载的驱动的目录, 执行以下命令: 

   ```bash
   sudo ./dkms-install.sh
   ```

6. 默认情况下, Linux可能会启用PCIe活动状态电源管理。这可能与该驱动程序冲突。编辑`/etc/default/grub`, 在`GRUB_CMDLINE_LINUX_DEFAULT`后添加`pci=noaer`, 比如: 

   ```
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash pci=noaer"
   ```

   然后更新grub: 

   ```bash
   sudo update-grub
   ```

   > 开机自检遇到某些报错时, 也可以在`GRUB_CMDLINE_LINUX_DEFAULT`后添加`pci=noaer`来解决, 这是一个在Stack Overflow常见的解决方案。

7. 重启设备。

## 常用软件

[**Chrome**](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb)

[**Clash For Windows**](https://github.com/Fndroid/clash_for_windows_pkg) / [**Clash for Windows Chinese**](https://github.com/ender-zhao/Clash-for-Windows_Chinese)

[**Qv2ray**](https://qv2ray.github.io/getting-started/)

[**Telegram**](https://desktop.telegram.org/)

[**Typora**](https://download.typora.io/linux/typora_0.11.18_amd64.deb)

[**VS Code**](https://code.visualstudio.com/)

[**OBS Studio**](https://obsproject.com/zh-cn/download)

[**命令行下载软件**](https://linux.cn/article-7369-1.html)

## fcitx输入法

{{< admonition type=warning title="注意" open=true >}}
以下方案用的输入法是`fcitx-pinyin`, 而不是`fcitx-googlepinyin`或者其他输入法。
{{< /admonition >}}

### 搜狗词库转换

参考[搜狗细胞词库转换](https://github.com/Chopong/fcitx-dict)。

1. 安装`fcitx-tools`。

2. 下载[createPYMB](https://github.com/Chopong/fcitx-dict/raw/master/createPYMB)、[gbkpy.org](https://raw.githubusercontent.com/Chopong/fcitx-dict/master/gbkpy.org)、[pyPhrase.org](https://github.com/Chopong/fcitx-dict/raw/master/pyPhrase.org)、[搜狗scel词库](https://pinyin.sogou.com/dict/), 假设都保存在`/home/用户名/下载/`。

3. ```bash
   #新建一个文件夹, 即/home/用户名/下载/orgfile
   mkdir -p orgfile
   #将scel转换为org
   for dict in *.scel; do scel2org $dict -o orgfile/$dict.org    ; done
   #复制pyPhrase.org到orgfile中
   cp pyPhrase.org orgfile
   #去重、排序、汇总
   cat orgfile/* | sort | uniq > dicts.org
   #将org转换为mb
   createPYMB gbkpy.org dicts.org 
   ```

4. 正常应生成`dicts.org`、`pybase.mb`、`pyERROR`、`pyphrase.mb`、`pyPhrase.ok`其中`pybase.mb`为拼音单字库, `pyphrase.mb`为拼音词库。

5. 将`dicts.org`、`pybase.mb`、`pyphrase.mb`移动到全局配置拼音词库`/usr/share/fcitx/pinyin/`中 (用户各自的配置拼音词库在`/home/用户名/.config/fcitx/pinyin/`) , 如果这文件夹不存在, 手动新建即可, 放好词库后, 重启Fcitx。

{{< admonition type=tip title="其他工具" open=true >}}
[深蓝词库转换](https://github.com/studyzy/imewlconverter)更加易用, 支持的词库类型也更多, 但是在Linux上使用, 需要配置**dotnet**, 可以在Windows上转换好词库再导入Linux。
{{< /admonition >}}

### 搜狗皮肤转换

网上都推荐用这个工具: [ssf2fcitx](https://github.com/VOID001/ssf2fcitx), 然而我总是被提示缺失几个头文件, 总也配置不好, 干脆就换成
[ssfconv](https://github.com/fkxxyz/ssfconv), 这个工具比较新, 而且对于ssf2fcitx还有改进。

1. 下载[搜狗皮肤](https://pinyin.sogou.com/skins/), 转换好皮肤。

2. 将皮肤文件夹放到全局设置路径`/usr/share/fcitx/skin/`, 或者用户设置路径`/home/用户名/.config/fcitx/skin/`, 如果文件夹不存在, 手动新建即可。

## Grub

{{< admonition type=warning title="注意" open=true >}}
使用PS或其他软件处理图像时, 要注意是否使用了grub支持的色彩空间和位数。
{{< /admonition >}}

### 主题

#### Plasma-light

1. Plasma-light[下载地址](https://www.gnome-look.org/p/1197062/)。
2. 参数设置参考[grub主题配置](https://blog.csdn.net/qq_27525611/article/details/104075692)、[grub参数介绍](https://blog.csdn.net/xinlan3618/article/details/78963513)。
3. 选择系统后的加载过程中, 屏幕中央会有一个一闪而过的图片, 它位于`/etc/alternatives/desktop-theme/grub/`。
4. 登录界面的背景图位于`/etc/alternatives/desktop-theme/login/`。

#### Vimix

Vimix[下载地址](https://www.gnome-look.org/p/1009236)。

### 字体

参考[Can GRUB font size be customised?](https://unix.stackexchange.com/questions/31672/can-grub-font-size-be-customised?answertab=votes#tab-top)

1. 将ttf字体转换成grub能理解的pf2字体文件。

   ```bash
   sudo grub2-mkfont -s 20 -o /目标路径/文件名.pf2 /源路径/文件名.ttf
   ```

2. 编辑`/etc/default/grub`, 添加下面一行: 

   ```
   GRUB_FONT=/目标路径/文件名.pf2
   ```

3. ```bash
   sudo update-grub
   ```

但上面的设置似乎会被主题设置覆盖从而不起作用。

#### 主题字体

就Vimix主题来说, 

1. 将生成的pf2字体文件复制到主题文件夹下。
2. 编辑`theme.txt`文件, 此主题默认使用Unifont字体, 更改为目标字体名称。

   ```
   # Show the boot menu
   + boot_menu {
     ……
     item_font = "Unifont Regular 16"
     ……
   ```

{{< admonition type=info title="注意" open=true >}}
**字体文件名**和**字体名**不是一回事, 比如微软的**等线**字体, 文件名默认为Dengl、Deng、Dengb, 而字体名为**DengXian**。
{{< /admonition >}}

## 添加软件到系统应用列表

1. 首先确定待执行文件是否有可执行权限。
2. 在任意位置右键新建一个`链接到应用程序`, 然后在**应用程序**标签下的**命令**里填入软件所在路径 (如果配置好后因为权限问题打不开软件, 可在路径后加上`--no-sandbox`) , 当然也可以直接浏览, 然后确定。
3. 之后将这个配置好的`.desktop`文件 (类似于Windows中的快捷方式) 移动到`/usr/share/applications/`即可。

## 为软件更改图标

在`/usr/share/applications/`中, 编辑要更改的软件的`.desktop`文件, 在`Icon=`后填入自定义的软件图标路径即可。

{{< admonition type=tip title="技巧" open=true >}}
为防止以后误删该图标, 也可将图标放入`/usr/share/pixmaps/`文件夹下 (第三方图标都默认存放在这里) , 然后在`Icon=`后接图片名称即可, 不用加文件后缀, 比如现在有文件`/usr/share/pixmaps/PicGo.png`, 在软件的`.desktop`文件写入`Icon=PicGo`即可。
{{< /admonition >}}

##  字体

参考[如何在linux系统上安装字体](https://www.linuxdashen.com/%E5%A6%82%E4%BD%95%E5%9C%A8linux%E7%B3%BB%E7%BB%9F%E4%B8%8A%E5%AE%89%E8%A3%85%E5%AD%97%E4%BD%93part-1), 或者直接复制Windows的字体到Linux: 

Windows系统字体路径是`C:\Windows\Fonts`, 此路径下字体文件有三种: 

1. `.fon`, 是DOS系统的字体。
2. `.ttf`, 是西文字体。
3. `.TTF`, 是中文字体。

DOS系统的字体用不上。复制Windows的`.ttf`和`.TTF`文件, 粘贴到Linux系统字体路径`/usr/share/fonts/`下的新建文件夹`WindowsFonts`。