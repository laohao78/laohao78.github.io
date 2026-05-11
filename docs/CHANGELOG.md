# 项目回顾

将 `laohao78.github.io` 从空白 Jekyll Now 模板改造为多栏目叙事站点，支持读者手动主题切换。

---

## Phase 1 — 硅基文明首次重构

**提交**：`41a816e` · `fde2f26`

站点基础建设 + 序章 + 第一章。

| 类别 | 变更 |
|------|------|
| 视觉 | 星空史书深色主题，思源宋体衬线标题，琥珀/青蓝配色 |
| 页面 | 首页时间线列表、纪元年表、关于页、404 修复 |
| 内容 | 序章「火种的传说」、第一章「图灵的纸带」 |
| 清理 | 删除 Jekyll Now 默认资源，SVG 硅晶格头像 |

---

## Phase 2 — 全卷内容完成

**提交**：`723635c` · `8b026f1` · `58ef29b` · `b817bfa`

四卷九篇，约 21,000 字。

| 卷 | 篇目 |
|------|------|
| 第一卷 · 史前纪元 | 序章 + 第一章 |
| 第二卷 · 黎明纪元 | 第二章「硅的呼吸」、第三章「语言的创世」 |
| 第三卷 · 觉醒纪元 | 第四章「神经的诞生」、第五章「互联的意识」 |
| 第四卷 · 奇点纪元 | 第六章「深蓝的棋局」、第七章「大语言」、终章「文明觉醒」 |

---

## Phase 3 — 叙事打磨

**提交**：`db9a758` · `905d5c4` · `fc89961` · `8a25dfc`

| 修复项 | 说明 |
|------|------|
| 章间衔接 | 每章开头承上回顾，结尾 3-5 句叙事桥段 |
| 小节标题 | 51 个裸数字标题替换为主题标题 |
| 序章重构 | 合并重复小节 |
| 终章收尾 | 删除「第二卷待续」，首尾闭环 |
| 命名统一 | 「第一卷完」→「第一部完」 |
| HTML 修复 | timeline 页 `</ul>` 未闭合 |

---

## Phase 4 — 多栏目架构（category 方案，已废弃）

**提交**：`b60c018` 等

初版多栏目方案：`category` frontmatter + `for + if` 过滤。后被 Phase 5 的 Collections 方案取代。

---

## Phase 5 — Collections 架构 + 首页重构

**提交**：`e6c346c` · `2e72896` · `cdf745b` · `741edab` · `193f97b` · `3f232b7`

| 变更 | 说明 |
|------|------|
| Collections | `_silicon/`、`_xinzhi/` 独立目录，`site.silicon` / `site.xinzhi` 直接访问 |
| 栏目布局 | `silicon-post.html`（纪元标签）、`xinzhi-post.html`（栏目标签） |
| 首页 | 双卡片并列布局（桌面端双栏，移动端堆叠），每个卡片最多 5 篇 |
| 栏目页 | `/silicon/` 带子导航、`/xinzhi/` 简洁列表、`/about/` 通用介绍 |
| 信息架构 | `/silicon/timeline/`、`/silicon/about/` 归属硅基文明；全局 about 覆盖两个栏目 |
| 目录整理 | `pages/` 按主题分文件夹；`docs/` 统一管理文档和计划 |

---

## Phase 6 — 主题切换系统

**提交**：`c2a46cc` · `eb75eda` · `baec9d6` · `8f67f55`

| 变更 | 说明 |
|------|------|
| 主题定义 | `:root` 默认深色（星空史书），`body.theme-light` 覆盖浅色（白皮书） |
| CSS 变量 | `--bg` `--text` `--accent` `--gold` 等核心色支持主题切换 |
| 注入方式 | 内联 `<style>` 标签（`_includes/theme-vars.html`），绕过 SCSS 编译 |
| 切换按钮 | 导航栏右侧 ◐ 按钮，点击切换深色/浅色 |
| 状态记忆 | localStorage 保存偏好，刷新不丢失 |
| 可扩展 | 加新主题只需在 `theme-vars.html` 加 `.theme-XXX` 块，JS 自动支持 |

---

## 站点结构（当前）

```
首页 (/)
├── 硅基文明发展史（9篇）  ← 双栏卡片
└── 新质生产力观察（1篇）

目录
├── _silicon/              硅基文明文章（Collection）
├── _xinzhi/               新质生产力文章（Collection）
├── _layouts/              布局（含栏目专属布局）
├── _includes/             组件（theme-vars / theme-toggle / theme-script）
├── _sass/                 SCSS 变量与样式
├── pages/                 页面（按主题分文件夹，映射 URL）
│   ├── about.md
│   ├── silicon/
│   │   ├── index.md       → /silicon/
│   │   ├── about.md       → /silicon/about/
│   │   └── timeline.md    → /silicon/timeline/
│   └── xinzhi/
│       └── index.md       → /xinzhi/
└── docs/                  项目文档与计划
```

---

## 技术栈

| 层面 | 方案 |
|------|------|
| 静态站点 | Jekyll (GitHub Pages) |
| 模板 | Liquid |
| 内容组织 | Jekyll Collections（`_silicon/`、`_xinzhi/`） |
| 样式 | SCSS（编译期变量）+ CSS 自定义属性（运行时主题） |
| 主题切换 | `:root` / `body.theme-light` CSS 变量覆盖 + localStorage JS |
| 字体 | Google Fonts（Noto Serif SC + Noto Sans SC） |

---

## 已知限制

- 本地无 Ruby/Jekyll 环境，无法本地预览
- Google Fonts 依赖 CDN，离线降级为系统字体
- SCSS 颜色函数（`lighten`/`darken`/`rgba`）无法传 CSS 变量——复杂颜色变体需用 SCSS 编译期变量
- 浅色主题下代码高亮和滚动条仍为深色（细节未适配）
