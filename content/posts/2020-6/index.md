---
title: "Debian使用问题"
date: 2020-07-10T20:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Debian,Linux]
featuredImagePreview: "/2020-6/featuredImagePreview.svg"
summary: 收录我在Debian系Linux中遇到的问题及其解决方法。
---

## 用户不在sudoers文件中

### 解决方法

1. 更改`sudoers`文件权限: 

   ```bash
   sudo chmod 644 /etc/sudoers
   ```

2. 编辑`/etc/sudoers`, 在`root ALL=(ALL:ALL) ALL`下添加一行`你的用户名 ALL=(ALL:ALL) ALL`, 比如: 

   ```
   root ALL=(ALL:ALL) ALL
   zhyoch ALL=(ALL:ALL) ALL
   ```

## 新建用户下终端难用

在新建的用户下使用命令终端, 出现方向键无法调出历史命令、tab键也无法补全输入命令等现象, 总之很不正常。

### 原因

新建用户时忘记指定用户运行`shell`: 

```bash
useradd -s /bin/bash username
```

### 解决方法

查看`/etc/passwd`, 发现该新建用户使用的`shell`为`/bin/sh`, 比如: 

```
username:x:1002:1003::/home/username:/bin/sh
```

而一般使用的`shell`为`/bin/bash`, 所以将该用户的`/bin/sh`修改为`/bin/bash`即可: 

```
username:x:1002:1003::/home/username:/bin/bash
```

**推荐**直接使用`usermod`指定用户运行`shell`: 

```bash
usermod -s /bin/bash username
```

## Linux开机速度慢

### 解决方法

1. 查看启动速度: 

   ```bash
   systemd-analyze
   ```

2. 查看各个自启动服务占用时间: 

   ```bash
   systemd-analyze blame
   ```

3. 禁用启动服务, 比如: 

   ```bash
   sudo systemctl disable snapd.service
   sudo systemctl disable snapd.seeded.service
   sudo systemctl disable snapd.apparmor.service
   sudo systemctl disable snapd.socket
   sudo systemctl disable NetworkManager-wait-online.service
   sudo systemctl disable networkd-dispatcher.service
   sudo systemctl disable ModemManager.service
   sudo systemctl disable pppd-dns.service
   ```

   有些服务`systemctl disable`无效, 需要使用`systemctl mask`, 比如: 

   ```bash
   sudo systemctl mask avahi-daemon.service
   ```

4. 另外, 检查开机启动项`/etc/xdg/autostart/`。

## 无法调节音量

### 场景

在有声音输出的前提下, 无法调节音量。

### 解决方法

- 使用`alsamixer`命令直接调节。

- 或者安装**Kmix**音量调节器: 

   ```bash
   sudo apt-get install kdemultimedia kmix
   ```

## 锁屏后黑屏/合盖后无法唤醒

### 解决方法

