---
title: Hexo 个人博客部署笔记（基于 Cursor 首次搭建踩坑与解决方案）
date: 2025-11-16 19:01:27
tags: [博客, Hexo]
categories: 踩坑
---

# Hexo 个人博客部署笔记（基于 Cursor 首次搭建踩坑与解决方案）
## 一、搭建背景与核心问题
- 工具：首次使用 Cursor 编辑器搭建 Hexo 博客
- 核心问题：执行 `hexo generate` 后，`public` 目录仅生成 CSS/JS/图片等静态资源，**缺失 index.html 及文章相关 HTML 文件**，博客无法正常访问。

## 二、问题根源分析
Cursor 对 Hexo 项目的「部分初始化」导致环境异常，具体原因如下：
1. Cursor 初始化流程不完整，未按 Hexo 官方规范创建项目结构（如缺失核心配置、插件依赖未正确声明）；
2. Hexo 脚手架（`hexo-cli`）无法识别被 Cursor 修改过的项目，导致插件加载顺序错乱（渲染器、生成器未按生命周期执行）；
3. 依赖管理混乱，关键插件（如 `hexo-renderer-marked`、`hexo-generator-index`）未被正确注册，生成器无法处理 Markdown 文章，最终不生成 HTML。

## 三、解决方案（两种可行方案）
### 方案 1：自定义 Hexo 执行命令（临时解决）
通过手动编写 `hexo.js` 脚本，强制指定插件加载顺序，绕过 Cursor 初始化的异常环境。
1. 脚本核心逻辑：
   - 初始化 Hexo 实例并设置全局变量；
   - 在 `hexo.load()` 前手动加载渲染器（`hexo-renderer-marked`、`hexo-renderer-ejs` 等）；
   - 显式触发 `hexo.source.process()` 处理文章文件；
   - 手动加载生成器（`hexo-generator-index`、`hexo-generator-archive` 等），确保生成逻辑执行。
2. 执行方式：
   ```powershell
   # 清理缓存并生成文件
   node hexo.js clean && node hexo.js generate
   # 本地启动测试
   node hexo.js server
   ```
3. 优势：无需重构项目，快速解决 HTML 生成问题；
4. 劣势：脚本维护成本高，后续添加插件需手动修改加载逻辑，易出现兼容性问题。

### 方案 2：按官方文档手动搭建 Hexo（推荐，一劳永逸）
放弃 Cursor 不完全的初始化，按 Hexo 官方流程从零搭建，确保环境纯净、配置规范。
1. 具体步骤：
   - 环境准备：安装 Node.js（≥14.x）、Git、Hexo 脚手架（`npm install -g hexo-cli`）；
   - 项目初始化：新建空目录，执行 `hexo init .` 完成完整初始化（生成标准目录结构、依赖配置）；
   - 安装插件：按需安装生成器、渲染器、部署器（`npm install hexo-generator-index hexo-renderer-marked hexo-deployer-git --save`）；
   - 配置与测试：修改 `_config.yml`（站点信息、主题、部署规则），执行 `hexo clean && hexo generate && hexo server` 验证 HTML 生成。
2. 优势：环境规范，插件自动加载无需手动干预，后续维护、升级更顺畅；
3. 劣势：需重新搭建项目，迁移文章/配置需手动备份恢复。

## 四、总结与经验教训
1. 首次搭建建议优先采用「方案 2：官方文档流程」，避免第三方工具（如 Cursor）的不完全初始化导致环境异常；
2. 核心避坑点：
   - 确保 `source/_posts` 下的文章包含完整 Front-matter（`title`、`date` 等必填字段）；
   - `_config.yml` 中 `theme` 配置需与 `themes/` 下的主题文件夹名称一致；
   - 插件需通过 `npm install` 安装并在 `package.json` 中声明，避免手动 `require` 导致的注册失败。
3. 部署建议：后续可配置 GitHub Actions 自动部署，无需本地执行生成/部署命令，提升效率。


