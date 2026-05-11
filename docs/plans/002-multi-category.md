# 站点多栏目架构改造计划

## 背景

硅基文明编年史完成后，站点需要支持多个独立栏目（首先加入「新质生产力」），每个栏目有独立的视觉系统和内容形态。

## 设计方案

### 栏目划分

使用 Jekyll `category` frontmatter 区分栏目：

- 硅基文明（9篇已有）—— 星空史书深色主题
- 新质生产力（待建）—— 独立白皮书视觉体系

### 视觉切换

CSS 自定义属性 + body class 实现栏目级视觉切换：

```scss
:root { --bg: #0a0a0f; --text: #d4c8a8; }       // 硅基文明默认
body.category-新质生产力 { --bg: #f8f6f0; ... }   // 覆盖为暖白主题
```

### 实施内容

1. SCSS 变量 → CSS 自定义属性迁移
2. 所有 9 篇文章添加 `category: 硅基文明`
3. `_layouts/default.html` body 加入 category class，导航增加栏目入口
4. 首页按栏目分组展示
5. 新质生产力第一篇文章

## 执行状态

已完成。详见 CHANGELOG.md Phase 4。
