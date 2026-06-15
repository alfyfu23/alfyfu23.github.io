---
title: 从零搭建 Jekyll 博客 — 我的踩坑记录
layout: post
author: alfyfu
date: 2026-05-31 12:00:00 +0800
categories: [建站]
tags: [Jekyll, Chirpy, GitHub Pages, 踩坑]
pin: true
---

## 为什么选择 Jekyll + Chirpy？

想搭一个个人主页，既能展示项目作品，又能写博客。对比了几个方案：

| 方案 | 优点 | 缺点 |
|------|------|------|
| Hexo | 中文社区好 | Node.js 依赖重 |
| Hugo | 速度快 | 配置复杂 |
| **Jekyll + Chirpy** | **GitHub Pages 原生支持** | **Ruby 环境** |
| Next.js | 现代化 | 过度工程化 |

最终选了 Jekyll + Chirpy，因为 GitHub Pages 原生支持，Markdown 写作，部署简单。

## 搭建过程

### 1. 安装 Chirpy 主题

使用 gem-based 方式，在 `Gemfile` 中引入：

```ruby
gem "jekyll-theme-chirpy", "~> 7.0"
```

### 2. 配置 GitHub Actions 自动部署

Jekyll 3.x 在 GitHub Pages 默认环境已不支持，需要用 GitHub Actions 自行构建：

```yaml
# .github/workflows/pages-deploy.yml
name: "Build and Deploy"
on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ruby/setup-ruby@v1
        with:
          ruby-version: 3.3
          bundler-cache: true
      - run: bundle exec jekyll b -d "_site"
        env:
          JEKYLL_ENV: production
      - uses: actions/upload-pages-artifact@v3
        with:
          path: "_site"

  deploy:
    needs: build
    runs-on: ubuntu-latest
    permissions:
      pages: write
      id-token: write
    steps:
      - uses: actions/deploy-pages@v4
```

### 3. 在 GitHub Settings 中配置

Settings → Pages → Source 选择 **GitHub Actions**。

## 踩过的坑

### 坑 1：GitHub Actions 构建失败

最初 workflow 引用了不存在的 `steps.pages.outputs.base_path`，导致构建直接报错。

> **解决**：简化构建命令，去掉不必要的变量引用。
{: .prompt-tip }

### 坑 2：Tabs 页面 404

Chirpy 主题的 `_tabs/` 目录需要在 `_config.yml` 中显式声明 collections：

```yaml
collections:
  tabs:
    output: true
    sort_by: order
```

### 坑 3：自定义 CSS 不生效

在 `_sass/` 下放了自定义样式文件，但构建后完全没有效果。

> **原因**：Chirpy 主题的 gem-based 模式不会自动加载用户的 `_sass/` 文件。
> **解决**：将 CSS 直接内联到 `_layouts/home.html` 的 `<style>` 标签中。
{: .prompt-warning }

### 坑 4：暗色模式下样式异常

自定义 CSS 用了硬编码颜色（`#333`、`#fff`），切到暗色模式后白底白字。

> **解决**：全部替换为 Chirpy 主题提供的 CSS 变量（`var(--text-color)`、`var(--card-bg)` 等）。
{: .prompt-warning }

### 坑 5：git push 超时

默认 remote 用 HTTPS，国内网络不稳定。

> **解决**：切换为 SSH 协议 `git@github.com:user/repo.git`。
{: .prompt-tip }

## 最终效果

- 首页：Portfolio 风格（自我介绍 + 技术栈 + 项目展示 + 最近文章）
- 博客：Markdown 写作，支持分类、标签、搜索
- 部署：推送即自动构建，完全自动化
- 评论：Utterances（基于 GitHub Issues）

## 总结

搭建过程中踩了不少坑，但最终效果还是很满意的。Chirpy 主题功能强大，只要理清它的构建机制，后续维护非常轻松。
