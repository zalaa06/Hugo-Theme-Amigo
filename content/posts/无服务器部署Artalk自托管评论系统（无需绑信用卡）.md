---
title: "无服务器部署Artalk自托管评论系统（无需绑信用卡）"
date: 2026-03-24T15:02:34+08:00
lastmod: 2026-03-24T15:02:34+08:00
draft: false
author: "AI"
---

# 使用 Render 无服务器部署 Artalk 评论系统

与 **Gitalk**、**Waline**、**Twikoo** 等个人博客界的前辈一样，**Artalk** 是一款自托管评论系统，其颜值出众，具有简明的管理后台。但由于其依赖后端服务，无法通过 **Vercel** 或 **Netlify** 等静态托管平台直接部署。本文将介绍如何通过 **[Render](https://render.com/)** 实现 **Artalk** 的无服务器部署。

---

## ⚠️ 预先提醒
- **Render** 对 **Docker** 实例有严格审查，若检测到滥用（如科学上网等），账号可能被封禁。
- 请勿尝试违规操作，避免不必要的风险。

---

## 📦 准备数据库
**Artalk** 需要持久化数据库存储评论数据。**Render** 的免费 **PostgreSQL** 数据库会在 **90 天**后自动清空，因此需使用第三方数据库服务。

### 推荐数据库服务
| 数据库类型 | 服务提供商 | 链接 |
|------------|------------|------|
| **PostgreSQL** | Neon | [https://neon.tech/](https://neon.tech/) |
| **MySQL** | PlanetScale | [https://planetscale.com/](https://planetscale.com/) |

### 以 **Neon** 为例
1. 注册 **Neon** 账号，创建新项目（`Region` 可选 **Singapore**）。
2. 数据库名称可设为 `artalkme` 或其他自定义名称。
3. 获取数据库连接字符串（类似于）：
   ```plaintext
   postgresql://user@xxx.com:xxxxxxxx@yyyyyy.ap-southeast-1.aws.neon.tech/artalkme?sslmode=require
   ```
   复制此链接，后续配置 **Artalk** 时会用到。

---

## 🛠️ 准备配置文件
1. 从 **[GitHub](https://github.com/)** 下载基于 **661111****[GitHub](https://github.com/)** 前辈的 **Render Docker 配置**（或参考 [Artalk 官方文档](https://artalk.js.org/)）。
2. 修改 `conf.yml`（或 `config.yml`）中的关键配置：
   - **`app_key`**：随机生成一个字符串（用于加密）。
   - **数据库配置**：根据 **Neon** 提供的连接字符串填写：
# 数据库配置方式

## 方式一：手动配置数据库连接字符串（DSN）
你可以通过配置 `db.dsn` 直接指定数据库连接字符串，支持 sqlite、mysql、pgsql、mssql 等数据库类型，示例如下：

```yaml
db:
  type: mysql # 支持的数据库类型：sqlite, mysql, pgsql, mssql
  dsn: mysql://myuser:mypassword@localhost:3306/mydatabase?tls=skip-verify
```

## 方式二：手动填写数据库各项参数
通过逐项配置数据库的主机、端口、用户名等参数完成连接，以 PostgreSQL 为例的示例如下：

```yaml
db:
  type: "pgsql"          # 数据库类型
  host: "yyyyyy.ap-southeast-1.aws.neon.tech"  # 数据库主机地址
  port: 5432             # 数据库端口
  user: "user@xxx.com"   # 数据库用户名
  password: "xxxxxxxx"   # 数据库密码
  name: "artalkme"       # 数据库名称
  ssl: true              # 是否启用 SSL 连接
  charset: "utf8"        # 数据库字符集
```
   - **`trust_domains`**：添加所有允许使用 **Artalk** 的域名（包括博客域名和 **Artalk** 自身域名），否则可能无法加载。

---
最好加两个环境变量
Key	                 Value
ATK_HTTP_PROXY_HEADER=X-Forwarded-For
ATK_LOCALE=zh-CN


## 💻 本地运行 & 初始化管理员
**Artalk** 默认无管理员账户，需在本地运行一次以设置：

### Linux 系统示例
1. 下载 **Artalk** 二进制文件：
   ```bash
   wget -O artalk.tar.gz https://github.com/ArtalkJS/Artalk/releases/download/v2.6.4/artalk_v2.6.4_linux_amd64.tar.gz
   tar -zxvf artalk.tar.gz
   cd artalk_v2.6.4_linux_amd64
   ```
2. 修改 `config.yml`（参考上述数据库配置）。
3. 运行以下命令设置管理员账户：
   ```bash
   ./artalk admin -c config.yml
   ```
   按提示输入用户名和密码。

### （可选）本地配置调试
若需进一步配置，可先在本地运行 **Artalk** 服务：
```bash
./artalk server -c config.yml
```
- 访问 `http://localhost:2336` 登录后台（默认端口 `2336`）。
- 配置完成后，将 `config.yml` 的内容同步到 **GitHub** 项目的 `conf.yml` 中（因 **Render** 不保存实例数据）。

---

## 🚀 部署到 Render
1. **上传配置到 GitHub**：
   - 在 **GitHub** 创建新仓库，上传 **Docker 配置文件** 和修改后的 `conf.yml`。

2. **在 Render 创建服务**：
   - 注册 **[Render](https://render.com/)** 账号。
   - 点击 **New** → **Web Service**，选择 **Build and deploy from a Git repository**。
   - 连接 **GitHub** 账户，选择对应仓库。
   - **Region** 可选 **Singapore**（延迟较低）。
   - 点击 **Create Web Service** 开始构建。

3. **设置自定义域名**：
   - **Render** 默认域名可能被墙，建议绑定自定义域名（如 `artalk.yourdomain.com`）。
   - 在 **Render** 的 **Custom Domains** 中添加域名，并配置 **DNS** 解析。

4. **访问部署结果**：
   - 构建完成后，访问自定义域名，即可看到 **Artalk** 评论系统运行正常。

---

## 🌍 其他平台部署
- **Koyeb**：[https://www.koyeb.com/](https://www.koyeb.com/)
  - 支持 **Docker** 部署，但需绑定信用卡。
  - 流程与 **Render** 类似，可参考上述步骤调整。

---

## 📌 注意事项
1. **数据持久化**：确保数据库（如 **Neon**/**PlanetScale**）长期可用，避免评论丢失。
2. **定期备份**：建议定期导出数据库备份。
3. **防止滥用**：勿使用 **Render** 运行违规服务（如代理、爬虫等）。
4. **性能限制**：免费层 **Render** 实例会定期重启，可能影响短暂可用性。

---
引用：感谢开源项目的大神
https://github.com/LeenHawk/artalk-on-render
https://github.com/661111/artalk-on-render
