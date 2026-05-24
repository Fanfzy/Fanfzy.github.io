---
title: Hexo博客部署到GitHub Pages记录
date: 2026-05-24 18:00:00
tags:
  - Hexo
  - GitHub Pages
  - Fluid
categories:
  - 个人博客
index_img: /img/post/default.png
banner_img: /img/bg/bg.png
---

# Hexo博客部署到GitHub Pages记录

在本地完成 Hexo 博客和 Fluid 主题配置之后，我希望把博客部署到 GitHub Pages 上，让别人也可以通过网址访问我的博客。

这篇文章记录的是：如何把本地的 Hexo 博客项目上传到 GitHub，并通过 GitHub Pages 发布成一个真正可以访问的网站。

我的本地博客目录是：

```text
~/Desktop/myBlog
```

最终希望别人可以通过下面这种地址访问：

```text
https://你的GitHub用户名.github.io
```

---

## 一、部署思路

我这次采用的部署方式是：

```text
本地 Hexo 源码
→ 推送到 GitHub 仓库
→ GitHub Actions 自动生成静态网页
→ GitHub Pages 发布网站
```

也就是说，我本地只负责写文章、改配置、提交代码。

真正生成网页和发布网站的过程，交给 GitHub Actions 自动完成。

这样做的好处是：

1. 不需要自己手动上传 `public` 文件夹。
2. 不需要每次执行 `hexo d`。
3. 博客源码和部署过程都保存在 GitHub 上。
4. 后续更新文章时，只需要 `git push` 即可自动部署。

---

## 二、在 GitHub 上创建博客仓库

首先登录 GitHub，然后新建一个仓库。

仓库名必须写成：

```text
你的GitHub用户名.github.io
```

比如我的 GitHub 用户名如果是：

```text
fan123
```

那么仓库名就应该是：

```text
fan123.github.io
```

创建仓库时，我的设置是：

| 配置项 | 选择 |
|---|---|
| Repository name | `你的GitHub用户名.github.io` |
| Public / Private | Public |
| Add a README file | 不勾选 |
| Add .gitignore | 不选择 |
| Choose a license | 不选择 |

这里我没有勾选 README，是为了让远程仓库保持空仓库，方便后面直接把本地博客源码推送上去。

---

## 三、修改 Hexo 的网站地址

部署到 GitHub Pages 之前，需要修改 Hexo 的主配置文件。

打开博客根目录下的：

```text
_config.yml
```

找到或者手动添加下面两项：

```yaml
url: https://你的GitHub用户名.github.io # 设置博客部署后的线上访问地址
root: / # 设置网站根路径，用户名.github.io 这种仓库固定写 /
```

例如 GitHub 用户名是 `fan123`，就写成：

```yaml
url: https://fan123.github.io # 设置博客部署后的线上访问地址
root: / # 设置网站根路径，用户主页仓库固定写 /
```

这里要注意：

如果仓库名是：

```text
你的GitHub用户名.github.io
```

那么：

```yaml
root: / # 用户主页仓库使用根路径
```

不要写成：

```yaml
root: /你的GitHub用户名.github.io/ # 错误写法，用户主页仓库不需要写仓库名
```

---

## 四、创建 .gitignore 文件

在博客根目录下创建一个文件：

```text
.gitignore
```

这个文件的作用是告诉 Git 哪些文件不要上传到 GitHub。

我的 `.gitignore` 内容如下：

```gitignore
node_modules/ # 忽略 Node.js 依赖文件夹，因为它很大，GitHub Actions 会重新安装依赖
public/ # 忽略 Hexo 本地生成的网页文件，因为线上部署时会自动重新生成
.deploy_git/ # 忽略 hexo deploy 方式生成的部署目录，这次不用这种方式
db.json # 忽略 Hexo 本地缓存数据库
*.log # 忽略日志文件
.DS_Store # 忽略 macOS 系统隐藏文件
Thumbs.db # 忽略 Windows 系统缩略图缓存文件
```

其中最重要的是：

```gitignore
node_modules/ # 忽略依赖文件夹
public/ # 忽略本地生成的网站文件
```

因为这两个文件夹没有必要上传到 GitHub。

---

## 五、创建 GitHub Actions 自动部署文件

为了让 GitHub 自动帮我生成并发布博客，需要创建 GitHub Actions 配置文件。

在博客根目录下创建文件夹：

```bash
mkdir -p .github/workflows # 创建 GitHub Actions 工作流配置目录
```

然后创建文件：

```text
.github/workflows/pages.yml
```

文件内容如下：

