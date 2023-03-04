---
title: "Nginx部署Hugo站点"
date: 2022-03-18T11:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Blog,Debian,Git,Hugo,Linux,Nginx]
featuredImagePreview: "/2022-8/featuredImagePreview.svg"
summary: 将本地Git仓库提交到位于云服务器的Remote仓库, 通过Git Hooks在接收到push行为后, 运行脚本以达成构建Hugo博客、同步到多个Git Repo源等多个目的。
---

{{< image src="step.png" caption="流程" height="auto" width="100%">}}

虽然通过[GitHub Actions](https://zhyoch.netlify.app/2021-3/)部署到GitHub Pages很方便, 此外[CircleCI](https://circleci.com/)、[Cloudflare Pages](https://pages.cloudflare.com/)、[Netlify](https://www.netlify.com/)、[Travis CI](https://www.travis-ci.com/)、[Vercel](https://vercel.com/)等服务也很易用, 但它们的访问速度都差强人意, 就算使用Cloudflare的CDN, 提升效果也并不显著。而如果使用国内CDN, 需要有备案域名, 所以干脆把整个站点都放到国内服务器。

{{< admonition type=info title="运行环境" open=true >}}
- 系统: Debian 11。
- 用户: 普通用户, 用户名为`bhsr`。
{{< /admonition >}}

## 安装软件

- Git: `sudo apt install git`
- Nginx: `sudo apt install nginx`
- Hugo: 不推荐使用`apt`方式安装, 因为版本很落后。推荐使用`dpkg`方式安装从Hugo的[GitHub Releases](https://github.com/gohugoio/hugo/releases/latest)下载的`deb`

## 配置仓库

### 服务器中的Remote仓库

> 以下是在云服务器中的操作。

{{< admonition type=question title="是否需要新建git用户" open=true >}}
由于SSH方式连接Git仓库, 参数格式是这样的: `ssh://username@<domain|ip>:[port]/xxx.git`, 所以: 
- 如果新建名为`git`的用户, 那么之后Git仓库地址是`git@<domain|ip>:[port]/xxx.git`, 符合习惯, 比如常用的`git@github.com`。
- 直接使用现有的普通用户, 比如`bhsr`, 之后Git仓库地址则是`ssh://bhsr@<domain|ip>:[port]/xxx.git`。
{{< /admonition >}}

1. 初始化裸仓库: 

    ```bash
    cd /home/bhsr/git-repos/blog
    git init --bare .git
    ```

2. 编辑Git Hooks: 

    ```bash
    vim .git/hooks/post-receive
    ```

    粘贴以下内容后保存退出: 

    ```bash
    #!/bin/bash
    
    BRANCH="master"
    GIT_DIR="/home/bhsr/project/blog/.git"
    TARGET="/home/bhsr/project/blog"
    
    read oldrev newrev ref
    
    if [ "$ref" == "refs/heads/$BRANCH" ]; then
        echo "$ref已成功接收, 正在部署$BRANCH分支"
        git --work-tree="$TARGET" --git-dir="$GIT_DIR" pull
        cd $TARGET
        rm -rf public
        hugo --gc --minify
    else
        echo "$ref已成功接收, 但只有$BRANCH分支更改时才进行部署"
    fi
    
    git push -f github
    git push -f gitee
    ```
    
3. 赋予`post-receive`可执行权限: 
   
    ```bash
    chmod +x .git/hooks/post-receive
    ```

### 服务器中的生产环境仓库

> 以下是在云服务器中的操作。

{{< admonition type=question title="服务器有必要两个仓库吗" open=true >}}
为了方便搞事, 比如自动更新twikoo、同步到多个Git Repo源等, 所以服务器上有两个Git仓库, 一个是Remote, 一个是生产环境, `nginx.conf`中`location`的`root`路径指向的正是生产环境。

如果只需要在本地`push`到Remote后, 自动构建Hugo博客, 可以从Remote的`.git`中`checkout`出仓库文件到`.git`的父目录中, 然后配置`nginx.conf`中`location`的`root`路径指向`.git`的父目录。

```bash
#!/bin/bash

BRANCH="master"
GIT_DIR="/home/bhsr/git-repos/blog/.git"
TARGET="/home/bhsr/git-repos/blog"

read oldrev newrev ref

if [ "$ref" == "refs/heads/$BRANCH" ]; then
    echo "$ref已成功接收, 正在部署$BRANCH分支"
    git --work-tree="$TARGET" --git-dir="$GIT_DIR" checkout -f "$BRANCH"
    cd $TARGET
    rm -rf public
    hugo --gc --minify
else
    echo "$ref已成功接收, 但只有$BRANCH分支更改时才进行部署"
fi
```
{{< /admonition >}}

1. 参考[Git Config](https://zhyoch.netlify.app/2020-7/)为当前用户进行设置。

2. 复制当前用户的公钥, 粘贴到`/home/bhsr/.ssh/authorized_keys`文件里, 如果有多个公钥, 则一行一个。

3. 将`/home/bhsr/git-repos/blog/.git`这个仓库克隆到`/home/bhsr/project`: 

    ```bash
    cd /home/bhsr/project/
    git clone ssh://bhsr@localhost:[port]/home/bhsr/git-repos/blog/.git
    ```

4. 配置仓库: 

    ```bash
    git remote add github git@github.com:zhyoch/blog.git
    git remote add gitee git@gitee.com:zhyoch/blog.git
    ```
    
5. `git remote -v`, Remote应该包括GitHub、Gitee和服务器中的Remote仓库: 

    ```bash
    gitee   git@gitee.com:zhyoch/blog.git (fetch)
    gitee   git@gitee.com:zhyoch/blog.git (push)
    github  git@github.com:zhyoch/blog.git (fetch)
    github  git@github.com:zhyoch/blog.git (push)
    origin  ssh://bhsr@localhost:[port]/home/bhsr/git-repos/blog/.git (fetch)
    origin  ssh://bhsr@localhost:[port]/home/bhsr/git-repos/blog/.git (push)
    ```

6. 注意权限: 

    ```bash
    chmod 700 /home/bhsr/.ssh
    chmod 600 /home/bhsr/.ssh/*
    ```

### 本地电脑中的Git仓库

> 以下是在本地电脑中的操作。

1. 克隆, 并设置仓库: 

    ```bash
    git clone ssh://bhsr@<domain|ip>:[port]/home/bhsr/git-repos/blog/.git
    ```
    
2. 存储、提交和推送: 

    ```bash
    git add -A .
    git commit -m "测试"
    git push --set-upstream origin master
    git push [-f]
    ```

## 配置Nginx

不同系统中, Nginx配置文件的路径不同, 在Debian中的路径是`/etc/nginx/nginx.conf`。

1. 备份`/etc/nginx/nginx.conf`文件: 

    ```bash
    sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
    ```

2. 编辑`/etc/nginx/nginx.conf`文件: 

    ```bash
    sudo vim /etc/nginx/nginx.conf
    ```

3. 编辑`/etc/nginx/nginx.conf`, 以下简单设置仅供参考: 

    ```nginx
    user www-data;
    worker_processes 2;
    error_log /dev/null;
    pid /run/nginx.pid;
    worker_rlimit_nofile 10240;
    
    events {
        use epoll;
        worker_connections 10240;
    }
    
    http {
        include /etc/nginx/mime.types;
        default_type application/octet-stream;
        charset utf-8;
        autoindex off;
        sendfile on;
        tcp_nopush on;
    
        server_names_hash_bucket_size 64;
        client_header_buffer_size 4k;

        log_format main '[$time_iso8601] $remote_addr '
                        '$request_method $scheme://$host$request_uri $status '
                        '"$http_user_agent"';

        access_log /var/log/nginx/access.log main;
        open_log_file_cache max=1000 inactive=10s min_uses=1 valid=60s;
    
        gzip on;
        gzip_comp_level 5;
        gzip_min_length 1k;
        gzip_buffers 4 16k;
        gzip_proxied any;
        gzip_types
            application/atom+xml
            application/javascript
            application/json
            application/rss+xml
            application/xhtml+xml
            application/x-javascript
            application/xml
            image/svg+xml
            text/css
            text/html
            text/plain
            text/xml;
        gzip_static on;  
        gzip_disable "MSIE [1-6]\.";
        gzip_vary on;

        server {
            listen 80 default_server;
            listen [::]:80 default_server;
            server_name 49.233.38.143;
            return 444;
        }
    
        server {
            listen 443 default_server ssl http2;
            listen [::]:443 default_server ssl http2;
            server_name 49.233.38.143;
            ssl_reject_handshake on;
        }
    
        server {
            listen 80;
            listen [::]:80;
            server_name bahuangshanren.tech www.bahuangshanren.tech;
            return 301 https://www.bahuangshanren.tech$request_uri;
        }
    
        server {
            listen 443 ssl http2;
            listen [::]:443 ssl http2;
            server_name bahuangshanren.tech www.bahuangshanren.tech;
            if ( $host != 'www.bahuangshanren.tech' ){
                return 301 https://www.bahuangshanren.tech$request_uri;
            }

            # 此处pem由站点证书(site.pem)和中间证书(intermediate.pem)组成
            ssl_certificate /usr/share/nginx/certs/www.bahuangshanren.tech.pem;
            ssl_certificate_key /usr/share/nginx/certs/www.bahuangshanren.tech.key;
            ssl_session_timeout 1d;
            ssl_session_cache shared:MozSSL:10m;
            ssl_session_tickets on;
            # 使用命令`openssl dhparam -out dhparam.pem 2048`生成
            ssl_dhparam /usr/share/nginx/certs/dhparam.pem;
            ssl_protocols TLSv1.2 TLSv1.3;
            ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
            ssl_prefer_server_ciphers on;
            add_header Strict-Transport-Security "max-age=31536000" always;
            ssl_stapling on;
            ssl_stapling_verify on;
            # 此处pem由中间证书(intermediate.pem)和CA根证书(root.pem)组成
            ssl_trusted_certificate /usr/share/nginx/certs/bahuangshanren.tech-chain.pem;
    
            location / {
                root /home/bhsr/project/blog/public;
                expires 3d;
                valid_referers none blocked server_names 
                    *.bahuangshanren.tech www.baidu.com www.bing.com duckduckgo.com www.google.com www.sogou.com;
                if ($invalid_referer) {
                    return 444;
                }
            }
    
            location ~ .*\.(aspx|php)$ {
                access_log off;
                log_not_found off;
                return 444;
            }
    
            error_page 404 /404.html;
        }
    }
    ```

4. 更改Nginx服务器的配置文件后, 重新启动或重新加载服务之前测试配置是否存在任何语法或系统错误: 

    ```bash
    sudo nginx -t
    ```

5. 重载Nginx配置: 

    ```bash
    sudo nginx -s reload
    ```

    或者重启Nginx服务: 

    ```bash
    sudo systemctl restart nginx.service
    ```

6. 设置Nginx服务开机自启动: 

    ```bash
    sudo systemctl enable nginx.service
    ```

7. 查看Nginx服务状态: 

    ```bash
    sudo systemctl status nginx.service
    ```

至此, 网站能够正常访问, 且访问`http://bahuangshanren.tech/`、`http://www.bahuangshanren.tech/`、`https://bahuangshanren.tech/`, 都会跳转到`https://www.bahuangshanren.tech/`。

{{< admonition type=quote title="参考链接" open=true >}}
1. [Nginx官方文档](http://nginx.org/en/docs/)
2. [Nginx配置详解](https://www.w3cschool.cn/nginx/nginx-d1aw28wa.html)
{{< /admonition >}}