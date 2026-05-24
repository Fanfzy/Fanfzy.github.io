---
title: 使用hexo以及fluid主题部署博客（初始创建）  
date: 2026-05-24 11:53:06 
tags:
  - Hexo 
  - Fluid
  - 个人博客部署 
categories: 
  - 个人博客 
index_img: /img/post/default.png # 设置首页文章卡片封面图，对应 source/img/post/default.jpg
banner_img: /img/bg/bg.png # 设置文章详情页顶部大图，对应 source/img/bg/home.jpg         
---

# 我使用hexo以及github部署博客，博客的部署过程如下：
1. 首先，我使用的windows11系统，所需的软件有Git、Node.js、VS Code
2. 下载所有软件之后，在桌面打开bash终端，用bash终端或者直接手动新建一个博客文件夹，文件夹名为myBlog
3. 进入myBlog文件夹，初始化hexo博客，输入以下命令：
```bash  
npm install hexo-cli -g # 全局安装 hexo-cli 工具
hexo init . # 初始化博客
```
4. 安装依赖，输入以下命令：
```bash  
npm install # 安装博客依赖
```
5. 安装主题，输入以下命令：
```bash  
npm install fluid-theme-hexo # 安装 fluid 主题
```
6. 可以直接用vscode打开博客文件夹，在文件夹中打开hexo.config.js文件，配置主题为 fluid
        ```bash  
        cd ~/Desktop/myBlog     # 确保现在位于博客根目录
        code .      # 用 VS Code 打开当前博客项目文件夹
        hexo new post "文章名"      # 新建一篇文章，文章名叫 第一篇文章
        hexo clean      # 清理 Hexo 旧缓存，避免旧时间配置继续生效
        hexo g      # 重新生成博客静态网页
        hexo s      # 启动本地预览服务器
        ```
今天我完成我的第一个博客的本地部署。
开始学习博客的编写，个人博客用于作为个人知识库，记录专业学习、个人项目、生活记录。