```yaml
name: Deploy Hexo to GitHub Pages # 设置 GitHub Actions 工作流名称

on: # 设置触发条件
  push: # 当代码推送到 GitHub 时触发
    branches: # 指定监听的分支
      - main # 只监听 main 分支
  workflow_dispatch: # 允许在 GitHub 页面手动运行工作流

permissions: # 设置工作流权限
  contents: read # 允许读取仓库代码
  pages: write # 允许写入 GitHub Pages 发布内容
  id-token: write # 允许获取部署所需的身份令牌

concurrency: # 设置并发控制
  group: pages # 把 GitHub Pages 部署任务放到同一个组
  cancel-in-progress: false # 不取消正在运行的部署任务

jobs: # 定义工作流任务
  build: # 定义构建任务
    runs-on: ubuntu-latest # 使用 GitHub 提供的 Ubuntu 运行环境

    steps: # 定义构建任务步骤
      - name: Checkout repository # 步骤名称：拉取仓库代码
        uses: actions/checkout@v4 # 使用 GitHub 官方 checkout 动作

      - name: Setup Node.js # 步骤名称：配置 Node.js 环境
        uses: actions/setup-node@v4 # 使用 GitHub 官方 Node.js 动作
        with: # 设置 Node.js 参数
          node-version: 22 # 使用 Node.js 22 版本
          cache: npm # 开启 npm 缓存，提高后续构建速度

      - name: Setup Pages # 步骤名称：配置 GitHub Pages 环境
        uses: actions/configure-pages@v5 # 使用 GitHub 官方 Pages 配置动作

      - name: Install dependencies # 步骤名称：安装项目依赖
        run: npm ci # 根据 package-lock.json 精确安装依赖

      - name: Build Hexo site # 步骤名称：生成 Hexo 静态网页
        run: npx hexo generate # 使用 Hexo 生成 public 文件夹

      - name: Upload Pages artifact # 步骤名称：上传生成好的网页文件
        uses: actions/upload-pages-artifact@v3 # 使用 GitHub 官方 Pages 上传动作
        with: # 设置上传参数
          path: ./public # 指定上传 Hexo 生成的 public 文件夹

  deploy: # 定义部署任务
    needs: build # 部署任务必须等待 build 构建任务完成
    runs-on: ubuntu-latest # 使用 GitHub 提供的 Ubuntu 运行环境

    environment: # 设置部署环境
      name: github-pages # 指定部署环境名称为 GitHub Pages
      url: ${{ steps.deployment.outputs.page_url }} # 部署完成后输出网站访问地址

    steps: # 定义部署步骤
      - name: Deploy to GitHub Pages # 步骤名称：部署到 GitHub Pages
        id: deployment # 设置步骤 ID，方便读取部署结果
        uses: actions/deploy-pages@v4 # 使用 GitHub 官方 Pages 部署动作
```

这个文件的作用是：

1. GitHub 收到我推送的代码。
2. 自动安装 Node.js 环境。
3. 自动安装 Hexo 项目依赖。
4. 自动执行 `npx hexo generate`。
5. 自动把生成的 `public` 文件夹发布到 GitHub Pages。

---

## 六、本地测试一次博客是否正常

在正式推送到 GitHub 之前，我先在本地测试一次。

在博客根目录执行：

```bash
cd ~/Desktop/myBlog # 进入桌面上的博客根目录
hexo clean # 清理 Hexo 旧缓存和旧生成文件
hexo g # 重新生成博客静态网页
hexo s # 启动本地预览服务器
```

然后打开浏览器访问：

```text
http://localhost:4000
```

如果本地页面能正常打开，说明当前博客配置基本没有问题。

测试完成后，在终端中按：

```text
Ctrl + C
```

停止本地服务器。

---

## 七、初始化 Git 仓库

接下来，需要把本地博客文件夹变成一个 Git 仓库。

在博客根目录下执行：

```bash
cd ~/Desktop/myBlog # 进入桌面上的博客根目录
git init # 初始化 Git 仓库，让当前文件夹开始被 Git 管理
git branch -M main # 把当前分支名称改为 main
git status # 查看当前有哪些文件等待提交
git add . # 添加当前目录下所有需要提交的文件
git commit -m "init hexo fluid blog" # 创建第一次提交，记录当前博客源码
```

如果第一次提交时提示没有配置用户名和邮箱，就执行：

```bash
git config --global user.name "你的GitHub用户名" # 设置 Git 提交用户名
git config --global user.email "你的GitHub邮箱" # 设置 Git 提交邮箱
git commit -m "init hexo fluid blog" # 重新创建第一次提交
```

这里的用户名和邮箱主要用于记录是谁提交了代码。

---

## 八、连接 GitHub 远程仓库

本地提交完成后，需要把本地仓库和 GitHub 仓库连接起来。

GitHub 仓库地址一般是：

```text
https://github.com/你的GitHub用户名/你的GitHub用户名.github.io.git
```

在本地执行：

```bash
git remote add origin https://github.com/你的GitHub用户名/你的GitHub用户名.github.io.git # 添加 GitHub 远程仓库地址
git remote -v # 查看远程仓库地址是否添加成功
git push -u origin main # 把本地 main 分支推送到 GitHub，并建立默认关联
```

如果出现：

```text
remote origin already exists
```

说明之前已经添加过远程仓库地址。

可以重新设置：

```bash
git remote remove origin # 删除旧的远程仓库地址
git remote add origin https://github.com/你的GitHub用户名/你的GitHub用户名.github.io.git # 添加新的 GitHub 远程仓库地址
git push -u origin main # 重新推送本地 main 分支到 GitHub
```

第一次推送时，Git 可能会弹出 GitHub 登录窗口，按照提示登录即可。

