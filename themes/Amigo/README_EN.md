# Amigo - Minimalist WeChat Moments Style Hugo Theme

[![Hugo](https://img.shields.io/badge/Hugo-%230076D1.svg?style=flat&logo=hugo&logoColor=white)](https://gohugo.io/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

Amigo is a minimalist blog theme for [Hugo](https://gohugo.io/), inspired by **WeChat Moments**. It aims to provide a private, intimate, and easy-to-read space for sharing updates, supporting PJAX seamless loading and multiple comment systems.

[中文文档](./README.md)

---

## ✨ Features

- 📱 **Moments UI**: Highly restored WeChat Moments visual experience, supporting 9-grid image display.
- 🚀 **Full-site PJAX**: Smooth, no-refresh page transitions for an enhanced browsing experience.
- 🌓 **Dark Mode**: Supports manual toggle and system-wide synchronization.
- 💬 **Multi-comment Support**: Built-in support for **Artalk / Twikoo / Giscus**.
- 🖼️ **Image Lightbox**: Integrated ViewImage.js for zooming into images with a single click.
- 🎨 **Beautiful Typography**: Optimized localized Chinese fonts (built-in *zql* font) for a better reading experience.
- 🛠️ **Responsive Design**: Perfectly adapted for mobile, tablet, and desktop.
- 🔙 **Smart Header**: Automatically toggles background and displays title on scroll, with integrated "Back to Top" functionality.

## 📸 Screenshots

*(Suggested: Add screenshots of your theme here)*

## ⚙️ Preparation

Before we start, let's get a few things ready:

### Required Environment

- **Hugo Extended**: Version >= 0.120 is recommended (Standard version might fail due to advanced features).
- **Git**: To download the theme and pull updates.
- **Build Environment**: A machine or CI/CD runner that can run `hugo --minify`.

### Optional Services (Choose Your Comment System)

**Artalk:**
- A deployed Artalk backend (HTTPS is required).
- Create a site in the admin panel and remember the `site` name.

**Twikoo:**
- Tencent Cloud: Remember the Environment ID (`envId`).
- Vercel / Self-hosted: Remember the backend URL, e.g., `https://twikoo.your-domain.com`.
- Make sure to configure CORS in the backend to allow your blog domain.

**Giscus:**
- A GitHub repository with Discussions enabled.
- Go to [giscus.app](https://giscus.app) to generate the config and get parameters like `repo`, `repoId`, `category`.

Once these are ready, we can start configuring!

## 🚀 Quick Start

### 1. Install Theme

Run this command in your Hugo site root directory:

```bash
git clone https://github.com/your-username/hugo-theme-amigo.git themes/Amigo
```

### 2. Configuration

You can copy the `hugo.toml` from the theme directory to your site root, or add these settings to your existing `hugo.toml`:

```toml
theme = "Amigo"

[params]
  # --- Profile Info ---
  username = "Your Name"
  avatar = "/images/avatar.jpg"
  description = "Life is dull, but running makes wind."
  cover = "/images/header.png" # Homepage cover image
  
  # If you want to use a video cover, comment out 'cover' and use this:
  # headerMedia = "/videos/cover-video.mp4"
  
  # --- Comment System ---
  # Pick one you like: "artalk", "twikoo", "giscus" or "none" (disable all)
  commentMode = "artalk"

  # --- Font Settings ---
  # Options: "ZQL" (Handwritten), "Harmony" (HarmonyOS), "System" (Default)
  fontFamily = "ZQL"

  # --- Feature Toggles ---
  showTags = true         # Show post tags
  showLocation = true     # Show location info
  enableSearch = true     # Enable search function
  enableDarkMode = true   # Show dark mode toggle button
  enablePjax = true       # Enable PJAX (seamless page switching)
  enableLightbox = true   # Enable image lightbox (click to zoom)

  # --- Artalk Config ---
  artalkServer  = "https://your-artalk-server.com"
  artalkSite    = "MyBlog"
  enableDanmaku = true   # Enable bottom danmaku

  # --- Twikoo Config (Choose one) ---
  # Plan A: Tencent Cloud
  # twikooEnvId = "your-env-id"
  # Plan B: Vercel / Self-hosted
  # twikooEnvId = "https://twikoo.your-domain.com"
  twikooLang = "zh-CN"

  # --- Giscus Config ---
  # Generate from https://giscus.app and fill in here
  giscusRepo             = ""
  giscusRepoId           = ""
  giscusCategory         = ""
  giscusCategoryId       = ""
  giscusMapping          = "pathname"
  giscusStrict           = "0"
  giscusReactionsEnabled = "1"
  giscusEmitMetadata     = "0"
  giscusInputPosition    = "bottom"
  giscusLang             = "zh-CN"
  giscusLoading          = "lazy"
```

#### Navigation Menu

Setting up the menu is also simple. Add this to your `hugo.toml`:

```toml
[[menu.main]]
  name = "Home"
  url  = "/"
  weight = 1

[[menu.main]]
  name = "About"
  url  = "/about.html"
  weight = 2

[[menu.main]]
  name = "Friends"
  url  = "/friends.html"
  weight = 3
```

## 📝 Usage Guide

### Directory Structure

It is recommended to organize your files like this for better image management:

```text
content/
├── posts/           # Moments/Updates
│   ├── 2024/
│   │   └── my-trip/
│   │       ├── index.md    # Article content
│   │       ├── photo1.jpg  # Image
│   │       └── photo2.jpg
├── about.md         # About page
└── friends.md       # Friends page
```

### Writing Updates (Posts)

You can create a folder (Page Bundle) or a standalone `.md` file under `content/posts/`.  
For better image management, it is recommended to create a folder per post:

```markdown
---
title: "Hiking Weekend"
date: 2024-03-20
author: "Vaica"
location: "Wuhan · East Lake"
---

The weather is amazing today! Went for a walk in the park and saw so many flowers blooming. 🌸

![Spring 1](photo1.jpg)
![Spring 2](photo2.jpg)

#Life #Spring #Photography
```

### Useful Shortcodes

Amigo comes with some handy shortcodes to help you insert special content easily.

#### Live Photo

Want to show off those "Live Photo" moments like on iPhone? Use this:

```markdown
{{< livephoto image="cover.jpg" video="video.mp4" >}}
```

- `image`: The cover image (shown when static)
- `video`: The video file (shown when playing)

> 💡 Pro Tip: If your image and video share the same filename (e.g., `my-cat.jpg` and `my-cat.mp4`), you can just write `image="my-cat.jpg"`, and the theme will automatically find the matching video.

---

## 🚀 Deployment (GitHub Actions)

### Friends Configuration

Create `data/friends.yml` in your site's root directory:

```yaml
- name: "Vaica"
  url: "https://usj.cc"
  avatar: "https://github.com/vaica.png"
  description: "Developer"
```

Then in `content/friends.md`:

```markdown
---
title: "Friends"
layout: "friends"
---
```

## 🛠️ Tech Stack

- **SSG**: [Hugo](https://gohugo.io/)
- **Icons**: [Remix Icon](https://remixicon.com/)
- **Comments**: [Artalk](https://artalk.js.org/) / [Twikoo](https://twikoo.js.org/) / [Giscus](https://giscus.app/)
- **JS Components**: ViewImage.js, PJAX.js

## ☕ Buy Me a Coffee

If you like this theme, you can buy me a coffee to support future development!

<table>
  <tr>
    <td align="center">
      <img src="static/images/wechat-pay.jpg" alt="WeChat Pay" width="200" /><br />
      WeChat Pay
    </td>
    <td align="center">
      <img src="static/images/alipay.jpg" alt="Alipay" width="200" /><br />
      Alipay
    </td>
  </tr>
</table>

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

Thanks for using **Amigo**! If you like this project, please give it a **Star** ⭐️.
