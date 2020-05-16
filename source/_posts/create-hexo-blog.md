---
title: 使用GitHub + hexo搭建个人博客
date: 2017-04-04 04:04:06
tags: hexo
categories: hexo
toc: true # 是否启用内容索引
---

### 本文环境

- git 2.18
- node v10.16.0

### 安装hexo

``` bash
npm install -g hexo-cli
```

### github上创建仓库

仓库名：username.github.io 仓库名必须为user + .github.io，这个名字也是将来博客的访问地址
![github创建仓库](/img/create-repository.jpg)

### 博客的创建

#### 初始化

```bash
hexo init xuyz-blog
```

会生成一个名为xuyz-blog的文件夹，就是将来博客的配置及存放博文的地方

#### 基础配置

打开xuyz-blog/_config.yml文件进行基础配置，以下只举几个关键的配置

```yml
# Site
title: xuyz-blog # 博客名字
subtitle: '鲜衣怒马少年时，一日看尽长安花' # 小标题可以写自己喜欢的话
description: '光景不待人，须臾发成丝' # 博客描述
keywords: xuyangzhou
author: xuyangzhou # 作者名字
language: zh-CN # 语言

url: / # 自己购买的域名网址（没有可不写）

theme: landscape # 主题的名字

deploy:
  type: git # 使用git发布
  repo: https://github.com/xuyangzhou/xuyangzhou.github.io.git # 刚刚创建的仓库地址
```

#### 本地预览

```bash
hexo server
hexo s # 简写
```

打开localhost:4000 即可看到我们的博客界面了

#### 发布

```bash
hexo clean
hexo generate # hexo g 简写
hexo deploy # hexo d 简写
# hexo d -g  === hexo g & hexo d
```

这里如果出错，找不到repository，需要安装hexo-deployer-git插件，再执行hexo d命令

``` bash
npm install hexo-deployer-git --save
```

打开xuyangzhou.github.io（就是你创建的仓库名）即可看到

至此，博客已经搭建工作已大功告成了，我们已经拥有了自己的一个小窝了，但是我们还要发表博文，修改页面样式，添加一些诸如搜索、评论、浏览统计等等功能，预知后事如何，且听下文分解！

### 写博客

#### 新建博文

```bash
hexo new '文件名' # 会在source/_posts创建一个文件名.md文件
```

#### 编辑博文

```markdown
---
title: new // 博客标题
date: 2017-04-04 04:04:06 // 创建时间
tags: hexo // 标签
categories: hexo // 分类
---
　　这里写正文（上面的---是必要的）
```

#### 插入图片

##### 外网图片

```markdown
![图片描述](http://www.baidu.com/1.jpg)
```

##### 本地图片

###### 使用hexo-asset-image插件

```bash
npm install hexo-asset-image --save
```

修改xuyz-blog/_config.yml中的post_asset_folder为true

```yml
post_asset_folder: false
```

之后再使用 hexo new 'new' 创建新博文的时候，会在source/_posts里面创建.md文件的同时生成一个相同的名字的文件夹，把该文章中需要使用的图片放在该文件夹下即可

```markdown
![图片描述](new/new.jpg)
```

###### 在source下新建images文件夹（推荐）

使用hexo-asset-image个人认为不利用图片复用，直接根目录的source下新建存放本地图片的文件夹简便有效

```markdown
![图片描述(可以不写)](/images/new.jpg)
```

### 更换主题

主题这东西嘛，萝卜青菜，各有所爱，没有最好，只有合适，具体请移步[hexo主题](https://hexo.io/themes/)选取一款适合自己的吧！

以next为例，下载对应主题，修改_config.yml中的theme为next即可！

```bash
cd xuyz-blog文件下
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

更多关于next主题下的配置问题请移步[next使用文档](http://theme-next.iissnan.com/)