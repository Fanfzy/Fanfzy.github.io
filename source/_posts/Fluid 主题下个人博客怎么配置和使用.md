---
title: Fluid主题下个人博客的配置与使用
date: 2026-05-24 16:30:00
tags:
  - Hexo
  - Fluid
  - 个人博客
categories:
  - 个人博客
index_img: /img/post/default.png # 设置首页文章卡片封面图，对应 source/img/post/default.jpg
banner_img: /img/bg/bg.png # 设置文章详情页顶部大图，对应 source/img/bg/home.jpg
---

# Fluid主题下个人博客的配置与使用

在完成 Hexo 本地部署，并且已经安装和启用 Fluid 主题之后，我开始对自己的个人博客进行进一步配置。

这篇文章主要记录的是：在 Fluid 主题已经可以正常显示的基础上，如何继续配置个人博客，包括博客基本信息、首页导航栏、分类与标签、博客图片、文章封面、关于页面中的个人信息，以及以后每次新建文章时应该遵循的流程。

这篇文章不是从零搭建教程，而是我在博客已经能通过 `http://localhost:4000` 正常访问之后，对博客进行个性化配置和日常使用的记录。

---

## 一、配置文件的基本分工

使用 Fluid 主题之后，博客中最重要的配置文件主要有两个。

```text
_config.yml
_config.fluid.yml
```

它们的作用不一样。

| 文件 | 作用 |
|---|---|
| `_config.yml` | Hexo 博客的总配置文件 |
| `_config.fluid.yml` | Fluid 主题的专用配置文件 |

一般来说，博客的标题、作者、语言、时区、主题名称这些内容放在 `_config.yml` 中。

而 Fluid 主题的首页样式、导航栏、关于页、代码块、图片、社交链接等内容，主要在 `_config.fluid.yml` 中修改。

我现在的理解是：

```text
_config.yml 管整个博客的基础信息。
_config.fluid.yml 管 Fluid 主题的页面显示效果。
```

所以，在后续配置时，我不会直接修改 `node_modules` 里的主题源文件，而是优先修改博客根目录下的 `_config.fluid.yml`。

---

## 二、配置博客的基础信息

博客的基础信息在根目录的 `_config.yml` 中修改。

例如：

```yaml
title: Fan Zhaoyu的个人博客 # 设置博客标题，通常显示在网页标题或主题首页中
subtitle: 学习、编程与生活记录 # 设置博客副标题，用来概括博客内容
description: 记录学习、编程、课程作业和个人项目 # 设置博客描述，说明这个博客主要写什么
keywords: 博客,学习,编程,Hexo,Fluid # 设置博客关键词，多个关键词之间用英文逗号隔开
author: Fan Zhaoyu # 设置博客作者名称
language: zh-CN # 设置博客语言为简体中文
timezone: Asia/Shanghai # 设置博客时间为北京时间
theme: fluid # 设置当前博客使用 Fluid 主题
```

这几项中，我认为最重要的是：

```yaml
language: zh-CN # 设置博客界面为简体中文
timezone: Asia/Shanghai # 设置博客文章时间为北京时间
theme: fluid # 指定当前使用 Fluid 主题
```

如果 `theme` 没有写成 `fluid`，那么 Hexo 就不会使用 Fluid 主题。

修改 `_config.yml` 之后，需要重新生成博客页面：

```bash
hexo clean # 清理旧缓存，避免旧配置继续影响网页显示
hexo g # 重新生成博客静态网页
hexo s # 启动本地预览服务器
```

然后打开：

```text
http://localhost:4000
```

查看修改是否生效。

---

## 三、配置博客顶部导航栏

Fluid 主题的顶部导航栏主要在 `_config.fluid.yml` 中配置。

打开：

```text
_config.fluid.yml
```

搜索：

```text
navbar:
```

可以把导航栏配置成下面这样：

