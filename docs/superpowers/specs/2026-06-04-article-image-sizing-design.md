# 文章图片尺寸优化设计

日期：2026-06-04

## 问题

桌面端文章内容中的图片以 `max-width: 100%` 展示，在大屏幕上图片撑满整个内容区，视觉上过大，破坏阅读节奏。

## 目标

- 桌面端图片默认缩至内容区 80% 宽度，留白产生呼吸感
- 移动端保持 100%，小屏寸土寸金
- hover 微放大 + 手型光标，暗示图片可点击（Fancybox 灯箱已启用）
- hover 效果仅桌面端生效，触屏设备无意义

## 方案

纯 CSS 改动，仅修改一个文件：`themes/anzhiyu/source/css/_layout/post.styl`

### 改动内容

将第 74-79 行：

```stylus
#article-container
  img
    display: block
    margin: 0 auto 20px
    max-width: 100%
    transition: .3s
    border-radius: 8px
```

替换为：

```stylus
#article-container
  img
    display: block
    margin: 0 auto 20px
    max-width: 80%
    transition: .3s
    border-radius: 8px
    cursor: pointer

    &:hover
      transform: scale(1.02)

    +maxWidth768()
      max-width: 100%
      &:hover
        transform: none
        cursor: default
```

### 断点逻辑

| 视口宽度 | max-width | hover 效果 |
|----------|-----------|------------|
| > 768px（桌面） | 80% | scale(1.02) + cursor: pointer |
| ≤ 768px（移动） | 100% | 无 |

使用主题已有的 `+maxWidth768()` mixin（定义于 `_global/function.styl:109`）。

### 影响范围

- 所有 `#article-container` 内的 `<img>` 标签，包括 Markdown `![](url)` 生成的图片
- `{% image %}` 标签插件生成的 `.img` 元素不受影响（选择器不同）
- `{% gallery %}` 生成的画廊图片不受影响（独立样式）
- `{% inlineImg %}` 内联小图不受影响（`display: inline`，不走 max-width）
- Fancybox 灯箱行为不变，点击仍弹出全尺寸大图

### 不做的事

- 不改 JS、不改模板、不加构建时图片处理
- 不加 srcset/WebP（CDN 外链，不在本次范围）
- 不改 `{% image %}` / `{% gallery %}` / `{% inlineImg %}` 的现有行为