---

## 九、设置 GitHub Pages 发布方式

代码推送到 GitHub 后，进入自己的 GitHub 仓库页面。

点击：

```text
Settings
```

然后找到：

```text
Pages
```

在 `Build and deployment` 里，把 `Source` 设置为：

```text
GitHub Actions
```

这样 GitHub Pages 就会使用刚才创建的 `pages.yml` 自动部署博客。

---

## 十、查看 GitHub Actions 部署结果

设置完成后，回到仓库页面，点击：

```text
Actions
```

应该可以看到一个工作流：

```text
Deploy Hexo to GitHub Pages
```

如果它前面是黄色圆点，说明正在运行。

如果变成绿色对号，说明部署成功。

如果变成红色叉号，说明部署失败，需要点进去查看报错信息。

部署成功后，就可以访问：

```text
https://你的GitHub用户名.github.io
```

第一次部署完成后，网站可能需要等待几十秒到几分钟才能正常访问。

---

## 十一、以后更新博客的流程

以后每次写新文章，或者修改 Fluid 主题配置后，我的更新流程是：

```bash
cd ~/Desktop/myBlog # 进入桌面上的博客根目录
hexo clean # 清理旧缓存
hexo g # 本地重新生成博客网页，检查有没有生成错误
hexo s # 启动本地预览服务器，确认页面效果正常
```

本地确认没有问题后，按：

```text
Ctrl + C
```

停止本地服务器。

然后执行：

```bash
git status # 查看本地有哪些文件发生了变化
git add . # 添加全部修改到暂存区
git commit -m "update blog" # 提交本次博客修改
git push # 推送到 GitHub，并自动触发 GitHub Actions 部署
```

也就是说，以后更新博客的核心流程是：

```text
写文章
→ 本地预览
→ git add .
→ git commit
→ git push
→ GitHub 自动部署
```

---

## 十二、常见问题记录

### 1. 访问网站出现 404

如果访问：

```text
https://你的GitHub用户名.github.io
```

出现 404，可以检查：

1. GitHub 仓库名是否是 `你的GitHub用户名.github.io`。
2. GitHub Pages 的 Source 是否选择了 `GitHub Actions`。
3. Actions 里的部署任务是否是绿色对号。
4. `_config.yml` 里的 `url` 和 `root` 是否正确。

正确配置应该类似：

```yaml
url: https://你的GitHub用户名.github.io # 设置博客线上访问地址
root: / # 用户主页仓库使用根路径
```

---

### 2. 页面文字能显示，但是样式丢失

如果网站能打开，但是页面样式不正常，通常是路径配置问题。

检查 `_config.yml`：

```yaml
url: https://你的GitHub用户名.github.io # 设置正确的 GitHub Pages 地址
root: / # 用户主页仓库必须写 /
```

修改后重新推送：

```bash
git add . # 添加配置修改
git commit -m "fix site url" # 提交网站地址修复
git push # 推送到 GitHub 重新部署
```

---

### 3. GitHub Actions 中 npm ci 报错

如果 GitHub Actions 报错位置在：

```text
npm ci
```

可能是本地没有正确生成 `package-lock.json`。

可以在本地执行：

```bash
cd ~/Desktop/myBlog # 进入博客根目录
npm install # 重新安装依赖并生成 package-lock.json
git add package.json package-lock.json # 添加依赖配置文件
git commit -m "fix npm dependencies" # 提交依赖文件修改
git push # 推送到 GitHub 重新部署
```

---

### 4. 线上图片不显示

如果本地图片能显示，但 GitHub Pages 上不显示，需要检查图片是否放在 `source` 文件夹里。

正确位置示例：

```text
source/img/bg/home.jpg
source/img/post/default.jpg
source/img/avatar/avatar.png
```

文章或配置中应该写成：

```text
/img/bg/home.jpg
/img/post/default.jpg
/img/avatar/avatar.png
```

不要写成：

```text
/source/img/bg/home.jpg
```

因为 `source` 文件夹会被当成网站根目录。

---

## 十三、总结

这次部署完成后，我的博客就不再只是本地页面，而是可以通过 GitHub Pages 在线访问。

整个部署过程的核心是：

1. 在 GitHub 创建 `用户名.github.io` 仓库。
2. 修改 Hexo 的 `url` 和 `root`。
3. 创建 `.gitignore`，避免上传无用文件。
4. 创建 GitHub Actions 配置文件。
5. 使用 Git 把本地博客源码推送到 GitHub。
6. 在 GitHub Pages 中选择 GitHub Actions 作为发布方式。
7. 每次更新博客时，通过 `git push` 自动触发部署。

以后我的日常更新命令就是：

```bash
cd ~/Desktop/myBlog # 进入博客根目录
hexo clean # 清理旧缓存
hexo g # 本地生成检查
hexo s # 本地预览检查
git status # 查看修改内容
git add . # 添加所有修改
git commit -m "update blog" # 提交本次修改
git push # 推送到 GitHub 并触发自动部署
```

这样，别人就可以通过：

```text
https://你的GitHub用户名.github.io
```

访问我的个人博客。