```yaml
navbar: # 配置网页顶部导航栏
  blog_title: Fan Zhaoyu的个人博客 # 设置导航栏左侧显示的博客名称
  menu: # 配置导航栏右侧的菜单
    - { key: 'home', link: '/', icon: 'iconfont icon-home-fill' } # 设置首页入口
    - { key: 'archive', link: '/archives/', icon: 'iconfont icon-archive-fill' } # 设置归档页面入口
    - { key: 'category', link: '/categories/', icon: 'iconfont icon-category-fill' } # 设置分类页面入口
    - { key: 'tag', link: '/tags/', icon: 'iconfont icon-tags-fill' } # 设置标签页面入口
    - { key: 'about', link: '/about/', icon: 'iconfont icon-user-fill' } # 设置关于页面入口
```

这里的导航栏主要包括五个入口：

| 导航项 | 作用 |
|---|---|
| 首页 | 进入博客主页 |
| 归档 | 按时间顺序查看全部文章 |
| 分类 | 按文章分类查看内容 |
| 标签 | 按文章标签查看内容 |
| 关于 | 查看个人介绍和联系方式 |

如果导航栏中有分类、标签、关于这些页面，就需要保证对应页面已经存在。

---

## 四、配置分类页、标签页和关于页

Fluid 主题中的分类页、标签页和关于页，都需要在 `source` 文件夹下有对应页面。

这几个页面通常放在：

```text
source/categories/index.md
source/tags/index.md
source/about/index.md
```

如果还没有这些页面，可以在博客根目录下执行：

```bash
hexo new page categories # 新建分类页面
hexo new page tags # 新建标签页面
hexo new page about # 新建关于页面
```

创建完成后，需要分别修改它们的 Front-matter。

分类页：

```markdown
---
title: 分类
layout: category
---
```

标签页：

```markdown
---
title: 标签
layout: tag
---
```

关于页：

```markdown
---
title: 关于
layout: about
---

这里是我的个人博客。

我会在这里记录学习、编程、课程作业、个人项目和博客搭建过程。
```

其中最重要的是 `layout`。

| 页面 | 需要的 layout |
|---|---|
| 分类页 | `layout: category` |
| 标签页 | `layout: tag` |
| 关于页 | `layout: about` |

如果 `layout` 写错，页面可能会生成，但是 Fluid 的主题样式不会正常显示。

---

## 五、配置首页背景图和首页文字

Fluid 主题首页上方一般会显示一张大图，也可以显示一句博客标语。

我把博客图片统一放在：

```text
source/img
```

常用图片目录可以这样安排：

```text
source/img/bg
source/img/post
source/img/avatar
```

它们分别表示：

| 文件夹 | 用途 |
|---|---|
| `source/img/bg` | 放首页背景图、文章顶部大图 |
| `source/img/post` | 放文章封面图 |
| `source/img/avatar` | 放头像、微信二维码等个人信息图片 |

例如，我可以放这几张图片：

```text
source/img/bg/home.jpg
source/img/post/default.jpg
source/img/avatar/avatar.png
source/img/avatar/wechat.png
```

在 Hexo 中，`source` 文件夹会被当成网站根目录。

所以图片路径应该这样写：

```text
/img/bg/home.jpg
/img/post/default.jpg
/img/avatar/avatar.png
/img/avatar/wechat.png
```

而不是写成：

```text
/source/img/bg/home.jpg
```

首页背景图可以在 `_config.fluid.yml` 中配置。

搜索：

```text
index:
```

然后修改：

```yaml
index: # 配置博客首页
  banner_img: /img/bg/bg.png # 设置首页顶部背景图
  banner_img_height: 70 # 设置首页背景图高度
  banner_mask_alpha: 0.3 # 设置首页背景图遮罩透明度
  slogan: # 配置首页标语
    enable: true # 开启首页标语
    text: "记录学习，记录成长" # 设置首页显示的文字
```

其中：

| 配置项 | 作用 |
|---|---|
| `banner_img` | 首页顶部背景图 |
| `banner_img_height` | 首页顶部图片高度 |
| `banner_mask_alpha` | 图片遮罩透明度 |
| `slogan.text` | 首页显示的文字 |

如果首页图片不显示，优先检查图片路径是否正确。

---

## 六、配置文章封面图和文章顶部图

在 Fluid 主题中，每篇文章可以单独设置封面图。

文章开头的 Front-matter 可以这样写：

