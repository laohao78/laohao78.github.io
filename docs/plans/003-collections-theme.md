# Phase 5/6 计划：Collections 架构 + 主题切换系统

## 背景

Phase 4 的 `category` + `for+if` 方案扩展性差：加栏目要改 5+ 个文件，CSS 变量视觉切换因 SCSS 函数不兼容而回退。

## 方案

### 内容层：Jekyll Collections

```
_silicon/  ← 硅基文明 9 篇
_xinzhi/   ← 新质生产力 1 篇
```

替代 `_posts/` 混合 + `category` 过滤。`site.silicon` / `site.xinzhi` 直接访问。

### 视觉层：CSS 变量双重体系

**编译期**：SCSS 变量供 `lighten()`/`darken()`/`rgba()` 使用
**运行时**：CSS 自定义属性（内联 `<style>`，绕过 SCSS）供主题切换

```
_sass/_variables.scss     ← SCSS 变量（$bg, $gold-rgba-02 ...）
_includes/theme-vars.html ← CSS 变量（:root + body.theme-light）
_includes/theme-script.html ← JS 切换逻辑 + localStorage
```

### 主题切换

- `:root` 默认深色（星空史书）
- `body.theme-light` 覆盖浅色（白皮书）
- 导航栏 ◐ 按钮，localStorage 记忆
- 扩展只需在 `theme-vars.html` 加 `.theme-XXX` 块

## 关键风险

- SCSS 函数（`lighten`/`darken`/`rgba`）不能传 CSS 变量——必须用双轨策略
- `_` 前缀目录被 Jekyll 忽略——collection 目录用 `_`，自定义目录用 `pages/`
- `:root` 默认变量不可省略——JS 延迟时页面仍有正常外观

## 执行结果

已完成。详见 CHANGELOG Phase 5/6。
