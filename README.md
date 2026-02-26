 🚀 一个基于 GitHub Actions 自动部署的 HTML 文件浏览器，支持自动扫描目录、生成文件列表并部署到 GitHub Pages。

## ✨ 功能特性

- **📂 自动扫描** - 自动扫描 html/ 目录下的所有 HTML 文件
- **🔄 实时同步** - 每次推送代码自动更新文件列表
- **🌐 在线访问** - 通过 GitHub Pages 在线浏览 HTML 文件
- **📱 响应式设计** - 支持移动端和桌面端访问
- **⚡ 零配置** - 开箱即用，无需复杂设置

## 🚀 快速开始

### **1**、创建仓库结构

```markdown
你的仓库/
├── index.html          # 前端页面（主入口）
├── html/               # 存放所有 HTML 文件的文件夹
│   ├── example1.html
│   └── subfolder/
│       └── example2.html
└── .github/
    └── workflows/
        └── deploy.yml  # GitHub Actions 工作流
 ```
```

### 2. 创建工作流文件

创建 `.github/workflows/deploy.yml`：

```yaml
name: Generate file list and deploy to Pages

on:
  push:
    branches: [ main ]  # 根据你的默认分支调整

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Generate filelist.json
        run: |
          node -e "
          const fs = require('fs');
          const path = require('path');
          const htmlDir = 'html';
          const results = [];
          
          function walk(dir, base = '') {
            const list = fs.readdirSync(dir);
            for (const file of list) {
              const fullPath = path.join(dir, file);
              const stat = fs.statSync(fullPath);
              if (stat.isDirectory()) {
                walk(fullPath, path.join(base, file));
              } else if (file.endsWith('.html') || file.endsWith('.htm')) {
                results.push(path.join(base, file));
              }
            }
          }
          
          if (fs.existsSync(htmlDir)) {
            walk(htmlDir, 'html');
          }
          
          fs.writeFileSync('filelist.json', JSON.stringify(results, null, 2));
          console.log('✅ Generated filelist.json with', results.length, 'entries.');
          "

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

### 3. 启用 GitHub Pages

1. 进入仓库 **Settings** → **Pages**
2. **Build and deployment** → Source 选择 **GitHub Actions**
3. 保存设置

### 4. 部署完成

推送代码后，访问：

```
https://<你的用户名>.github.io/<仓库名>/
```

## 📁 项目结构

```
.
├── .github/
│   └── workflows/
│       └── deploy.yml      # 自动部署工作流
├── html/                   # 你的 HTML 文件目录
│   ├── index.html
│   └── ...
├── index.html              # 主页面（文件浏览器 UI）
├── filelist.json           # 自动生成的文件列表
└── README.md               # 本文件
```

## 🛠️ 自定义配置

### 修改扫描目录

编辑 `.github/workflows/deploy.yml` 中的 `htmlDir` 变量：

```javascript
const htmlDir = '你的目录名';  // 默认为 'html'
```

### 支持更多文件类型

修改文件过滤条件：

```javascript
// 添加更多扩展名
} else if (file.endsWith('.html') || file.endsWith('.htm') || file.endsWith('.svg')) {
```

## 🐛 常见问题

<details>
<summary><b>❓ 工作流运行失败，提示 "404"</b></summary>

**原因**：GitHub Pages 未启用或未配置为 GitHub Actions 模式。

**解决**：
1. 进入 Settings → Pages
2. Source 选择 **GitHub Actions**
3. 重新运行工作流

</details>

<details>
<summary><b>❓ 文件列表没有更新</b></summary>

**原因**：`filelist.json` 生成失败或缓存问题。

**解决**：
1. 检查 `html/` 目录是否存在
2. 确认文件扩展名为 `.html` 或 `.htm`
3. 手动触发工作流重新运行

</details>

<details>
<summary><b>❓ 页面显示空白</b></summary>

**原因**：`index.html` 入口文件缺失或路径错误。

**解决**：
1. 确认仓库根目录有 `index.html`
2. 检查浏览器控制台是否有 404 错误
3. 确认 `filelist.json` 已正确生成

</details>

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

1. Fork 本仓库
2. 创建你的分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 打开 Pull Request

## 📄 许可证

本项目基于 [MIT](LICENSE) 许可证开源。

---

<p align="center">
  Made with ❤️ by <a href="https://github.com/{username}">{username}</a>
</p>
```

**注意**：使用前请将 `{username}` 和 `{repo}` 替换为你的 GitHub 用户名和仓库名。