参考[解决ubuntu合盖后无法唤醒](https://www.linuxdiyf.com/linux/18722.html)。

1. 安装laptop-mode: 
   
   ```bash
   sudo apt install laptop-mode-tools
   ```

2. 判断Laptop是否启用了laptop_mode模式, 如果显示结果为0, 则表示未启动, 如果为非0的数字则表示启动了: 

   ```bash
   cat /proc/sys/vm/laptop_mode
   ```

3. 启动laptop_mode: 

   修改配置文件`/etc/default/acpi-support`, 更改`ENABLE_LAPTOP_MODE=true`。

   {{< admonition type=question title="未找到? " open=true >}}
   有些用户在`/etc/default/acpi-support`中未找到`ENABLE_LAPTOP_MODE=true`被注释的项.看文件最后一行的提示用户在`/etc/laptop-mode/laptop-mode.conf`中进行配置。
   
   于是在该文件中查找`ENABLE_LAPTOP_MODE_ON_BATTERY`、`ENABLE_LAPTOP_MODE_ON_AC`、`ENABLE_LAPTOP_MODE_WHEN_LID_CLOSED`, 分别是当**用电池**、**外接电源**、**合上显示屏**的时候是否启用LAPTOP_MODE, 全部设置为 1 就可以了。
   {{< /admonition >}}

4. 执行`sudo laptop_mode start`启动laptop_mode之后, 在Ubuntu挂起后, 基本上就不会遇到无法唤醒的情况了。

5. 再次判断Laptop是否启用了laptop_mode模式: 

   ```bash
   cat /proc/sys/vm/laptop_mode
   ```

## 缺失firmware

### 场景

{{< admonition type=failure title="报错" open=true >}}
Possible missing firmware /lib/firmware/rtl_nic/rtl8168e-2.fw for module r8169

Possible missing firmware /lib/firmware/i915/glk_dmc_ver1_04.bin for module i915
{{< /admonition >}}

### 解决方法

参考[Possible missing firmware /lib/firmware/i915/* for module i915](https://unix.stackexchange.com/questions/556946/possible-missing-firmware-lib-firmware-i915-for-module-i915)。

对于Debian: 

```bash
sudo apt install firmware-linux
```

对于Ubuntu: 

```bash
sudo apt install linux-firmware
```

## 配置环境变量

### 场景

{{< admonition type=failure title="dpkg报错" open=true >}}
dpkg: warning: 'ldconfig' not found in PATH or not executable.

dpkg: warning: 'start-stop-daemon' not found in PATH or not executable.

dpkg: error: 2 expected programs not found in PATH or not executable.

Note: root's PATH should usually contain /usr/local/sbin, /usr/sbin and /sbin.
{{< /admonition >}}

### 解决方法

编辑`/root/.bashrc`, 并添加以下内容: 

```
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

## 软件无法启动

### 场景

1. root身份登录系统时, 无法运行某些软件 (如Google Chrome) , 而普通用户可以打开; 

2. 某些软件普通用户也无法运行 (如PicGo的AppImage) 。

### 解决方法

以Google Chrome为例: 

  1. 找到`usr/share/applications`中的Google Chrome启动文件, 右击用文本编辑器打开; 
  2. 在`Exec=typora %U`后面添加` --no-sandbox` (注意`--`的前面有个空格) 。

## 双系统时间不一致

### 解决方法

修改Windows硬件时钟为**UTC**时间, 以管理员身份打开**PowerShell**, 运行以下命令: 

```bash
> Reg add HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation /v RealTimeIsUniversal /t REG_DWORD /d 1
```

或者打开**注册表编辑器**, 定位到`计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\TimeZoneInformation`目录下, 新建一个`DWORD`类型, 名称为`RealTimeIsUniversal`的键, 并修改键值为`1`即可。

## 双系统磁盘读写问题

### Windows磁盘挂载到Linux

#### 场景

Windows磁盘挂载到Linux后, 有时处于只读状态。

#### 解决方法

- 从grub界面进入Windows, 然后从Windows重启, 而不是直接关机。

   虽然不方便, 但这也是解决问题的最快方法, 不需要像其他解决方法那样长期更改任何内容。Windows重启时, 不会在下次启动时使用“快速启动”功能。这意味着它不会进入休眠状态、获取系统运行状态的快照或将任何内存数据保存到磁盘。分区上没有休眠数据, 这意味着可以安全地写入到分区上, Linux会识别出这一点。

- 或者禁用快速启动。

   如果常常需要从Linux写入到Windows分区上, 可以选择这个方法。**缺点是Windows需要更长的开机引导时间**。要禁用快速启动, 可以在设置或者控制面板里面找**电源选项**。

以上两种方法是安全的做法, 当然也可以在网上搜索使用Linux命令的方法来解决问题。

### Linux磁盘挂载到Windows

#### 解决方法

在Windows上使用[Paragon ExtFS for Windows](https://china.paragon-software.com/home-windows/extfs-for-windows/download.html)。
