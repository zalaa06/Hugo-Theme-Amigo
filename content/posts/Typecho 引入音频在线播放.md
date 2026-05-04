### Typecho 引入音频在线播放

Typecho 支持在文章中使用 **HTML5 `<audio>`** 或 **播放器插件** 嵌入音频。**本地音频**需先上传文件（默认只支持图片，推荐 FTP 或上传插件）；**在线音乐**（网易云/QQ 等）用插件一键解析。

#### 前置准备
1. **上传音频文件**：
   - FTP 到 `/usr/uploads/audio/`（创建文件夹），路径如 `https://你的域名/usr/uploads/audio/song.mp3`。
   - 或安装插件：**TeSharp** 或 **Uploader**（后台“外观 > 插件”搜索安装，支持 MP3 上传）。
2. **文章编辑**：后台“内容 > 撰写”，切换 **HTML 模式**（或 Markdown 支持 HTML）。
3. **清缓存**：修改后，删除 `/var/` 文件夹或后台“设置 > 维护”。

#### 方法1：HTML5 原生 `<audio>`（最简单，无插件，美观一般）
直接在文章 HTML 中插入，支持控件、自动播放（浏览器限）。

```html
<!-- 单曲播放 -->
<audio src="https://你的域名/usr/uploads/audio/song.mp3" controls preload="auto">
  您的浏览器不支持 audio 标签。
</audio>

<!-- 播放列表（多个 sources） -->
<audio controls>
  <source src="song1.mp3" type="audio/mpeg">
  <source src="song1.ogg" type="audio/ogg">
</audio>
```

- **属性**：`controls`（控件）、`autoplay`（自动）、`loop`（循环）、`muted`（静音）。
- **优点**：轻量、无 JS 依赖。
- **缺点**：样式朴素，不支持歌词/封面。手机兼容好。

#### 方法2：APlayer 插件（推荐，美观、支持网易云/QQ 等在线音乐）
**APlayer-Typecho**：复制网易云链接，后台生成代码，支持歌单/专辑/歌词/封面。

**安装步骤**：
1. 下载：[GitHub MoePlayer/APlayer-Typecho](https://github.com/MoePlayer/APlayer-Typecho)，解压到 `/usr/plugins/APlayer/`。
2. 后台“**外观 > 插件**” > 启用 **APlayer**。
3. 配置：插件设置中填网易云 API（默认可用），自定义皮肤/颜色。
4. **使用**：
   - 编辑文章，点击插件按钮（或短代码）。
   - 粘贴网易云/QQ 音乐链接（如 `https://music.163.com/#/song?id=123`），生成：
     ```
     [meting type="163" id="123" autoplay="false"]
     ```
   - 支持：网易云/QQ/虾米/百度/酷狗。

**效果**：吸底播放器、进度条、歌词同步、响应式。

#### 方法3：其他插件（本地/高级需求）
| 插件名称 | 主要功能 | 下载链接 | 引用 |
|----------|----------|----------|------|
| **AudioPlayer** | HTML5 本地 MP3，支持 ID3 标签/列表/配色 | [GitHub jzwalk/AudioPlayer](https://github.com/jzwalk/AudioPlayer) | `[mp3]音频URL[/mp3]` |
| **Plyr** | 自适应 MP3/MP4，HTML5 控件 | [qt06.com](https://www.qt06.com/post/373) | `<plyr-audio src="mp3URL"></plyr-audio>` |
| **cPlayer** | 美观 HTML5，支持网易云解析 | [GitHub journey-ad/cPlayer-Typecho-Plugin](https://github.com/journey-ad/cPlayer-Typecho-Plugin) | 短代码嵌入 |
| **APlayerAtBottom** | 全站底部悬浮播放 | [GitHub jstar0/APlayerAtBottom](https://github.com/jstar0/APlayerAtBottom) | 自动解析网易云 |

- **安装通用**：ZIP 下载 > `/usr/plugins/` > 后台启用 > 配置 > 编辑器按钮插入。

#### 手动引入 APlayer（无插件，主题集成）
1. 主题 `header.php` 添加 CDN：
   ```html
   <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer@1.10/dist/APlayer.min.css">
   <script src="https://cdn.jsdelivr.net/npm/aplayer@1.10/dist/APlayer.min.js"></script>
   ```
2. 文章中：
   ```html
   <div id="aplayer" class="aplayer" data-url="song.mp3" data-title="歌曲名" data-artist="歌手" data-cover="封面.jpg"></div>
   <script>const player = new APlayer({ container: document.getElementById('aplayer') });</script>
   ```

#### 常见问题
- **不播放**：检查 HTTPS（混合内容阻挡）、文件权限 644、浏览器策略（Chrome 禁 autoplay）。
- **手机**：HTML5/APlayer 均兼容。
- **全站播放**：用底部插件如 QPlayer。
- **社区**：forum.typecho.org 或 GitHub Issues。

推荐 **APlayer-Typecho**，5 分钟搞定在线音乐！如需具体主题适配，提供更多细节。
