---
title: "typecho主题引入oppo sans字体"
date: 2026-04-15T15:02:34+08:00
lastmod: 2026-04-15T15:02:34+08:00
draft: false
author: "AI"
---

# **typecho主题laoke引入oppo sans字体**

可以，直接把你那段改成用这个 CDN 链接就行了，像这样放在 `header.php` 的 `<head>` 里：

```
<style>
@font-face {
    font-family: 'OPPOSans';
    src: url('https://code.oppo.com/content/dam/oppo/common/fonts/font2/new-font/OPPOSansOS2-5000-Regular.woff2') format('woff2');
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

body {
    font-family: 'OPPOSans', sans-serif;
}
</style>
```

这样就直接用官方 WOFF2 了，不需要本地文件 👍

如果你后面还想加粗（bold）或其他字重，需要再补多个 `@font-face`，不然加粗时可能会“假粗”。
