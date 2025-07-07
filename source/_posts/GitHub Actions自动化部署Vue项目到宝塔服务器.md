---
title: GitHub Actions自动化部署Vue项目到宝塔服务器
index_img: /img/芙莉莲.jpg
date: 2025-07-7 11:42:00
tags: 
  - GitHub Actions
---
# GitHub Actions自动化部署Vue项目到宝塔服务器

**通过GitHub Actions自动化部署Vue项目到宝塔服务器，可实现代码推送后自动构建和部署，极大提升开发效率，减少人工操作错误**。整个流程包括服务器环境准备、SSH密钥对生成与配置、GitHub仓库配置以及GitHub Actions工作流配置四个关键步骤。这套方案特别适合个人开发者和小型团队，无需依赖复杂的云服务或付费部署工具，即可实现高效、安全的持续集成/持续部署(CI/CD)。

## 一、服务器环境准备

### 1.1 宝塔面板安装与基础配置

**宝塔面板**是一个功能强大的Linux服务器管理面板，提供可视化的Web界面来管理服务器。你可以从[宝塔官网](https://www.bt.cn/)获取最新的安装脚本和详细文档。

对于Ubuntu/Debian系统，使用以下命令安装：

```bash
wget -O install.sh <http://download.bt.cn/install/install-ubuntu_6.0.sh> && sudo bash install.sh
```

对于CentOS系统：

```bash
yum install -y wget && wget -O install.sh <http://download.bt.cn/install/install_6.0.sh> && sh install.sh
```

安装完成后，在面板中安装必要的服务：

- **Node.js**：用于本地构建测试（可选）
- **Nginx**：用于Web服务器

> 提示：宝塔官网（https://www.bt.cn/）提供了详细的安装文档和视频教程，建议首次使用的用户参考官方指南进行安装。
>

### 1.2 创建网站并配置Nginx

在宝塔面板中创建网站：

1. 进入"网站"模块，点击"添加站点"
2. 设置域名或IP地址
3. 设置网站根目录（如：`/www/wwwroot/yourdomain.com`）
4. 不选择PHP版本（前端项目无需PHP）

创建完成后，配置Nginx以支持Vue路由：

```
server {
    listen 80;
    server_name yourdomain.com;  # 替换为你的域名或IP

    root /www/wwwroot/yourdomain.com;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 静态资源缓存
    location ~* \\.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}

```

**关键配置说明**：

- `root`：指向Vue项目构建后的部署目录
- `try_files`：确保Vue Router的History模式正常工作
- 静态资源缓存配置：提升页面加载性能

### 1.3 安装必要工具

确保服务器已安装`unzip`工具（用于解压构建产物）：

```bash
sudo apt update
sudo apt install unzip

```

## 二、SSH密钥对生成与配置

### 2.1 生成SSH密钥对

在本地机器生成SSH密钥对：

```bash
ssh-keygen -t rsa -b 4096 -C "github-actions@yourdomain.com"

```

**建议**：

- 密钥文件名可指定为`github_actions_rsa`以便区分
- 密码留空，支持自动化部署
- 妥善保管私钥文件

### 2.2 配置服务器公钥

将公钥添加到服务器的授权列表：

```bash
# 1. 查看公钥内容
cat ~/.ssh/github_actions_rsa.pub

# 2. 连接服务器
ssh user@server_ip

# 3. 配置公钥
mkdir -p ~/.ssh
echo "公钥内容" >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

```

**权限设置注意事项**：

- 确保SSH用户对网站目录有读写权限
- 如使用宝塔面板默认用户，可能需要调整目录所有者
- 建议创建专用部署用户，避免使用root账户

### 2.3 测试SSH连接

验证SSH免密登录是否配置成功：

```bash
ssh -i ~/.ssh/github_actions_rsa user@server_ip

```

## 三、GitHub仓库配置

### 3.1 设置Repository Secrets

在GitHub仓库中配置以下机密变量：

1. 进入仓库 → Settings → Secrets and variables → Actions
2. 添加以下secrets：

| 名称 | 值 | 说明 |
| --- | --- | --- |
| `SSH_HOST` | 服务器IP地址 | 如：`123.456.789.0` |
| `SSH_USERNAME` | SSH用户名 | 如：`root`或`btuser` |
| `SSH_PRIVATE_KEY` | SSH私钥内容 | 完整的私钥文件内容 |
| `DEPLOY_DIR` | 部署目录路径 | 如：`/www/wwwroot/yourdomain.com` |

### 3.2 验证配置

确保所有secrets配置正确，特别注意：

- SSH_PRIVATE_KEY包含完整的私钥内容（包括头尾标识）
- DEPLOY_DIR路径与宝塔面板中设置的网站根目录一致
- SSH_HOST为服务器的公网IP地址

## 四、GitHub Actions工作流配置

在项目根目录创建`.github/workflows/deploy.yml`文件：

```yaml
name: Vue Deploy  # 工作流名称

on:
  push:
    branches:
      - master  # 仅当推送至 master 分支时触发部署

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest  # 使用 GitHub 提供的最新 Ubuntu 环境

    steps:
      # 1. 检出代码仓库
      - name: Checkout repository
        uses: actions/checkout@v4  # 使用官方 Action 检出代码

      # 2. 安装 Node.js 环境
      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20.11'  # 指定 Node.js 版本,可以自选

      # 3. 安装项目依赖
      - name: Install dependencies
        run: npm install  # 安装项目所需的 npm 包

      # 4. 构建并压缩 dist 内容
      - name: Build and Zip
        run: |
          sudo apt-get update && sudo apt-get install -y zip  # 安装 zip 工具
          npm run build  # 执行 Vue 项目构建
          cd dist  # 进入 dist 目录
          zip -r ../dist-content.zip ./*  # 压缩 dist 内容（不包含 dist 文件夹）

      # 5. 上传 dist-content.zip（可选，用于调试）
      - name: Upload dist-content.zip
        uses: actions/upload-artifact@v4
        with:
          name: dist-content  # 构建产物名称
          path: dist-content.zip  # 构建产物路径

      # 6. 通过 SCP 将 dist-content.zip 传输到服务器
      - name: Transfer dist-content.zip via SCP
        uses: appleboy/scp-action@v0.1.7
        with:
          host: ${{ secrets.SSH_HOST }}  # 服务器 IP（从 GitHub Secrets 获取）
          username: ${{ secrets.SSH_USERNAME }}  # SSH 用户名（从 GitHub Secrets 获取）
          key: ${{ secrets.SSH_PRIVATE_KEY }}  # SSH 私钥（从 GitHub Secrets 获取）
          source: "dist-content.zip"  # 本地文件路径
          target: ${{ secrets.DEPLOY_DIR }}  # 服务器目标目录（从 GitHub Secrets 获取）

      # 7. SSH 登录服务器并执行部署脚本
      - name: Deploy via SSH
        uses: appleboy/ssh-action@v0.1.7
        with:
          host: ${{ secrets.SSH_HOST }}
          username: ${{ secrets.SSH_USERNAME }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            shopt -s extglob  # 启用 Bash 的扩展 glob 模式（支持 !(pattern) 语法）
            DEPLOY_DIR=${{ secrets.DEPLOY_DIR }}  # 从 GitHub Secrets 获取部署目录路径
            cd $DEPLOY_DIR  # 进入部署目录
            rm -rf !(dist-content.zip)  # 删除所有文件，但保留 dist-content.zip
            unzip -o dist-content.zip  # 解压 dist-content.zip（-o 保留符号链接）
            rm -f dist-content.zip  # 删除 zip 文件（可选）
            nginx -s reload  # 重载 Nginx 服务（确保 Nginx 配置正确）

```

## 五、部署流程说明

### 5.1 触发条件

- 推送代码到`master`分支时自动触发
- 可根据需要修改为其他分支或添加tag触发条件

### 5.2 构建过程

1. **代码检出**：获取最新代码
2. **环境准备**：安装Node.js和项目依赖
3. **项目构建**：执行`npm run build`生成dist目录
4. **文件压缩**：将 dist 内容打包为zip文件

### 5.3 部署过程

1. **文件传输**：通过SCP将构建产物传输到服务器
2. **清理旧文件**：删除部署目录中的旧文件
3. **解压部署**：解压新的构建产物
4. **服务重载**：重新加载Nginx配置

## 六、常见问题与解决方案

### 6.1 权限问题

**问题**：部署时提示权限不足
**解决**：

```bash
# 调整部署目录权限
sudo chown -R $SSH_USERNAME:$SSH_USERNAME $DEPLOY_DIR
sudo chmod -R 755 $DEPLOY_DIR

```

### 6.2 Nginx重载失败

**问题**：`nginx -s reload`执行失败
**解决**：

- 检查Nginx配置文件语法：`nginx -t`
- 确保SSH用户有执行nginx命令的权限
- 可使用`sudo nginx -s reload`或配置sudoers

### 6.3 构建失败

**问题**：`npm install`或`npm run build`失败
**解决**：

- 检查`package.json`中的scripts配置
- 确保Node.js版本与项目要求匹配
- 检查是否有私有依赖需要配置访问权限

### 6.4 路由404问题

**问题**：Vue Router的History模式路由无法访问
**解决**：确保Nginx配置中包含`try_files $uri $uri/ /index.html;`