```markdown
---
title: 示例文章
date: 2026-05-24 16:30:00
tags:
  - Hexo
  - Fluid
categories:
  - 个人博客
index_img: /img/post/default.jpg
banner_img: /img/bg/home.jpg
---
```

其中：

| 配置项 | 作用 |
|---|---|
| `index_img` | 文章在首页卡片中显示的封面图 |
| `banner_img` | 点进文章后顶部显示的大图 |

我现在一般把文章封面图放在：

```text
source/img/post
```

例如：

```text
source/img/post/default.jpg
```

然后在文章中写：

```markdown
index_img: /img/post/default.jpg
banner_img: /img/bg/home.jpg
```

这里要注意，图片路径前面有 `/`，表示从网站根目录开始查找。

---

## 七、配置关于页中的头像和个人信息

关于页的正文在：

```text
source/about/index.md
```

但是关于页中的头像、名字、简介、GitHub、微信等信息，主要在 `_config.fluid.yml` 中设置。

打开：

```text
_config.fluid.yml
```

搜索：

```text
about:
```

可以配置成：

```yaml
about: # 配置关于页面
  avatar: /img/avatar/avatar.png # 设置关于页头像图片
  name: Fan Zhaoyu # 设置关于页显示的名字
  intro: "本科自动化学生，记录学习、编程和个人博客搭建过程。" # 设置关于页个人简介
  icons: # 设置关于页下方的社交链接
    - { class: 'iconfont icon-github-fill', link: 'https://github.com/你的GitHub用户名', tip: 'GitHub' } # 设置 GitHub 链接
    - { class: 'iconfont icon-wechat-fill', qrcode: '/img/avatar/wechat.png', tip: '微信' } # 设置微信二维码
```

这里的微信二维码图片需要提前放在：

```text
source/img/avatar/wechat.png
```

头像图片需要提前放在：

```text
source/img/avatar/avatar.png
```

如果暂时不想显示微信，也可以只保留 GitHub：

```yaml
about: # 配置关于页面
  avatar: /img/avatar/avatar.png # 设置关于页头像
  name: Fan Zhaoyu # 设置关于页名字
  intro: "记录学习、编程和个人成长。" # 设置个人简介
  icons: # 设置社交链接
    - { class: 'iconfont icon-github-fill', link: 'https://github.com/你的GitHub用户名', tip: 'GitHub' } # 设置 GitHub 链接
```

这里要注意，GitHub 链接需要替换成自己的真实 GitHub 地址。

---

## 八、配置文章分类和标签

每篇文章的分类和标签都写在文章开头的 Front-matter 中。

例如：

```markdown
---
title: Fluid主题下个人博客的配置与使用
date: 2026-05-24 16:30:00
tags:
  - Hexo
  - Fluid
  - 个人博客
categories:
  - 个人博客
index_img: /img/post/default.jpg
banner_img: /img/bg/home.jpg
---
```

这里的：

```markdown
tags:
  - Hexo
  - Fluid
  - 个人博客
```

表示这篇文章有三个标签。

这里的：

```markdown
categories:
  - 个人博客
```

表示这篇文章属于“个人博客”分类。

如果分类页里没有出现“个人博客”，通常有几个原因：

1. 文章的 Front-matter 没有被正确识别。
2. `categories` 拼写错误。
3. 分类页面的 `layout: category` 没有设置。
4. 修改之后没有重新执行 `hexo clean` 和 `hexo g`。

Front-matter 的开头和结尾必须是单独一行的 `---`。

正确写法：

```markdown
---
title: 示例文章
date: 2026-05-24 16:30:00
categories:
  - 个人博客
---
```

错误写法：

```markdown
--- title: 示例文章
date: 2026-05-24 16:30:00
categories:
  - 个人博客
---
```

如果写错，Hexo 可能会把 `title`、`date`、`tags`、`categories` 都当成正文显示出来。

---

## 九、在博客文章中插入图片

如果我想在文章正文中插入图片，可以先把图片放到：

```text
source/img/post
```

例如：

```text
source/img/post/blog-example.jpg
```

然后在文章中写：

```markdown
![博客示例图片](/img/post/blog-example.jpg)
```

