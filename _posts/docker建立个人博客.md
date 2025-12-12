---
layout: post
title: 使用 Docker 建立个人博客
date: 2023-06-15 10:00:00
description: 记录使用 Docker 搭建 WordPress 个人博客的完整流程
tags: Docker WordPress 博客 教程
categories: 技术
---

# 使用 Docker 建立个人博客

本文记录使用 Docker 搭建 WordPress 个人博客的完整流程，包括 MySQL 数据库配置和 WordPress 容器部署。

## 安装 WordPress

```bash
docker pull wordpress:latest
```

## 安装 MySQL

可以选择本地安装 MySQL 或使用 Docker 容器。这里介绍 Docker 方式：

```bash
docker pull mysql:latest
```

## 启动和配置 Docker MySQL

```bash
docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```

### 进入 MySQL 容器

```bash
docker exec -it mysql bash
```

### 创建数据库

```sql
CREATE DATABASE IF NOT EXISTS wordpress DEFAULT CHARACTER SET UTF8mb4 collate utf8mb4_bin;
```

### 创建用户

```sql
CREATE USER IF NOT EXISTS 'wordpressuser'@'localhost' IDENTIFIED BY 'password';
CREATE USER IF NOT EXISTS 'wordpressuser'@'%' IDENTIFIED BY 'password';
```

### 授予用户权限

```sql
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'localhost';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpressuser'@'%';
```

### 刷新权限

```sql
FLUSH PRIVILEGES;
```

## 启动和配置 WordPress

### 使用 Docker 的 MySQL

```bash
docker run --name wordpress --link mysql:mysql -p 80:80 -d wordpress
```

### 使用本地的 MySQL

```bash
docker run --name some-wordpress \
  -e WORDPRESS_DB_HOST={mysql server IP}:3306 \
  -e WORDPRESS_DB_USER=... \
  -e WORDPRESS_DB_PASSWORD=... \
  -d wordpress
```

## 完成配置

启动完成后，访问服务器的 80 端口即可进入 WordPress 安装向导，按照提示完成博客的初始配置。
