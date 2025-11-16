# 详细搭建指南

## 📖 完整搭建流程

### 第一步：准备工作

1. **安装 Node.js**
   - 访问 https://nodejs.org/
   - 下载并安装 LTS 版本（推荐 v18 或更高）

2. **验证安装**
   ```bash
   node -v
   npm -v
   ```

3. **安装 Git**
   - 访问 https://git-scm.com/
   - 下载并安装 Git

### 第二步：创建 GitHub 仓库

#### 选项 A：使用 username.github.io 仓库（推荐）

1. 登录 GitHub
2. 创建新仓库，名称格式：`yourusername.github.io`
   - 例如：如果你的用户名是 `zhangsan`，仓库名就是 `zhangsan.github.io`
3. 设置为 Public（GitHub Pages 需要）
4. 不要初始化 README、.gitignore 或 license（我们稍后会上传）

**优点：** 访问地址更简洁，直接是 `https://yourusername.github.io`

#### 选项 B：使用自定义名称仓库

1. 创建任意名称的仓库，例如 `my-blog`
2. 在 Settings → Pages 中：
   - Source 选择 "Deploy from a branch"
   - Branch 选择 `gh-pages` 分支（或 `main` 分支，取决于部署方式）

**访问地址：** `https://yourusername.github.io/my-blog/`

### 第三步：初始化项目

1. **安装 Hexo CLI**
   ```bash
   npm install -g hexo-cli
   ```

2. **如果是新项目，初始化 Hexo**
   ```bash
   npx hexo init
   ```

3. **安装依赖**
   ```bash
   npm install
   ```

4. **安装部署插件**
   ```bash
   npm install --save hexo-deployer-git
   ```

### 第四步：配置项目

1. **编辑 `_config.yml`**

   ```yaml
   # 基本信息
   title: 我的个人博客
   subtitle: ''
   description: '分享技术，记录生活'
   author: 你的名字
   language: zh-CN
   
   # URL 配置（根据你的仓库选择）
   # 选项 A：username.github.io 仓库
   url: https://yourusername.github.io
   root: /
   
   # 选项 B：自定义名称仓库
   # url: https://yourusername.github.io/my-blog
   # root: /my-blog/
   
   # 部署配置
   deploy:
     type: git
     repo: https://github.com/yourusername/your-repo-name.git
     branch: main  # 或 gh-pages（取决于仓库设置）
     message: 'Site updated: {{ now("YYYY-MM-DD HH:mm:ss") }}'
   ```

2. **配置 Git 认证**

   **方式 1：使用 SSH Key（推荐）**
   
   - 生成 SSH Key：
     ```bash
     ssh-keygen -t ed25519 -C "your_email@example.com"
     ```
   - 将公钥添加到 GitHub：Settings → SSH and GPG keys → New SSH key
   - 修改 `_config.yml` 中的 repo 为 SSH 格式：
     ```yaml
     repo: git@github.com:yourusername/your-repo-name.git
     ```

   **方式 2：使用 Personal Access Token**
   
   - GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)
   - 生成新 token，勾选 `repo` 权限
   - 修改 `_config.yml` 中的 repo：
     ```yaml
     repo: https://[TOKEN]@github.com/yourusername/your-repo-name.git
     ```

### 第五步：创建第一篇文章

```bash
npm run new "我的第一篇文章"
```

**注意：** 如果直接使用 `hexo new` 命令无法识别项目，请使用 `npm run new` 或 `node hexo.js new`。

编辑 `source/_posts/我的第一篇文章.md`，添加内容：

```markdown
---
title: 我的第一篇文章
date: 2024-01-01 12:00:00
tags: [博客, Hexo]
categories: 随笔
---

欢迎来到我的博客！

这是一篇测试文章。
```

### 第六步：本地测试

```bash
npm run server
```

**注意：** 如果直接使用 `hexo server` 命令无法识别项目，请使用 `npm run server` 或 `node hexo.js server`。

访问 http://localhost:4000 查看效果。

### 第七步：部署到 GitHub Pages

#### 使用 Hexo Deploy（方法一）

```bash
npm run deploy
```

或者分步执行：

```bash
npm run clean
npm run generate
node hexo.js deploy
```

**注意：** 如果直接使用 `hexo` 命令无法识别项目，请使用 `npm run` 脚本命令。

首次部署可能需要输入 GitHub 用户名和密码（使用 Personal Access Token 作为密码）。

#### 使用 GitHub Actions（方法二，已配置）

1. **推送源代码到 GitHub**
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/yourusername/your-repo-name.git
   git push -u origin main
   ```

2. **配置 GitHub Pages**
   - 进入仓库 Settings → Pages
   - Source 选择 "GitHub Actions"（使用 Actions 自动部署时）
   - 或者 Source 选择 "Deploy from a branch"，Branch 选择 `gh-pages`（使用 Hexo Deploy 时）
   - 等待几分钟，访问你的博客地址

### 第八步：自定义域名（可选）

1. **在域名服务商添加 CNAME 记录**
   - 类型：CNAME
   - 主机记录：www（或其他子域名）
   - 记录值：`yourusername.github.io`

2. **在项目根目录创建 `source/CNAME` 文件**
   ```
   www.yourdomain.com
   ```

3. **如果使用 GitHub Actions，在 `deploy.yml` 中配置**
   ```yaml
   cname: 'www.yourdomain.com'
   ```

4. **在仓库 Settings → Pages 中设置 Custom domain**

## 🔍 常见问题

### Q1: 部署后显示 404

**解决方案：**
- 检查 `_config.yml` 中的 `url` 和 `root` 配置是否正确
- 等待几分钟让 GitHub Pages 完成构建
- 检查仓库 Settings → Pages 中的 Source 设置是否正确（"GitHub Actions" 或 "Deploy from a branch"）

### Q2: 图片无法显示

**解决方案：**
- 将图片放在 `source/images/` 目录
- 在文章中引用：`![图片](/images/image.png)`
- 或使用图床服务（如 sm.ms、imgur 等）

### Q3: 部署时提示权限错误

**解决方案：**
- 确保已配置 SSH Key 或 Personal Access Token
- 检查仓库 URL 是否正确
- 确认对仓库有写权限

### Q4: 想要更换主题

**解决方案：**
1. 安装主题：
   ```bash
   git clone https://github.com/theme-name/theme-name.git themes/theme-name
   ```
2. 修改 `_config.yml`：
   ```yaml
   theme: theme-name
   ```
3. 参考主题文档进行配置

### Q5: 中文文章标题乱码

**解决方案：**
- 确保 `_config.yml` 中 `language: zh-CN`
- 确保文件使用 UTF-8 编码

## 🎯 下一步

- [ ] 更换喜欢的主题
- [ ] 配置 RSS 订阅
- [ ] 添加评论系统（如 Gitalk、Valine）
- [ ] 配置 Google Analytics
- [ ] 设置自定义域名
- [ ] 优化 SEO 设置

## 📞 获取帮助

- [Hexo 官方文档](https://hexo.io/docs/)
- [Hexo GitHub Issues](https://github.com/hexojs/hexo/issues)
- [GitHub Pages 帮助](https://docs.github.com/en/pages)

---

**祝你搭建成功！** 🎉
