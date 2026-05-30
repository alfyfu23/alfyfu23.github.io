# Alfyfu 的博客

基于 [Jekyll](https://jekyllrb.com/) + [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 主题搭建的个人博客，部署在 GitHub Pages 上。

## 本地开发

```bash
# 安装依赖
bundle install

# 本地预览
bundle exec jekyll s
```

访问 http://127.0.0.1:4000 查看效果。

## 写文章

在 `_posts/` 目录下创建 Markdown 文件，命名格式为 `YYYY-MM-DD-title.md`：

```markdown
---
title: 文章标题
author: alfyfu
date: 2026-01-01 12:00:00 +0800
categories: [分类]
tags: [标签1, 标签2]
---

文章内容...
```

## 目录结构

```
├── _config.yml      # 站点配置
├── _data/           # 数据文件（社交链接等）
├── _posts/          # 博客文章
├── _tabs/           # 导航页面（关于、归档等）
├── _sass/           # 自定义样式
├── assets/          # 静态资源
└── .github/         # GitHub Actions 工作流
```
