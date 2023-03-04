---
title: "GitHub Actions部署Hugo站点"
date: 2021-03-13T20:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Blog,Hugo]
featuredImagePreview: "/2021-3/featuredImagePreview.svg"
summary: 双仓库模式或双分支模式部署Hugo站点。
---

## GitHub Actions自动部署Hugo

### 双仓库模式

使用私有仓库存放博客源码, 将Hugo构建好的`public`目录推送到公有仓库来发布, 参考`peaceiris/actions-gh-pages`的[README](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-deploy-to-external-repository-external_repository)。

1. 创建一个私有仓库用来存放博客源码。
2. 创建一个公有仓库用来发布博客。
3. 创建一个个人[token](https://github.com/settings/tokens), 名字为了方便记住它的用途, **可以**叫做**blog**。
4. 到期日期选择**No expiration**。
5. 作用域选择**repo**和**workflow**。
6. 生成token, 复制它的值。
7. 到博客源码仓库的**Settings**→**Secrets**中新建一个**Actions secrets**, 名为**blog**, **Value**粘贴上一步的个人token的值。
8. 在博客源码仓库的根目录下创建`.github/workflows/gh-pages.yml`文件, 自定义使用以下代码, 然后提交: 

```yaml
name: GitHub Pages

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3.0.0
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2.4.13
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --gc --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3.7.3
        with:
          github_token: ${{ secrets.blog }}
          external_repository: #用来发布博客的公有仓库名称
          publish_branch: master
          publish_dir: ./public
          cname: #填写自定义域名
```

### 双分支模式

1. 创建一个公有仓库用来存放博客源码。
2. 在博客源码仓库的根目录下创建`.github/workflows/gh-pages.yml`文件, 自定义使用以下代码, 然后提交: 

```yaml
name: GitHub Pages

on:
  push:
    branches:
      - master

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3.0.0
        with:
          submodules: false
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2.4.13
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --gc --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3.7.3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }} #GITHUB_TOKEN不需要手动设置, GitHub会分配临时token
          publish_dir: ./public
          cname: #填写自定义域名
```

{{< admonition type=warning title="警告" open=true >}}
参考文章[GitHub Pages使用问题](https://zhyoch.netlify.app/2022-5/), 如果CNAME设置为`www`域名, 那么需要如下设置DNS: 

| 类型  | 名称                  | 内容                      |
| ----- | --------------------- | ------------------------- |
| CNAME | www                   | bahuangshanren.github.io  |
| CNAME | bahuangshanren.tech | www.bahuangshanren.tech |

{{< /admonition >}}

## 其他部署方式

1. [CircleCI](https://circleci.com/)、[Cloudflare Pages](https://pages.cloudflare.com/)、[Netlify](https://www.netlify.com/)、[Travis CI](https://www.travis-ci.com/)、[Vercel](https://vercel.com/)等。

{{< admonition type=warning title="警告" open=true >}}
Cloudflare Pages的`.pages.dev`域名似乎已被墙, 如果使用Cloudflare Pages, 建议自定义域名, 然后DNS设置为Cloudflare代理。
{{< /admonition >}}

2. 使用Nginx在自己服务器上部署, 参考[Nginx部署Hugo站点](https://zhyoch.netlify.app/2022-8/)。

{{< admonition type=quote title="参考链接" open=true >}}
1. [peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo)
2. [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)
{{< /admonition >}}
