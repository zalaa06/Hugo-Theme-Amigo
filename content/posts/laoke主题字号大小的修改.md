---
title: "laoke主题字号大小的修改"
date: 2026-04-15
lastmod: 2026-04-15
draft: false
author: "AI"
---
# **laoke主题字号大小的修改**
通过修改base.css 来改变字体的大小
你这段代码里，正文默认字号就是这里控制的：

```css
p,
ul,
ol,
table,
th,
td,
input,
textarea,
button {
    font-size: 13px;
    line-height: 26px;
    font-weight: 300;
    letter-spacing: 0.6px;
}
```

---

## 一、你现在为什么正文是 13px
因为这段意思是：

- 段落 `p`
- 列表 `ul / ol`
- 表格 `table / th / td`
- 输入框等

都统一用：

```css
font-size: 13px;
line-height: 26px;
```

所以文章正文看起来偏小。

---

## 二、最直接的改法
你可以直接把这里的 `13px` 改大一点，比如改成 `16px` 或 `17px`：

```css
p,
ul,
ol,
table,
th,
td,
input,
textarea,
button {
    font-size: 16px;
    line-height: 28px;
    font-weight: 300;
    letter-spacing: 0.6px;
}
```

---

## 三、但我不太推荐直接这样改
因为这样会把这些东西**全部一起改大**：

- 正文段落
- 列表
- 表格
- 输入框
- 评论框按钮文字

也就是说不只是文章正文，**评论区、表单、别的模块**也可能跟着变。

---

## 四、推荐做法：只改文章正文
更稳的方法是：  
**保留全局默认 13px 不动**，单独给文章内容 `.post-content` 设置字号。

你可以在这个 CSS 文件后面加：

```css
.post-content {
    font-size: 18px;
    line-height: 1.9;
}

.post-content p,
.post-content ul,
.post-content ol,
.post-content li,
.post-content table,
.post-content th,
.post-content td,
.post-content blockquote {
    font-size: inherit;
    line-height: inherit;
}
```

---

## 五、为什么这样更好
因为你这个主题明显是：

- 全局基础文字：13px
- 某些模块再单独覆盖

所以你如果只想改**文章页正文**，最好只动 `.post-content`，这样：

- 首页摘要不一定乱
- 评论区不会跟着变
- 按钮不会变
- 文章内容阅读体验会更好

---

## 六、你应该怎么操作
### 方案 A：只想改正文，推荐这个
直接把下面这段加到 CSS 文件最后：

```css
.post-content {
    font-size: 18px;
    line-height: 1.9;
}

.post-content p,
.post-content ul,
.post-content ol,
.post-content li,
.post-content table,
.post-content th,
.post-content td,
.post-content blockquote {
    font-size: inherit;
    line-height: inherit;
}
```

如果手机端也想小一点，再加：

```css
@media (max-width: 720px) {
    .post-content {
        font-size: 16px;
        line-height: 1.85;
    }
}
```

---

### 方案 B：全站基础文字都变大
如果你就是想让整个站的普通文字都变大，那就改这段：

```css
p,
ul,
ol,
table,
th,
td,
input,
textarea,
button {
    font-size: 13px;
    line-height: 26px;
    font-weight: 300;
    letter-spacing: 0.6px;
}
```

改成比如：

```css
p,
ul,
ol,
table,
th,
td,
input,
textarea,
button {
    font-size: 15px;
    line-height: 28px;
    font-weight: 300;
    letter-spacing: 0.6px;
}
```

但这个会影响范围很大。

---

## 七、我建议你用哪种
如果你问的是**“正文的字号大小怎么改”**，  
那我建议你用这个最终版，**直接追加到文件最后**：

```css
.post-content {
    font-size: 18px;
    line-height: 1.9;
}

.post-content p,
.post-content ul,
.post-content ol,
.post-content li,
.post-content table,
.post-content th,
.post-content td,
.post-content blockquote {
    font-size: inherit;
    line-height: inherit;
}

@media (max-width: 720px) {
    .post-content {
        font-size: 16px;
        line-height: 1.85;
    }
}
```

---

## 八、补充一点
你这份全局 CSS 里已经有：

```css
h1, h2, h3, h4, h5, h6
```

和正文标题大小在另一个 CSS 片段里又单独设置了 `.post-content h1`、`.post-content h2`，  
所以**正文标题你不用在这里改**，只需要在这里补 `.post-content` 正文字号即可。

---

如果你愿意，我可以直接帮你整理成一份：  
**“复制到你当前 CSS 文件最后就能用”的完整代码**，并且按你这个页面风格给你调成和示例网页很接近的字号。
