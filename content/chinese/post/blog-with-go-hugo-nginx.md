---
title: "基于Hugo & Nginx搭建博客"
date: 2022-03-21T20:12:33+08:00
# post thumb
images:
- "images/blog-with-go-hugo-nginx/hugo.png"
#author
author: "郁金香啊"
# description
description: "基于Hugo & Nginx搭建博客"
# Taxonomies
categories: ["tutorial"]
tags: ["Go","Hugo","Logbook"]
type: "regular"
draft: false
---

本文中，我将会简单讨论什么是静态页面生成，简单了解几种比较流行的静态页面生成器并选取其中一种名叫**Hugo**的静态页面生成器进行详细介绍。
事实上，您现在所看到的的内容正是基于**Hugo**以及其最受欢迎的主题**Log Book**所构建的。

# 目录
* [什么是静态网站生成](#what)
* [为什么仍然需要静态网站](#why)
* [常见的一些静态页面生成器](#who)
* [Hugo / Jekyll / Hexo](#jekyll-hexo-hugo)
* [准备工作](#prerequisite)
  * [第一步 - 安装Go](#install-go)
  * [第二步 - 安装Hugo](#install-hugo)
  * [第三步 - 让我们简单认识一下Hugo CLI](#hugo-cli)
    * [hugo命令](#hugo)
    * [草稿 / 未来 / 过期内容](#draft-future-expiry)
    * [hugo server / 热加载](#hugo-server-with-live-load)
* [开始网站搭建](#get-start)
  * [第一步 - 创建一个网站](#create-new-site)
  * [第二步 - 为你的网站添加主题](#adding-hugo-theme)
  * [第三步 - 为你的网站添加一些内容](#adding-site-content)
  * [第四步 - 启动Hugo Server](#start-hugo-server)
* [部署我们的网站到服务器](#deploy-to-cloud)
  * [第一步 - 换一个好看的主题](#using-hugo-theme-logbook)
  * [第二步 - 构建我们需要部署的静态资源](#generate-static-resource)
  * [第三步 - 推送文件到远程仓库](#push-to-remote)
  * [第四步 - 使用Nginx部署网站](#deploy-with-nginx)
* [踩坑记录](#question-and-answer)


## 什么是静态网站生成 <div id='what' />
所谓静态网站生成或者静态页面生成是指一种处理过程，其能够静态地生成一个网站，这个网站可能由一系列的静态的`HTML`页面组成。
通常，我们在本地环境通过这种流程生成静态的页面，然后我们将其上传至对应的服务器进行发布，当用户请求对应的资源时，我们直接将这些资源返回给用户进行展示。
整个过程中，不需要任何的服务器端渲染或者处理，客户端和服务器也不会有其他形式的数据传输，仅仅专递静态资源文件。

事实上，在互联网诞生之初，第一个网站就是静态的，那时候，服务器只能返回一些非常原始的静态的页面，并没有想`PHP`这样的脚本语言，也不依赖于任何类似`Mysql`这样的数据库管理系统。

## 为什么仍然需要静态网站 <div id='why' />
今天，我们已经拥有了海量的非常高效的软件和技术，比如服务端语言，数据库以及内容管理系统等，那么我们为什么还需要静态网站呢？
* 内容直接使用文件进行存储，不依赖于数据库
* 一个静态网站不需要服务器端渲染
* 静态网站比动态网站更快因为它不需要服务端渲染以及数据访问
* 静态网站比动态网站更加安全因为它不太可能暴露安全漏洞
* 使用`CDN`，静态网站很容易扩展
* 缓存静态文件资源比缓存动态数据以及页面等更加高效
* 对于博客这种数据，使用静态页面进行构建是再合适不过得了

## 常见的一些静态页面生成器 <div id='who' />
静态页面生成器，顾名思义，用来生成静态页面或者说静态网站的一种技术或者工具。
生成，那就意味着除了一般的静态内容维护之外，这些技术或者功能往往还能提供一些非常好用的功能特性，使我们的页面看起来更加的绚丽，维护起来也会更加的方便。

### Jekyll & Hexo & Hug0 <div id='jekyll-hexo-hugo' />
* Jekyll - 由Github构建的一款静态内容生成器，基于Ruby，用于驱动Github Page
* Hexo - 一款基于Nodejs的静态内容生成器
* Hugo - 一款基于Go语言的静态内容生成器，非常快速

## 基于Hugo & Nginx搭建博客 <div id="how" />
**Hugo**是最流行的开源静态站点生成器之一。凭借其惊人的速度和灵活性，**Hugo**让搭建网站再次变得有趣。

* 飞快的构建速度 - Hugo 是同类中最快的工具。每个页面的构建时间小于1ms时，网站的平均构建时间不到一秒钟。
* 健壮的内容管理 - Hugo 支持无限的内容类型、分类、菜单、动态 API 驱动的内容等，所有这些都无需插件。
* 短代码(Shortcodes) - 我们喜欢 Markdown 语法的漂亮、简洁，但有时我们需要更多的灵活性。Hugo 短代码满足了美观和灵活的需求。
* 内置模板 - Hugo 提供了预制的模板，可以快速完成 SEO、评论、统计和其他功能。一行代码，完成所有工作。
* 支持多语言 & i18n - Hugo 为多语言站点提供了完整的 i18n 支持，并且与 Hugo 用户喜欢的单语言站点的开发体验完全相同。
* 定制输出 - Hugo 允许以多种格式输出您的内容，包括 JSON 或 AMP，并使您可以轻松创建自己的内容。

### 准备工作 <div id="prerequisite" >
### 第一步 - 安装Go <div id="install-go" >
```shell
➜  ~  brew install go
➜  ~  go version
```

### 第二步 - 安装Hugo <div id="install-hugo" >
```zsh
#Hugo有很多种安装方式，此处我们使用从源码进行安装，因为我们需要Hugo Extended版本来启动scss文件的处理功能

#因为我们暂时不需要同这个仓库进行协作，所以此处设置depth为1，否则需要拉取大量的代码
➜  ~  mkdir ~/src && cd ~/src && git clone https://github.com/gohugoio/hugo.git --depth=1

#构建完成之后，hugo目录应该在 ~/go/bin
➜  ~  cd hugo & go install --tags extended 

#将$HOME/go/bin添加到执行路径中，也可以将此行代码添加到/etc/profile或者~/.zshrc文件中
➜  ~  export PATH="$PATH:$HOME/go/bin"

#验证是否安装成功，能多内容参见https://hugo.zcopy.site/getting-started/usage/
➜  ~  hugo help
```

### 第三步 - 让我们简单认识一下Hugo CLI <div id="hugo-cli" >
```shell
➜  ~  hugo help     
hugo is the main command, used to build your Hugo site.

Hugo is a Fast and Flexible Static Site Generator
built with love by spf13 and friends in Go.

Complete documentation is available at http://gohugo.io/.

Usage:
  hugo [flags]
  hugo [command]

Available Commands:
  completion  Generate the autocompletion script for the specified shell
  config      Print the site configuration
  convert     Convert your content to different formats
  deploy      Deploy your site to a Cloud provider.
  env         Print Hugo version and environment info
  gen         A collection of several useful generators.
  help        Help about any command
  import      Import your site from others.
  list        Listing out various types of content
  mod         Various Hugo Modules helpers.
  new         Create new content for your site
  server      A high performance webserver
  version     Print the version number of Hugo

Flags:
  -b, --baseURL string             hostname (and path) to the root, e.g. http://spf13.com/
  -D, --buildDrafts                include content marked as draft
  -E, --buildExpired               include expired content
  -F, --buildFuture                include content with publishdate in the future
      --cacheDir string            filesystem path to cache directory. Defaults: $TMPDIR/hugo_cache/
      --cleanDestinationDir        remove files from destination not found in static directories
      --config string              config file (default is path/config.yaml|json|toml)
      --configDir string           config dir (default "config")
  -c, --contentDir string          filesystem path to content directory
      --debug                      debug output
  -d, --destination string         filesystem path to write files to
      --disableKinds strings       disable different kind of pages (home, RSS etc.)
      --enableGitInfo              add Git revision, date, author, and CODEOWNERS info to the pages
  -e, --environment string         build environment
      --forceSyncStatic            copy all files when static is changed.
      --gc                         enable to run some cleanup tasks (remove unused cache files) after the build
  -h, --help                       help for hugo
      --ignoreCache                ignores the cache directory
      --ignoreVendorPaths string   ignores any _vendor for module paths matching the given Glob pattern
  -l, --layoutDir string           filesystem path to layout directory
      --log                        enable Logging
      --logFile string             log File path (if set, logging enabled automatically)
      --minify                     minify any supported output format (HTML, XML etc.)
      --noChmod                    don't sync permission mode of files
      --noTimes                    don't sync modification time of files
      --panicOnWarning             panic on first WARNING log
      --poll string                set this to a poll interval, e.g --poll 700ms, to use a poll based approach to watch for file system changes
      --printI18nWarnings          print missing translations
      --printMemoryUsage           print memory usage to screen at intervals
      --printPathWarnings          print warnings on duplicate target paths etc.
      --printUnusedTemplates       print warnings on unused templates.
      --quiet                      build in quiet mode
      --renderToMemory             render to memory (only useful for benchmark testing)
  -s, --source string              filesystem path to read files relative from
      --templateMetrics            display metrics about template executions
      --templateMetricsHints       calculate some improvement hints when combined with --templateMetrics
  -t, --theme strings              themes to use (located in /themes/THEMENAME/)
      --themesDir string           filesystem path to themes directory
      --trace file                 write trace to file (not useful in general)
  -v, --verbose                    verbose output
      --verboseLog                 verbose logging
  -w, --watch                      watch filesystem for changes and recreate as needed

Use "hugo [command] --help" for more information about a command.
```
可以看到Hugo提供了很多的功能，但是在此篇博客中，我们会跳过大部分的命令，只介绍我们可能需要用到的部分。
#### `hugo`命令 <div id="hugo" >
要说最常用的命令那一定是`hugo`了，其将你当前所在目录作为工作目录执行`hugo`。

这个操作默认会基于你当前工作目录的内容生成你的静态网站，通常这些生成的静态资源放在`/public`目录下，当然你也可以通过手动指定`publishDir`来进行控制。

`hugo`命令将你的网站渲染到`/public`目录，这样你的静态网站就可以部署到你的服务器上了。
#### 草稿 & 未来 & 过期内容 <div id="draft-future-expiry" >
**Hugo**允许你为你想要发布的内容设置`draft`，`publishdate`，`expirydate`这三个属性。
顾名思义，分别表示这个待发布的内容是否是一个草稿文件，这个待发布的内容计划的发布日期，这个待发布的内容的预计过期时间。 默认情况下，**Hugo**不会帮你发布：
1. `draft: true`，也就是目前尚且是草稿的内容
2. `publishdate: future date`，也就是计划在未来发布的内容
3. `expirydate: past date`，也就是已经过期的内容

当然了，**Hugo**也为这些特性提供了非常方便的控制方式。
你可以通过在本地开发或者生产部署时添加相应的标志来进行控制，也可以直接在你的配置文件中进行配置。
此外，在适当的时间直接调整你待发布的内容，调整其各个属性比如`draft`，`publishdate`，`expirydate`，似乎更加合理。
1. --buildDrafts    #构建文件即使`draft`等于`true`
2. --buildFuture    #构建文件即使`publishdate`是一个未来的时间，即文件尚未到达发布时间
3. --buildExpired    #构建文件即使`expirydate`是一个过去的时间，即文件已经过期

#### `hugo server` & 热加载 <div id="hugo-server-with-live-load" >
通常，当我们在构建我们的网站时，我们会一边开发可以一边预览我们的网站的实际效果，这通过一个简单的命令`hugo server`就可以搞定了。
这会为你启动一个简单的服务器将你的静态网站托管在上面，然后你就可以在浏览器中对你的网站进行预览了。

此外，**Hugo**还为我们内置了热加载的功能，这意味着你不需要安装任何其他的类库或者插件就可一边进行开发一边实时的预览的网站效果。
通常，以下目录中的文件发生变更时将会触发重新构建以及热加载。
* /static/*
* /content/*
* /data/*
* /i18n/*
* /layouts/*
* /themes/<CURRENT-THEME>/*
* config

> **PS >** 如果你正在准备的内容状态为`draft: true`，在运行`hugo server`命令时不要忘了加上`--buildDrafts`；`publishdate`，`expirydate`也是类似

当然了，我们也可以禁用掉热加载
```shell
➜  ~  hugo server --watch=false

#或者

➜  ~  hugo server --disableLiveReload

#或者
➜  ~  echo "disableLiveReload: true" >> config.yaml
```
## 开始网站搭建 <div id="get-start" >
### 第一步 - 创建一个网站 <div id="create-new-site" >
很简单，直接运行`hugo new site {your site name}`
```shell
➜  ~  hugo new site serius           
Congratulations! Your new Hugo site is created in ~/serius.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
```

让我们来简单看下我们的目录结构
```shell
➜  ~  hugo cd serius && ls -al
total 8
drwxr-xr-x  9 hugo  staff  288 Mar 22 21:12 .
drwxr-xr-x  7 hugo  staff  224 Mar 22 21:12 ..
drwxr-xr-x  3 hugo  staff   96 Mar 22 21:12 archetypes
-rw-r--r--  1 hugo  staff   82 Mar 22 21:12 config.toml
drwxr-xr-x  2 hugo  staff   64 Mar 22 21:12 content
drwxr-xr-x  2 hugo  staff   64 Mar 22 21:12 data
drwxr-xr-x  2 hugo  staff   64 Mar 22 21:12 layouts
drwxr-xr-x  2 hugo  staff   64 Mar 22 21:12 static
drwxr-xr-x  2 hugo  staff   64 Mar 22 21:12 themes
```

正如你所看到的的，Hugo为我们生成的目录结构非常的简单，也非常直观
```shell
.
├── archetypes    #当你使用hugo new命令区创建一个新的内容时，可以通过指定archetype来快速添加一些内容，比如标题，创建日期，draft：true
├── config.toml    #你的网站的配置文件
├── content    #你的网站的主体内容
├── data    #你的数据
├── layouts    ##你的布局信息
├── static    #你的静态资源比如css，js等，其实还有一个资源文件目录assets，默认情况下不会创建这个目录，这个目录一般用于存储图片文件等
└── themes    #你的网站的主体，你可以很轻松的更换一个主题，只需要将其放到这个目录并调整你的配置文件即可
```

> **PS >** 你可以在[Get Hugo Theme](https://gethugothemes.com/)查看更多主题。
> 但是你可能需要进行购买，单个主题是49到79美元，相比之下一个捆绑包才99美元（又一个价值杠杆的标志性例子）可能更划算。
> 如果你有一些功能想要尝试一些比较好看的主题，你可以联系我，我现在已经购买了整个捆绑包，我这边会免费和你协作，比如直接Contribute到你的仓库（相信不收费的话应该没有违反license了吧[Doge][Doge][Doge]）。

### 第二步 - 为你的网站添加主题 <div id="adding-hugo-theme" >
```shell
#使用Git来对你的网站进行版本控制
➜  serius  git init

#使用git submodule将一个主题themes/ananke直接添加到你的themes目录下
➜  serius  git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke

#更改的你的配置文件来启用这个主题，这样你的主题就添加好了
➜  serius  echo theme = \"ananke\" >> config.toml 
```

### 第三步 - 为你的网站添加一些内容 <div id="adding-site-content" >
你可以手动创建一些内容到指定的路径，比如`content/<CATEGORY>/<FILE>.<FORMAT>`，然后你可以手动的为其添加一些元数据，比如标题，创建时间，是否是草稿等等。

此外你也可以使用前面提到的`hugo new`命令辅助你进行创建，像前面提到的，此时会使用默认的archetype帮助你创建新的内容，可以自动帮你添加标题，创建时间，是否是草稿等元数据。

```shell
➜  serius  hugo new posts/hello-hugo.md

➜  serius  echo "# Hello Hugo" >> content/posts/hello-hugo.md
➜  serius  echo "Hello hugo, this is my first post build by hugo new command." >> content/posts/hello-hugo.md

➜  serius  cat content/posts/hello-hugo.md         
---
title: "Hello Hugo"
date: 2022-03-22T21:46:58+08:00
draft: true
---

# Hello Hugo
Hello hugo, this is my first post build by hugo new command.
```

### 第四步 - 启动Hugo Server <div id="start-hugo-server" >
然后我们就用启动Hugo Server来预览我们的网站了。

```shell
➜  serius git:(master) ✗ hugo server -D                                                                          
Start building sites … 
hugo v0.96.0-DEV-b80853de90b10171155b8f3fde47d64ec7bfa0dd+extended darwin/amd64 BuildDate=2022-03-17T21:03:27Z

                   | EN  
-------------------+-----
  Pages            | 10  
  Paginator pages  |  0  
  Non-page files   |  0  
  Static files     |  1  
  Processed images |  0  
  Aliases          |  1  
  Sitemaps         |  1  
  Cleaned          |  0  

Built in 100 ms
Watching for changes in ~/Workplace/hugo/serius/{archetypes,content,data,layouts,static,themes}
Watching for config changes in ~/Workplace/hugo/serius/config.toml, ~/Workplace/hugo/serius/themes/ananke/config.yaml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

打开浏览器，输入[http://localhost:1313/](http://localhost:1313/)，就可访问你的第一个静态网站了。

## 部署我们的网站到服务器 <div id="deploy-to-cloud" >
好了网站已经准备好了，现在我们来将我们的网站部署到我们的服务器上。此处我先假定你已经
* 购买了一个云服务器
* 申请了自己的域名
* 购买了`DNS`服务并完成了对应的域名解析
* 进行了网站的备案
* 为你的域名申请了响应的证书
* 在你的服务器上安装了Nginx并且配置好了响应的证书

即使你没有完成上述步骤也没有关系，之后我会写一篇新的文章来详细介绍上述内容。接下来我们会在上面这些已经配置完成的基础上进行。

### 第一步 - 换一个好看的主题 <div id="using-hugo-theme-logbook" >
在我们将网站部署到服务器之前，我们可以先为它装扮一个好看的主体。你可以访问[Get Hugo Theme](https://gethugothemes.com/)查看更多主题，
不过这上面大多数主题都是收费的，具体怎么免费试用，应该不用我再教你一遍了吧。好了，废话不多说，直接开始吧。

此处我们选用Hugo非常受欢迎的一款主题来进行操作，这款主题名叫[Log Book](https://docs.gethugothemes.com/logbook/). 你可以查看前面的链接来预览这个主题。
此文档中也包含了如何安装这款主题，其实很简单，之前我们是通过`Git Submodule`克隆了一款主题，现在我们在[Get Hugo Theme](https://gethugothemes.com/)上
直接下载拿到的就是主题的源文件以及对应的一个example site。

```shell
➜  logbook-hugo ls -al                   
total 48
drwx------@   8 hugo  staff    256 Jan 13 13:43 .
drwx------@ 407 hugo  staff  13024 Mar 22 20:18 ..
-rw-r--r--@   1 hugo  staff   6148 Mar 18 23:54 .DS_Store
-rw-r--r--@   1 hugo  staff   2074 Dec 29 12:51 changelog.md
-rw-r--r--@   1 hugo  staff     84 Aug 25  2021 documentation.html
-rw-r--r--@   1 hugo  staff     91 Aug 25  2021 hire-us.html
-rwxr-xr-x@   1 hugo  staff    101 Jan 22 18:05 license.html
drwxr-xr-x@   4 hugo  staff    128 Dec 29 12:49 themes
```

```shell
➜  logbook-hugo cd themes/logbook && ls -al
➜  logbook ls -al    
total 32
drwxr-xr-x@ 10 hugo  staff   320 Jan  4 11:54 .
drwxr-xr-x@  4 hugo  staff   128 Dec 29 12:49 ..
-rw-r--r--@  1 hugo  staff  6148 Mar 18 23:59 .DS_Store
drwxr-xr-x@  4 hugo  staff   128 Dec 29 12:45 .forestry
drwxr-xr-x@  3 hugo  staff    96 Dec 15 15:20 archetypes
drwxr-xr-x@  5 hugo  staff   160 Dec 15 15:20 assets
-rw-r--r--@  1 hugo  staff  2179 Dec 29 12:05 config.toml
drwxr-xr-x@ 11 hugo  staff   352 Dec 29 12:09 exampleSite
drwxr-xr-x@ 11 hugo  staff   352 Dec 29 11:55 layouts
-rw-r--r--@  1 hugo  staff   491 Dec 29 12:47 netlify.toml
```

```shell
➜  logbook cd exampleSite && ls -al
total 24
drwxr-xr-x@ 11 hugo  staff   352 Dec 29 12:09 .
drwxr-xr-x@ 10 hugo  staff   320 Jan  4 11:54 ..
-rw-r--r--@  1 hugo  staff  6148 Jan  4 11:55 .DS_Store
-rw-r--r--@  1 hugo  staff     0 Dec 15 15:20 .hugo_build.lock
drwxr-xr-x@  4 hugo  staff   128 Dec 29 12:09 assets
drwxr-xr-x@  4 hugo  staff   128 Jan  4 11:55 config
drwxr-xr-x@  4 hugo  staff   128 Dec 26 12:20 content
drwxr-xr-x@  4 hugo  staff   128 Dec 15 15:20 i18n
-rw-r--r--@  1 hugo  staff   421 Dec 29 12:47 netlify.toml
drwxr-xr-x@  3 hugo  staff    96 Dec 29 11:48 resources
drwxr-xr-x@  5 hugo  staff   160 Dec 15 15:20 static
```

好了，上面就是整个主题的大致结构了。那么我们只需要
* 复制`logbook`到你的网站的themes目录下
* 复制`exampleSite`目录下的所有内容到你的网站的根目录
* 删除`themes/logbook/exampleSite`目录下的所有内容
* 更改配置文件，调整使用的主题的名称为`logbook`  
* 执行`hugo server -D`, 打开你的浏览器，然后**BOOM！！！**

好的，到此我们新的主题就已经更换好了，而且，由于我们使用了log book提供的example site，所以整个网站看起来已经相当的丰富了。
然后你就可以尽情探索这一款主题了，各种风格的布局，各个不同的模块等等。

### 第二步 - 构建我们需要部署的静态资源 <div id="generate-static-resource" >
好了，让我们回归主题，现在我们已经准备好了我们想要发布的内容，现在就是将其构建为静态资源。

在此之前，我们需要做一点小小的配置改动。

关于更加完整的Hugo的配置，详情请参考[Hugo Configuration](https://gohugo.io/getting-started/configuration/)。
关于更加完整的Log Book的配置，详情请参考[Log Book Configuration](https://docs.gethugothemes.com/logbook/basic-configuration/)。

此处我们需要调整的配置是`baseUrl`。这个配置是你的网站的基础路径。
为什么需要调整这个呢，因为我们的整个博客框架里面充斥着大量的连接，方便我们快速的进行导航，比如从主页跳转的某一篇文章，或者从主页直接跳转到你的个人信息等等。
所以我们需要告诉我们的静态资源生成器，我们的网站的根路径是什么，比如` https://www.example.com/ `，
这样它就知道当我们跳转时，是应该从` https://www.example.com/home `跳转到` https://www.example.com/me `，而不是`http://localhost:1313/me`.

```shell
#调整baseUrl为你的网站地址，比如baseURL = "https://www.tlst.cc/"
➜  themes vi /config/_default/config.toml

#生成静态文件
➜  themes hugo -D
```

此时我们会发现，目录下面多了一个`public`文件夹，这就是我们默认的静态资源生成到的文件目录。

### 第三步 - 推送文件到远程仓库 <div id="push-to-remote" >
```shell
#设置你的仓库地址
➜  serius git remote set-url origin https://github.com/{your repo here}.git

#添加并提交
➜  themes git add -A && git commit -m "{your commit message here}"

#推送到远程
➜  themes git branch --set-upstream-to=origin/master master && git push
```

> **PS >** 此处我将构件好的`public`目录直接推送到了远程，其实本来可以只推送内容，然后在服务器上安装Go和Hugo然后再生成相应的静态资源。
> 但是博主使用的Linux服务器操作系统是CentOS 7，在CentOS 7上目前没有extended版本的hugo可用。
> 我曾尝试基于源码编译，但是执行Go install时 由于一些政策限制无法正常下载依赖的类库等无法构建。
> 我也曾尝试在本地用Docker启动对应的CentOS环境进行源码编译，但是最终构建失败，一些版本的依赖无法正确解析。最终只能放弃。

### 第四步 - 使用Nginx部署网站 <div id="deploy-with-nginx" >
最后一步就是使用Nginx部署我们的网站了。这一步相对来说就非常简单了，只需要将我们的源码克隆到我们的服务器上，然后再将`public`目录复制到Nginx默认的静态资源根目录`/usr/share/nginx/html`就好。

我也曾尝试调整nginx配置将`root`直接指向Git Repo的`public`目录，但是Nginx访问时直接`403 Forbidden`了，目前尚未花时间进行排查。

好了，话不多说，先直接看我的Nginx的配置文件，其中包含了证书配置的部分，如果你暂时没有申请证书，那么可以先暂时忽略。
```shell
➜  ~ cat /etc/nginx/nginx.conf 

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;
    
    include /etc/nginx/conf.d/*.conf;


    server {
        listen 80;
        server_name www.tlst.cc;
        rewrite ^(.*)$ https://$host$1; #将所有HTTP请求通过rewrite指令重定向到HTTPS。
        location / {
            index index.html index.htm;
        }
    }

    server {
        listen 443 ssl;
        server_name {your domain}; #需要将yourdomain.com替换成证书绑定的域名。
        root html;
        index index.html index.htm;
        ssl_certificate certs/{your domain}.pem;  #需要将cert-file-name.pem替换成已上传的证书文件的名称。
        ssl_certificate_key certs/{your domain}.key; #需要将cert-file-name.key替换成已上传的证书密钥文件的名称。
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #表示使用的TLS协议的类型。
        ssl_prefer_server_ciphers on;
        location / {
            try_files $uri $uri/ =404;
            add_header Cache-Control "public, max-age=3600";
        }

        location ~ \.(?:jpg|jpeg|gif|png|ico|cur|gz|svg|mp4|ogg|ogv|webm|htc|webp)$ {
            add_header Cache-Control "public, s-maxage=7776000, max-age=86400";
        }   

        location ~ \.(css|js)$ {
            add_header Cache-Control "public, max-age=31536000";
        }   

        location /assets/fonts/ {
            add_header Cache-Control "public, s-maxage=7776000, max-age=86400";
        }
    }
}
➜  ~ 
```

接下来让我们部署我们的静态网站到我们的服务器。
```shell
#克隆你的仓库
~ git clone https://github.com/{your repo here}.git

#复制你的public文件目录到nginx的静态资源root
~ cd {your repo name} && rm -rf /usr/share/nginx/html && cp public /usr/share/nginx/html

#重新加载一下nginx
~ nginx -s reload
```

OK，至此，我们的静态网站就搭建起来啦~

## 踩坑记录 <div id="question-and-answer" >
* baseUrl的设置
  * 通过跳转的链接发现的这个问题，改成网站基础路径就好了
* CentOS 7无法构建Hugo
  * 目前只能将构建好的public静态资源目录推送到Git Repo
* 构建静态资源时，也就是 `hugo -D`时，有一次构建出来的所有的图片文件（后缀为`webp`）内容均为空，
  导致http请求返回Code 200但是实际的Content Length为0，一度以为是Nginx除了问题，知道最后我
  执行了一下`ls -al`，看到了文件的实际大小。 
  * 临时的解决方案就是构建之前，先全部删除，不过之后还想研究下增量构建的，比如只构建未来发布的内容，
  这个通过`publishdate`很容易控制的，这样的话，由于我们把public目录直接上传上去导致的每次git
  提交内容巨多的问题就解决了。