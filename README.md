# GYung.github.io

这是我的 GitHub Pages 博客源码（Jekyll + minima）。

## 本地预览

GitHub Pages 会自动在远端构建。如果你想在本地预览：

1. 安装 Ruby（macOS 可用 Homebrew）
2. 安装构建依赖：
   ```bash
   gem install bundler jekyll
   ```
3. 启动本地服务：
   ```bash
   bundle exec jekyll serve --livereload
   ```
4. 打开 `http://localhost:4000`

提示：如果没有 Gemfile，GitHub Pages 仍会使用托管环境构建；本地可直接使用 `jekyll` 命令。

## 目录结构

- `_config.yml`: 站点配置（主题、导航、插件、元信息）
- `index.md`: 主页（最新文章列表）
- `about.md`: 关于页面
- `_posts/`: 文章目录，文件命名规则 `YYYY-MM-DD-title.md`
- `assets/main.scss`: 自定义样式，继承 minima 主题
- `404.html`: 404 页面

## 写作

- 新建文章：在 `_posts/` 下新增文件，如 `2025-01-01-new-post.md`
- Front Matter 示例：
  ```yaml
  ---
  layout: post
  title: "文章标题"
  categories: [分类]
  tags: [标签]
  ---
  ```

## 自定义

- 修改站点标题、描述、导航：编辑 `_config.yml`
- 开关深浅色：`minima.skin: auto | classic | dark | solarized`
- 添加社交链接：`minima.social_links`

## 部署

- 推送到 `main` 分支后，GitHub Pages 会自动构建。
- 站点地址：`https://gyung.github.io`