---
title: "防盗链配置"
date: 2026-03-24T15:02:34+08:00
lastmod: 2026-03-24T15:02:34+08:00
draft: false
author: "AI"
---
你提供的代码是 **Apache（.htaccess）** 的防盗链配置，用于阻止其他网站直接访问 `.m3u8` 和 `.ts` 文件。下面详细解释如何配置和使用：

---

### **1. 这是 Apache 的 `.htaccess` 配置**
这段规则 **不是 PHP 文件**，也 **不是伪静态规则**，而是 **Apache 的 URL 重写和访问控制规则**，需要放在：
- **网站根目录的 `.htaccess` 文件**（如果 Apache 允许 `.htaccess` 生效）。
- **或者 Apache 虚拟主机配置**（如 `/etc/apache2/sites-available/yourdomain.conf`）。

---

### **2. 配置步骤**
#### **方法一：使用 `.htaccess`（适用于共享主机）**
1. 在你的网站根目录（如 `/var/www/html/`）创建或编辑 `.htaccess` 文件。
2. 添加以下内容：
   ```apache
   <FilesMatch "\.(m3u8|ts)$">
       # 开启 Referer 验证
       RewriteEngine On
       # 如果 Referer 为空（直接访问），允许（取消这行可禁止空 Referer）
       RewriteCond %{HTTP_REFERER} !^$
       # 如果 Referer 不是 yourdomain.com，拒绝访问
       RewriteCond %{HTTP_REFERER} !^https?://(www\.)?yourdomain\.com [NC]
       RewriteRule .* - [F,L]

       # CORS 限制（只允许 yourdomain.com 跨域访问）
       Header set Access-Control-Allow-Origin "https://yourdomain.com"
       Header set Access-Control-Allow-Methods "GET"
   </FilesMatch>
   ```
3. **替换 `yourdomain.com`** 为你的实际域名。
4. 确保 Apache 的 `mod_rewrite` 和 `mod_headers` 模块已启用：
   ```bash
   sudo a2enmod rewrite
   sudo a2enmod headers
   sudo systemctl restart apache2
   ```

#### **方法二：在 Apache 虚拟主机配置中设置（适用于独立服务器）**
1. 编辑 Apache 虚拟主机配置文件（如 `/etc/apache2/sites-available/yourdomain.conf`）。
2. 在 `<VirtualHost>` 块内添加：
   ```apache
   <Directory "/var/www/html">
       <FilesMatch "\.(m3u8|ts)$">
           RewriteEngine On
           RewriteCond %{HTTP_REFERER} !^$
           RewriteCond %{HTTP_REFERER} !^https?://(www\.)?yourdomain\.com [NC]
           RewriteRule .* - [F,L]
           Header set Access-Control-Allow-Origin "https://yourdomain.com"
       </FilesMatch>
   </Directory>
   ```
3. 重启 Apache：
   ```bash
   sudo systemctl restart apache2
   ```

---

### **3. 规则解释**
| 代码 | 作用 |
|------|------|
| `<FilesMatch "\.(m3u8|ts)$">` | 匹配 `.m3u8` 和 `.ts` 文件。 |
| `RewriteEngine On` | 开启 URL 重写引擎。 |
| `RewriteCond %{HTTP_REFERER} !^$` | 如果 Referer **不为空**，继续检查（取消此行可禁止空 Referer 访问）。 |
| `RewriteCond %{HTTP_REFERER} !^https?://(www\.)?yourdomain\.com [NC]` | 如果 Referer **不是** `yourdomain.com`，则触发规则。 |
| `RewriteRule .* - [F,L]` | 返回 **403 Forbidden** 并停止后续规则。 |
| `Header set Access-Control-Allow-Origin "https://yourdomain.com"` | 限制 CORS，只允许 `yourdomain.com` 跨域访问。 |

---

### **4. 测试防盗链是否生效**
1. **直接访问 M3U8 文件**：
   - 在浏览器地址栏输入：
     ```
     https://yourdomain.com/path/to/stream.m3u8
     ```
   - 应该返回 **403 Forbidden**（如果你注释了 `RewriteCond %{HTTP_REFERER} !^$`）。
   - 如果允许空 Referer，则可以访问。

2. **通过其他网站访问**：
   - 在另一个网站（如 `evil.com`）嵌入：
     ```html
     <video src="https://yourdomain.com/path/to/stream.m3u8"></video>
     ```
   - 应该被阻止（返回 403）。

3. **通过你的网站访问**：
   - 在 `yourdomain.com` 的页面嵌入播放器，应该正常播放。

4. **检查 CORS**：
   - 使用浏览器开发者工具（F12 → Network），查看响应头是否包含：
     ```
     Access-Control-Allow-Origin: https://yourdomain.com
     ```

---

### **5. 可能的问题及解决**
#### **问题 1：防盗链不生效**
- **原因**：Apache 未启用 `mod_rewrite` 或 `.htaccess` 被禁用。
- **解决**：
  ```bash
  sudo a2enmod rewrite
  sudo systemctl restart apache2
  ```
  确保虚拟主机配置中允许 `.htaccess`：
  ```apache
  <Directory "/var/www/html">
      AllowOverride All
  </Directory>
  ```

#### **问题 2：空 Referer 被拒绝**
- **场景**：有些浏览器或工具（如 `curl`）不发送 Referer，导致无法访问。
- **解决**：
  - 如果需要允许空 Referer，保留 `RewriteCond %{HTTP_REFERER} !^$`。
  - 如果需要禁止空 Referer，删除或注释掉这行。

#### **问题 3：HTTPS/HTTP 混合问题**
- 如果你的网站同时支持 HTTP 和 HTTPS，确保规则覆盖两种情况：
  ```apache
  RewriteCond %{HTTP_REFERER} !^https?://(www\.)?yourdomain\.com [NC]
  ```
  （`?` 表示 `s` 可选，即匹配 `http` 或 `https`）

#### **问题 4：CORS 错误**
- 如果播放器报错 `CORS policy`，确保：
  - `Access-Control-Allow-Origin` 与你的网站域名一致。
  - 如果使用子域名（如 `video.yourdomain.com`），需要添加：
    ```apache
    Header set Access-Control-Allow-Origin "https://video.yourdomain.com"
    ```

---

### **6. 进阶：结合 Token 验证（更安全）**
仅 Referer 防盗链可能被绕过（如伪造 Referer）。可以增加 **Token 验证**：
1. 生成带 Token 的 URL（如 `/stream.m3u8?token=abc123`）。
2. 在 `.htaccess` 中验证 Token：
   ```apache
   RewriteCond %{QUERY_STRING} !^token=valid_token_here [NC]
   RewriteRule .* - [F,L]
   ```
3. Token 由后端动态生成（如 PHP）：
   ```php
   $token = md5("secret_key" . $_SERVER['REQUEST_URI'] . time());
   $m3u8_url = "/stream.m3u8?token=" . $token;
   ```

---

### **7. 与 Nginx 的区别**
- 如果你用的是 **Nginx**，需要使用 `valid_referers`（见之前的回答）。
- 这段配置 **只适用于 Apache**。

---

### **总结**
1. 将代码放入 `.htaccess` 或 Apache 虚拟主机配置。
2. 确保 `mod_rewrite` 和 `mod_headers` 已启用。
3. 测试 Referer 和 CORS 是否生效。
4. 如需更高安全性，结合 **Token 验证** 或 **CDN 防盗链**。
