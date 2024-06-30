---
title: 使用GitHub Pages 部署 Hexo
date: 2023/07/23 17:25
updated: 2024/03/27 07:13:33
categories:
- [技术, 示例]
tags:
- Hexo
- GitHub
---

## 准备工作

1. 建立名为 `maojunxyz.github.io` 的储存库。
2. 将 Hexo 文件夹中的文件 push 到储存库的默认分支，默认分支通常名为 `main`。

- 将 `main` 分支 push 到 GitHub：

  ```
  $ git push -u origin main
  ```

- 默认情况下 `public/` 不会被上传(也不该被上传)，确保 `.gitignore` 文件中包含一行 `public/`。

3. 使用 `node --version` 指令检查你电脑上的 Node.js 版本，并记下该版本 (例如：`v16.y.z`)
4. 在储存库中前往 `Settings > Pages > Source`，并将 `Source` 改为 `GitHub Actions`。

> 注意：如果本地已经有了workflow配置文件pages.yml，推送代码到仓库执行actions后，再将 `Source` 改为 `Deploy from a branch`，并切换到构建后的`gh-pages`分支。

3. 在储存库中建立 `.github/workflows/pages.yml`，并填入以下内容 (将 20行的版本`16` 替换为上个步骤中记下的版本)：

```
.github/workflows/pages.ymlname: Pages

on:
  push:
    branches:
      - main # default branch

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          # If your repository depends on submodule, please see: https://github.com/actions/checkout
          submodules: recursive
      - name: Use Node.js 16.x
        uses: actions/setup-node@v2
        with:
          node-version: '16'
      - name: Cache NPM dependencies
        uses: actions/cache@v2
        with:
          path: node_modules
          key: ${{ runner.OS }}-npm-cache
          restore-keys: |
            ${{ runner.OS }}-npm-cache
      - name: Install Dependencies
        run: npm install
      - name: Build
        run: npm run build
      - name: Upload Pages artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

6. 部署完成后，前往 `https://maojunxyz.github.io` 查看网站。

> CNAME：若你使用了一个带有 `CNAME` 的自定义域名，你需要在 `source/` 文件夹中新增 `CNAME` 文件。



## 项目页面

如果你希望网站部署在 `maojunxyz.github.io` 的子目录中：

1. 建立名为 `blog` 的储存库，这样你的博客网址为 `maojunxyz.github.io/blog`。
2. 编辑你的 `_config.yml`，将 `url:` 更改为 `maojunxyz.github.io/blog`。
3. 在储存库中前往 `Settings > Pages > Source`，并将 `Source` 改为 `GitHub Actions`。
4. Commit 并 push 到默认分支上。
5. 部署完成后，前往 `https://maojunxyz.github.io/blog` 查看网站。

## Hexo 常用命令

### 创建一个新的文章

``` bash
$ hexo new "My New Post"
```

更新信息: [Writing](https://hexo.io/docs/writing.html)

### 运行服务

``` bash
$ hexo server
```

更新信息: [Server](https://hexo.io/docs/server.html)

### 生成静态文件

``` bash
$ hexo generate
```

更新信息: [Generating](https://hexo.io/docs/generating.html)

### 部署远程站点

``` bash
$ hexo deploy
```

更新信息: [Deployment](https://hexo.io/docs/one-command-deployment.html)



（完）
