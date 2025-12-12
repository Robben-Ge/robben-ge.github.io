# Robben Ge's Personal Website

个人学术网站，基于 [al-folio](https://github.com/alshedivat/al-folio) 主题构建。

## 本地开发

### 方式一：使用 Docker（推荐）

最简单的方式，无需安装 Ruby 环境：

```bash
# 启动开发服务器
docker compose up

# 或者使用 docker-compose（旧版本）
docker-compose up
```

访问 http://localhost:8080

停止服务器：按 `Ctrl+C` 或运行 `docker compose down`

### 方式二：本地 Ruby 环境

如果你已经安装了 Ruby 和 Bundler：

```bash
# 1. 安装依赖
bundle install

# 2. 启动开发服务器
bundle exec jekyll serve

# 或者使用简写
bundle exec jekyll s
```

访问 http://localhost:4000

#### 安装 Ruby 环境（如果还没有）

**macOS:**

```bash
# 使用 Homebrew
brew install ruby

# 或使用 rbenv
brew install rbenv ruby-build
rbenv install 3.1.0
rbenv global 3.1.0
```

**Linux (Ubuntu/Debian):**

```bash
sudo apt-get update
sudo apt-get install ruby-full build-essential zlib1g-dev
```

**Windows:**

- 下载并安装 [RubyInstaller](https://rubyinstaller.org/)

安装完成后，安装 Bundler：

```bash
gem install bundler
```

## 代码格式化

项目使用 Prettier 进行代码格式化：

```bash
# 安装 Node.js 依赖（首次运行）
npm install

# 检查代码格式
npx prettier . --check

# 自动修复格式问题
npx prettier . --write
```

## 构建

生成静态网站文件：

```bash
# 使用 Docker
docker compose run jekyll bundle exec jekyll build

# 或使用本地环境
bundle exec jekyll build
```

构建后的文件在 `_site` 目录中。

## 项目结构

```
.
├── _posts/          # 博客文章
├── _projects/       # 项目页面
├── _pages/          # 静态页面
├── _includes/       # Liquid 模板片段
├── _layouts/        # 页面布局
├── _sass/           # SCSS 样式文件
├── assets/          # 静态资源（图片、JS、CSS）
│   └── img/         # 图片资源
│       └── projects/ # 项目相关图片
├── _config.yml      # Jekyll 配置文件
├── Gemfile          # Ruby 依赖
└── docker-compose.yml # Docker 配置
```

## 图片使用说明

### 项目封面图

在项目文件的 front matter 中设置 `img` 字段，用于在项目列表页显示封面图：

```yaml
---
layout: page
title: 项目名称
img: assets/img/projects/your-image.jpg # 项目封面图路径
importance: 1
category: work
---
```

### 在项目内容中显示图片

使用 Liquid 模板在项目页面中显示图片：

```liquid
<div class="row mt-3">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/projects/your-image.jpg" title="图片标题" class="img-fluid rounded z-depth-1" zoomable=true %}
  </div>
</div>
<div class="caption">图片说明文字</div>
```

### 图片路径规范

- 项目相关图片应放在 `assets/img/projects/` 目录下
- 博客文章图片应放在 `assets/img/` 目录下
- 使用相对路径，从 `assets/img/` 开始

## 部署

网站部署在 GitHub Pages，通过 GitHub Actions 自动构建和部署。

推送到 `main` 分支后，GitHub Actions 会自动构建并部署网站。

## 常见问题

### Docker 权限问题

如果遇到权限错误，可以在 `docker-compose.yml` 中取消注释 build args 部分，并填入你的用户信息：

```bash
# 获取你的用户信息
id -u    # USERID
id -gn   # GROUPNAME
id -g    # GROUPID
echo $USER  # USERNAME
```

### Bundle 锁定问题

如果遇到 `Waiting for another process to let go of lock` 错误：

```bash
# 删除锁文件
rm /usr/local/rvm/gems/default/bundler.lock

# 或使用 rbenv 时
rm ~/.bundle/config
```

### 端口被占用

如果 8080 或 4000 端口被占用，可以修改端口：

**Docker:** 修改 `docker-compose.yml` 中的端口映射
**本地:** 使用 `bundle exec jekyll serve -P 4001` 指定其他端口

## 更多信息

- [Jekyll 官方文档](https://jekyllrb.com/docs/)
- [al-folio 主题文档](https://github.com/alshedivat/al-folio)