这里的格式是：

```markdown
![图片说明文字](图片路径)
```

其中：

| 部分 | 作用 |
|---|---|
| `图片说明文字` | 图片加载失败时显示的文字 |
| `图片路径` | 图片在博客中的访问地址 |

如果图片不显示，我会优先检查：

1. 图片是否真的放在了 `source/img/post` 里。
2. 图片路径是否写成了 `/img/post/图片名`。
3. 文件名大小写是否一致。
4. 修改后是否重新执行了 `hexo clean` 和 `hexo g`。

---

## 十、每次新建博客文章的流程

以后每次写新博客，我可以按照固定流程进行。

第一步，进入博客根目录：

```bash
cd ~/Desktop/myBlog # 进入桌面上的博客项目根目录
```

第二步，新建文章：

```bash
hexo new post "文章标题" # 新建一篇博客文章
```

第三步，打开生成的文章文件：

```text
source/_posts/文章标题.md
```

第四步，修改文章开头的 Front-matter：

```markdown
---
title: 文章标题
date: 2026-05-24 16:30:00
tags:
  - 学习
categories:
  - 个人博客
index_img: /img/post/default.jpg
banner_img: /img/bg/home.jpg
---
```

第五步，在 Front-matter 下面写正文。

第六步，重新生成并启动本地预览：

```bash
hexo clean # 清理旧缓存
hexo g # 重新生成博客静态网页
hexo s # 启动本地预览服务器
```

第七步，打开浏览器查看：

```text
http://localhost:4000
```

如果页面正常显示，说明这篇文章已经成功加入博客。

---

## 十一、每次修改配置后的检查流程

只要修改了下面这些文件，就建议重新生成：

```text
_config.yml
_config.fluid.yml
source/about/index.md
source/categories/index.md
source/tags/index.md
source/_posts/文章.md
```

重新生成的命令是：

```bash
hexo clean # 清理旧缓存，避免旧配置继续影响页面
hexo g # 根据最新配置生成静态网页
hexo s # 启动本地预览服务器
```

如果浏览器中还是没有变化，可以按：

```text
Ctrl + F5
```

强制刷新网页缓存。

---

## 十二、我目前使用 Fluid 主题的目录结构

经过这些配置后，我的博客目录大致是这样的：

```text
myBlog
├─ _config.yml
├─ _config.fluid.yml
├─ source
│  ├─ _posts
│  │  └─ Fluid主题下个人博客的配置与使用.md
│  ├─ about
│  │  └─ index.md
│  ├─ categories
│  │  └─ index.md
│  ├─ tags
│  │  └─ index.md
│  └─ img
│     ├─ avatar
│     │  ├─ avatar.png
│     │  └─ wechat.png
│     ├─ bg
│     │  └─ home.jpg
│     └─ post
│        └─ default.jpg
```

其中我最常修改的是：

| 文件或文件夹 | 作用 |
|---|---|
| `_config.yml` | 修改博客基础信息 |
| `_config.fluid.yml` | 修改 Fluid 主题显示效果 |
| `source/_posts` | 存放博客文章 |
| `source/about/index.md` | 修改关于页正文 |
| `source/img` | 存放博客图片 |

---

## 十三、总结

在 Fluid 主题已经安装并启用之后，个人博客的配置重点不在于重新搭建环境，而是在于理解各个文件的作用。

我目前认为最重要的是：

1. `_config.yml` 用来配置博客基础信息。
2. `_config.fluid.yml` 用来配置 Fluid 主题效果。
3. `source/_posts` 用来存放博客文章。
4. `source/img` 用来存放博客图片。
5. 每篇文章通过 Front-matter 设置标题、时间、分类、标签和封面。
6. 修改配置后要执行 `hexo clean`、`hexo g`、`hexo s` 重新预览。

以后我写博客时，主要流程就是：

```bash
cd ~/Desktop/myBlog # 进入博客根目录
hexo new post "文章标题" # 新建文章
hexo clean # 清理旧缓存
hexo g # 重新生成静态网页
hexo s # 启动本地预览服务器
```

本地确认没有问题之后，再考虑把博客推送到 GitHub，实现在线访问。