# 首页常用网站和最近打开格子视觉优化设计规格书

本文档描述了将首页的“常用网站”和“最近打开”卡片重构为“实体悬浮卡片风（Elevated Solid Card）”的设计和实现细节。

## 1. 目标与背景

当前首页在开启 `has-scenic-bg` 时，卡片使用了高透明度且无阴影的样式（如 `rgba(255, 255, 255, 0.4)`）。这导致卡片与毛玻璃背景面板（`.panel`，透明度为 `0.65`）混合在一起，缺乏视觉边界与层次感，使得书签看起来不像是独立的“格子”。

本优化方案通过增强卡片底色不透明度、引入常驻阴影、微调边框色值与悬停提升效果，使卡片脱颖而出，呈现精致的实体悬浮质感。

## 2. 设计细节

### 2.1 常用网站卡片 (`.favorite-card`)

在开启 `has-scenic-bg` 时：

*   **常规状态 (Normal State)**：
    *   **亮色模式**：
        *   `background`: `rgba(255, 255, 255, 0.9)` (提升亮度，增加实色感)
        *   `border`: `1px solid rgba(255, 255, 255, 0.6)`
        *   `box-shadow`: `0 4px 12px rgba(15, 23, 42, 0.03)`
    *   **深色模式** (`:root[data-theme='dark']`)：
        *   `background`: `rgba(20, 24, 33, 0.85)`
        *   `border`: `1px solid rgba(255, 255, 255, 0.05)`
        *   `box-shadow`: `0 4px 12px rgba(0, 0, 0, 0.3)`
    *   **平滑过渡 (Transition)**：
        *   `transition`: `all 0.25s cubic-bezier(0.16, 1, 0.3, 1)`
        *   `transform`: `translateY(0)`

*   **悬停状态 (Hover State)**：
    *   `transform`: `translateY(-4px)` (增强上浮感)
    *   **亮色模式**：
        *   `background`: `#ffffff`
        *   `border-color`: `var(--primary)`
        *   `box-shadow`: `0 12px 24px rgba(15, 23, 42, 0.08)`
    *   **深色模式**：
        *   `background`: `rgba(30, 41, 59, 0.95)`
        *   `border-color`: `var(--primary)`
        *   `box-shadow`: `0 12px 24px rgba(0, 0, 0, 0.5)`

### 2.2 添加网站卡片 (`.favorite-add`)

*   **常规状态 (Normal State)**：
    *   **亮色模式**：
        *   `background`: `rgba(255, 255, 255, 0.5)`
        *   `border`: `1.5px dashed rgba(15, 23, 42, 0.15)`
        *   `box-shadow`: `none`
    *   **深色模式**：
        *   `background`: `rgba(15, 23, 42, 0.2)`
        *   `border`: `1.5px dashed rgba(255, 255, 255, 0.1)`
        *   `box-shadow`: `none`
*   **悬停状态 (Hover State)**：
    *   `transform`: `translateY(-2px)`
    *   `border-color`: `var(--primary)`
    *   `color`: `var(--primary)`
    *   **亮色模式**：
        *   `background`: `#ffffff`
        *   `box-shadow`: `0 4px 12px rgba(15, 23, 42, 0.04)`
    *   **深色模式**：
        *   `background`: `rgba(30, 41, 59, 0.9)`
        *   `box-shadow`: `0 4px 12px rgba(0, 0, 0, 0.3)`

### 2.3 最近打开卡片 (`.recent-card`)

在开启 `has-scenic-bg` 时：

*   **常规状态 (Normal State)**：
    *   **亮色模式**：
        *   `background`: `rgba(255, 255, 255, 0.85)`
        *   `border`: `1px solid rgba(255, 255, 255, 0.5)`
        *   `box-shadow`: `0 2px 8px rgba(15, 23, 42, 0.02)`
    *   **深色模式**：
        *   `background`: `rgba(20, 24, 33, 0.8)`
        *   `border`: `1px solid rgba(255, 255, 255, 0.04)`
        *   `box-shadow`: `0 2px 8px rgba(0, 0, 0, 0.2)`
    *   `transition`: `all 0.25s cubic-bezier(0.16, 1, 0.3, 1)`
    *   `transform`: `translateY(0)`

*   **悬停状态 (Hover State)**：
    *   `transform`: `translateY(-2px)`
    *   `border-color`: `var(--primary)`
    *   **亮色模式**：
        *   `background`: `#ffffff`
        *   `box-shadow`: `0 6px 16px rgba(15, 23, 42, 0.06)`
    *   **深色模式**：
        *   `background`: `rgba(30, 41, 59, 0.9)`
        *   `box-shadow`: `0 6px 16px rgba(0, 0, 0, 0.4)`

### 2.4 其他微调

*   **平滑阴影转换**：确保常用卡片和最近打开卡片都包含对 `box-shadow` 的 transition。
*   **拖拽中状态 (`.dragging` 和 `.drag-over`)**：
    *   拖拽中卡片 `.dragging` 的阴影设为 `none`，不透明度微调。
    *   拖拽划过卡片 `.drag-over` 的过渡应当平滑，使用合适的边框和阴影。

## 3. 修改文件范围

*   [newtab.css](file:///Users/quentin/Documents/me/code-open/bookmark-ai/entrypoints/newtab/newtab.css)

## 4. 验证方案

### 4.1 自动测试与构建
- 执行开发环境打包，确认无编译错误。

### 4.2 手动验证
- 启动本地开发服务，并在浏览器中打开新标签页。
- 观察“常用网站”的卡片是否呈现实体卡片视觉，是否有阴影浮起。
- 悬停在其上，检查是否产生平滑上浮动画、阴影加深、边框颜色过渡。
- 观察“最近打开”的卡片是否同样有独立格子的高级质感。
- 分别切换至系统**深色模式**和**亮色模式**，确认其对比度和显示效果是否均合理且精美。
