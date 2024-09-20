## 1# Docsify 构建

### 1.1# 前置条件

1. IDE：VSCode
2. Markdown 编辑：Typora
3. node.js 版本：v20.16.0

### 1.2# 三句话成功搭建博客系统

1. 安装 Docsify：`npm i docsify-cli -g`
2. 创建目录并初始化：`docsify init my_docsify`
3. 启动服务：`docsify serve my_docsify`

访问`http://localhost:3000/`，成功！

### 1.3# 系统文件

| 文件名         | 描述                                                            |
| -------------- | --------------------------------------------------------------- |
| index.html     | 主要 HTML 文件，加载和显示整个文档网站。                        |
| \_coverpage.md | 定义封面页，包括标题、描述和封面图片或链接。                    |
| \_navbar.md    | 配置导航栏，定义导航链接和下拉菜单。                            |
| \_sidebar.md   | 定义侧边栏菜单，包含指向不同页面或部分的链接。                  |
| README.md      | 默认主页内容，Docsify 将其渲染为网站首页。                      |
| .nojekyll      | 禁用 GitHub Pages 上的 Jekyll 处理，确保正确提供 Docsify 网站。 |

大概了解一下就可以，具体内容可以直接克隆这个仓库。TODO

### 1.4# 博客内容

可以自定义目录结构，只需要将其配置到`\_sidebar.md`即可。

## 2# Docsify 插件

TODO

## 3# Docsify 部署（GitHub）

### 3.1# 注册 GitHub 账号

GitHub 是全球最大的共享平台，使用 GitHub Pages 部署是免费的且便捷的选择。使用邮箱注册，并记住密码（这很重要）。

### 3.2# 部署方针

- 创建两个仓库：

  - **私有仓库**：用于上传源码。

  - **公开仓库（GitHub Pages）**：用于部署静态页面。

- 创建自动化任务，每次私有仓库更新时，自动将编译结果部署到 GitHub Pages。

### 3.3# 创建第一个仓库（my_docsify）

- 访问 `https://github.com/new`，创建名为 `my_docsify` 的私有仓库。必要时克隆到本地。

- 如果本地已经存在 `my_docsify`，则只需绑定远程仓库地址：

  ```bash
  git remote add origin https://github.com/你的账号/my_docsify.git
  ```

  如果本地仓库已经绑定了其他仓库，则需要执行：

  ```bash
  git remote set-url origin https://github.com/你的账号/my_docsify.git
  ```

- 在本地提交 Git 记录并 Push 到远程库，完成第一个仓库的配置。

### 3.4# 创建第二个仓库（你的账号.github.io）

- 访问 `https://github.com/new`，创建名为 `你的账号.github.io` 的公开仓库，用于托管 GitHub Pages。

### 3.5# 创建自动化脚本

在 `.github/workflows/deploy.yml` 中设置部署流程：

```json
name: Deploy Docsify

on:
  push:
    branches:
      - main # Or your specific branch

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout source
        uses: actions/checkout@v2

      - name: Set up Node.js
        uses: actions/setup-node@v2
        with:
          node-version: "20.16.0"

      - name: Install Docsify CLI
        run: npm install -g docsify-cli

      - name: Initialize or Update Docsify
        run: |
          if [ ! -d "docs" ]; then
            mkdir -p docs
            docsify init docs
          fi

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.DEPLOY_KEY_PRIVATE }}
          external_repository: 你的账号/你的账号.github.io # 需要修改
          publish_branch: docsify_page # Destination branch for the docs
          publish_dir: .
          force_orphan: true

```

保存并推送到远程，GitHub 会自动启动工作流，通常在 1-2 分钟内完成，但可能会失败。

### 3.6# 添加秘钥

- 本地打开 cmd，执行 `ssh-keygen -t rsa`，生成两个文件，目录为 `C:\Users\你的账号\.ssh`。其中 `id_rsa` 是私钥，第一个仓库保留，`id_rsa.pub` 是公钥，第二个仓库保留。
- 在第一个仓库中，添加私钥到 `Settings > Secrets and variables > Actions`。
- 在第二个仓库中，添加公钥到 `Settings > Deploy keys`。

### 3.7# 写权限

打开第一个和第二个仓库，点击 `Settings > Actions > General`，设置 `Workflow permissions` 为 `Read and write permissions`。

保存后，再次上传代码，或重启失败的 Actions 任务。1-2 分钟后查看第二个仓库的 `docsify_page` 分支。

### 3.8# 配置 GitHub Page

- 打开第二个仓库，点击 `Settings > Pages`，进行以下设置：
  - **Source**：选择 `Deploy from a branch`
  - **Branch**：选择 `docsify_page`

保存后，访问 `https://你的账号.github.io/`，如果一切顺利，部署就完成了。 后续只需更新私有库即可自动更新 GitHub Pages。

2024 年 9 月 17 日

## 4# 参考链接

[docsify-themeable - Options (jhildenbiddle.github.io)](https://jhildenbiddle.github.io/docsify-themeable/#/options)
