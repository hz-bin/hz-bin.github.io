---
title:  "如何搭建个人博客站点"
author: wangbin
categories: [ Blog ]
tags: [ Blog ]
key: k1628870400
pageview: true
---

## 1、如何使用 github page 部署个人博客
- 登录 [github](https://github.com/)，点击右上角新建仓库，仓库名填 `username.github.io`，其中 `username` 填自己的 `github` 名称，例如博主的是 `hz-bin`，则仓库名为 `hz-bin.github.io`。

![](/assets/images/blog1.png)

- 进入 `Settings->Pages` 页面，`Source` 选择 `master`，然后点击 `Save`，则完成部署，即可通过 [https://hz.bin.github.io](https://hz.bin.github.io) 进行访问了。

![](/assets/images/blog2.png)
![](/assets/images/blog3.png)

## 2、如何使用 jekyll 和 TeXt 主题编辑博客内容
- 本地部署 `jekyll` 环境参考 [jekyll 中文文档](https://www.jekyll.com.cn/docs/)
- 本地克隆 [TeXt 主题](https://github.com/kitian616/jekyll-TeXt-theme)，然后进入到 `jekyll-TeXt-theme` 目录，如果有 `Gemfile.lock` 文件，先删除该文件。
- 然后在命令行中先执行 `gem install bundler jekyll` 命令，再执行 `bundle exec jekyll serve`，则本地博客搭建完成
- 浏览器中输入 `http://localhost:4000/` 即可访问博客内容。
- **如何新增一篇博客**：在 `_posts` 目录中新建 `markdown` 格式文件，文件名格式为 `yyyy-mm-dd-blogname.md`。`jekyll` 会自动进行构建。

## 3、如何配置 gitalk 作为博客的评论系统
- 首先需要新建一个 [OAuth application](https://github.com/settings/applications/new)，如下图所示：

![](/assets/images/blog4.png)

- 点击 `Generate a new client secret`：

![](/assets/images/blog5.png)
![](/assets/images/blog6.png)

- 将 `Client ID` 和 刚刚生产的 `Client secrets` 拷贝到 `_config.yml` 文件中，其他参数如图所示：

![](/assets/images/blog7.png)

配置完成之后，登录 `gitalk` 时可能会出现403错误，参考 [Gitalk 评论登录 403 问题解决](https://cuiqingcai.com/30010.html) 和 [在cloudflare上创建一个免费的在线代理来解决gitalk授权登录报403问题](https://www.chenhanpeng.com/create-own-cors-anywhere-to-resolve-the-request-with-403/#create-cors-by-self)


## 4、如果配置 leancloud 记录文章访问次数
- 首先进入 [LeanCloud](https://console.leancloud.cn/apps)，新建一个应用，应用名称自己随便填。然后进入到设置界面，如下所示，`_config.yml` 配置中，`pageview` 的 `provider` 填 `leancloud`，`leancloud` 的 `app_id` 和 `app_key` 分别填应用凭证界面的 `AppID` 和 `AppKey`。

![](/assets/images/blog8.png)
![](/assets/images/blog9.png)

- `app_class` 填 `Blog` 或者其他的名字，`LeanCloud` 会用该名称在 **数据存储 -> 结构化数据** 中创建Class，相当于存储访问次数的表。

![](/assets/images/blog10.png)

- 修改 `_includes/pageview-providers/leancloud/leancloud.js`，如下所示，`serverURLs` 改为 `serverURL`，值改为上面应用凭证中的 `REST API 服务器地址`。
![](/assets/images/blog11.png)

可参考官方文档：
> 初始化：https://leancloud.cn/docs/sdk_setup-js.html#hash20935048

> 数据存储入门教程 · JavaScript：https://leancloud.cn/docs/leanstorage-started-js.html