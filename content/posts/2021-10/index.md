---
title: "NodeJS、NPM与Yarn的安装与配置"
date: 2021-03-24T20:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [NodeJS,Debian,Linux,Windows]
featuredImagePreview: ""
summary: Debian系Linux和Windows中, NodeJS、NPM与Yarn的安装与配置。
---

## NodeJS

### 手动安装

#### Debian系

1. 安装必要组件: 

   ```bash
   sudo apt-get update
   sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
   ```

2. 设置Node.js源: 

   ```bash
   curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/nodesource.gpg
   # 18.x
   echo "deb [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/nodesource.list > /dev/null
   echo "deb-src [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/nodesource.list > /dev/null
   # 16.x
   echo "deb [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_16.x $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/nodesource.list > /dev/null
   echo "deb-src [signed-by=/usr/share/keyrings/nodesource.gpg] https://deb.nodesource.com/node_16.x $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/nodesource.list > /dev/null
   ```

3. 安装Node.js: 

   ```bash
   sudo apt-get install -y nodejs
   ```

4. 检查版本: 

   ```shell
   node --version
   npm --version
   ```

#### Windows

最新尝鲜版: [https://nodejs.org/zh-cn/download/current/](https://nodejs.org/zh-cn/download/current/)

长期维护版: [https://nodejs.org/zh-cn/download/](https://nodejs.org/zh-cn/download/)

### 自动安装

和上面的手动安装效果一样。

#### Debian

- Node.js Current (v18.x):

   ```bash
   curl -fsSL https://deb.nodesource.com/setup_current.x | bash - &&\
   sudo apt-get install -y nodejs
   ```

- Node.js LTS (v16.x):

   ```bash
   curl -fsSL https://deb.nodesource.com/setup_lts.x | bash - &&\
   sudo apt-get install -y nodejs
   ```

#### Ubuntu

- Node.js Current (v18.x):

   ```bash
   curl -fsSL https://deb.nodesource.com/setup_current.x | sudo -E bash - &&\
   sudo apt-get install -y nodejs
   ```

- Node.js LTS (v16.x):

   ```bash
   curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash - &&\
   sudo apt-get install -y nodejs
   ```

检查版本: 

```shell
node --version
npm --version
```

### 配置
{{< admonition type=warning title="注意" open=true >}}
在Windows中, 需要注意Node.js安装目录的所有者和权限, 其所有者可能是`SYSTEM`, 并且其他用户的权限受到限制, 这会导致后续使用中出现问题, 比如使用npm安装软件包时需要管理员权限等等。建议按下图进行更改: 
{{< /admonition >}}

{{< image src="1.png" caption="Node.js安装目录的所有者和权限" height="auto" width="100%">}}

## NPM

### 安装

npm会随着Node.js一起被安装, 不必单独安装。

### 配置

#### cache和global

检查npm的缓存路径和全局安装路径: 

```shell
npm config get cache
npm config get prefix
```

Linux中一般不用设置, Windows中的设置: 

1. Node.js安装路径`D:\NodeJS`。

2. 创建文件夹`D:\NodeJS\node_cache`和`D:\NodeJS\node_global`。

3. **环境变量**→**用户变量**中, 删除`C:\Users\Username\AppData\Roaming\npm`。

4. **环境变量**→**系统变量**中, 新建名为`NODE_PATH`, 值为`D:\NodeJS\node_modules`的变量。

5. **环境变量**→**系统变量**→**PATH**中, 新建`D:\NodeJS`和`D:\NodeJS\node_global`。

6. 配置缓存路径和全局安装路径: 

   ```shell
   npm config set cache "D:\NodeJS\node_cache"
   npm config set prefix "D:\NodeJS\node_global"
   ```

#### npm镜像源

1. 检查当前源: 

   ```shell
   npm config get registry
   ```

2. 更换镜像源: 

   ```shell
   # 腾讯云镜像
   npm config set registry https://mirrors.cloud.tencent.com/npm/
   # 淘宝镜像源
   npm config set registry https://registry.npmmirror.com
   # 默认官方源
   npm config set registry https://registry.npmjs.org
   ```

## Yarn

### 安装

#### via npm

```shell
npm install --global yarn
yarn --version
```

#### via official package repository

1. 安装必要组件: 

   ```bash
   sudo apt-get update
   sudo apt-get install -y apt-transport-https ca-certificates curl gnupg
   ```

2. 设置Yarn源: 

   ```bash
   curl -fsSL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo gpg --dearmor -o /usr/share/keyrings/yarn.gpg

   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/yarn.gpg] https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list > /dev/null
   ```

3. 安装Yarn: 

   ```bash
   sudo apt-get update
   sudo apt-get install -y yarn
   ```

4. 检查版本: 

   ```bash
   yarn --version
   ```

### 配置

#### cache和global

检查yarn的缓存路径和全局安装路径: 

```shell
yarn config get cache-folder
yarn config get global-dir
```

Linux中一般不用设置, Windows中的设置: 

1. Node.js安装路径`D:\NodeJS`。

2. 创建文件夹`D:\NodeJS\yarn_global`和`D:\NodeJS\yarn_cache`。

3. 配置缓存路径和全局安装路径: 

   ```shell
   yarn config set cache-folder "D:\NodeJS\yarn_cache"
   yarn config set global-dir "D:\NodeJS\yarn_global"
   ```

#### Yarn镜像源

1. 检查当前源: 

   ```shell
   yarn config get registry
   ```

2. 更换镜像源: 

   ```shell
   # 腾讯云镜像
   yarn config set registry https://mirrors.cloud.tencent.com/npm/
   # 淘宝镜像源
   yarn config set registry https://registry.npmmirror.com
   # 默认官方源
   yarn config set registry https://registry.yarnpkg.com
   ```
