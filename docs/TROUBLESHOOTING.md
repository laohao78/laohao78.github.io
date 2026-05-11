# 踩坑记录

无本地 Jekyll 环境，所有问题通过 GitHub Pages 线上构建日志排查。以下按时间顺序记录。

---

## 1. 头像图片加载失败

**现象**：站点头图不显示。

**原因**：`_config.yml` 中 avatar 使用了 raw.githubusercontent.com 的绝对 URL，路径中包含分支名 `/main/`，但仓库默认分支是 `master`。

**修复**：改用站点相对路径 `/images/avatar.svg`，不依赖分支名。

---

## 2. 纪元年表页面出现裸露的 `</div>` 文本

**现象**：`/timeline` 页面上出现多余的 `</div>` 标签被当作纯文本渲染。

**原因**：Liquid 模板在切换卷时只输出了 `</div>` 关闭外层容器，忘记先 `</ul>` 关闭内部列表。HTML 结构不匹配导致浏览器将多余闭合标签视为文本节点。

**修复**：将 `{% unless forloop.first %}</div>{% endunless %}` 改为 `</ul></div>`，同理修正最后的闭合逻辑。

---

## 3. SCSS 自带函数无法处理 CSS 自定义属性（核心故障）

**现象**：GitHub Pages 构建在 "Build with Jekyll" 步骤 2 秒内失败，站点回退到上次成功构建的旧版本。本地无法复现。

**原因**：将 SCSS 变量（`$bg-primary` 等）迁移为 CSS 自定义属性（`var(--bg-primary)` 等）后，以下 SCSS 函数在编译期无法处理运行时 CSS 变量：

| 函数 | 问题代码 | 说明 |
|------|----------|------|
| `lighten()` | `lighten(var(--accent), 15%)` | SCSS 需要编译期颜色值 |
| `darken()` | `darken(var(--bg-secondary), 2%)` | 同上 |
| `rgba()` | `rgba(var(--gold), 0.2)` | hex 值包装在 `var()` 中无法被解析 |

Libsass（GitHub Pages 使用的 SCSS 编译器）在编译期执行这些函数，此时 CSS 自定义属性还是未解析的字符串，导致编译报错。

**修复**：
- `lighten(var(--x), N%)` → 直接用 `var(--x)`
- `darken(var(--x), N%)` → 用更深的备用变量
- `rgba(var(--x), alpha)` → 定义 RGB 分量变量 `--x-rgb: R, G, B`，改为 `rgba(var(--x-rgb), alpha)`

**教训**：CSS 自定义属性与 SCSS 颜色函数不兼容。如果要用 CSS 变量做主题切换，要么 (a) 放弃 SCSS 颜色函数，所有颜色用硬编码或备用变量处理；(b) 保持 SCSS 变量做编译期主题，用不同 CSS 文件做主题切换。

**最终方案**：回退到纯 SCSS 变量方案。多栏目共用同一套 SCSS 变量（深色主题），暂未实现栏目级视觉切换。

---

## 4. Liquid `where` 过滤器在 GitHub Pages 中可能不被支持

**现象**：首页使用 `site.posts | where: "category", "硅基文明"` 过滤文章，构建可能失败。

**原因**：`where` 过滤器在 Jekyll 3.2+ 中可用，但 GitHub Pages 的安全构建模式对自定义 frontmatter 字段的过滤支持不完整。

**修复**：放弃 `where` 和 `where_exp`，改用 `for` 循环内嵌 `if` 条件判断：

```liquid
{% for post in site.posts %}
  {% if post.category == "硅基文明" %}
    ...
  {% endif %}
{% endfor %}
```

---

## 5. Liquid `reversed` 关键字不生效

**现象**：硅基文明栏目页文章列表是倒序（终章在前），加了 `reversed` 后仍不生效。

**原因**：GitHub Pages 的 Liquid 环境中 `reversed` 关键字对 `site.posts` 的处理存在兼容问题。

**修复**：改用显式排序 `{% assign posts = site.posts | sort: "date" %}`，升序排列即正序。

---

## 6. `_pages` 目录被 Jekyll 忽略导致全站 404

**现象**：将页面文件移入 `_pages/` 目录后，所有页面（`/about/`、`/silicon/`、`/timeline/` 等）全部 404。

**原因**：Jekyll 默认忽略 `_` 开头的自定义目录。只有白名单目录（`_posts`、`_layouts`、`_includes`、`_sass`、`_data` 等）会被处理。`_pages` 不在白名单中，所有文件被跳过。

**修复**：重命名为 `pages/`（无下划线前缀）。

**教训**：Jekyll 目录命名规则——自定义目录不要用 `_` 或 `.` 前缀。

---

---

## 7. `rgba(var(--x), alpha)` 也会导致 SCSS 编译失败

**现象**：用 `rgba(var(--gold-rgb), 0.2)` 的方式引用 CSS 变量作为颜色分量，构建失败。

**原因**：SCSS 的 `rgba()` 函数同样在编译期解析颜色参数。`var(--gold-rgb)` 不是有效的编译期颜色值，报错。

**修复**：预计算所有 alpha 变体为独立 CSS 变量：
```css
:root { --gold-rgba-02: rgba(201,168,76,0.2); }
```
然后在 SCSS 中用 `var(--gold-rgba-02)` 引用。或者保持 SCSS 编译期变量 `$gold-rgba-02`。

**教训**：**任何** SCSS 颜色函数（`lighten`、`darken`、`rgba`、`saturate` 等）传 CSS 变量都会报错。唯一安全的用法是 `var()` 作为简单属性值，不被任何 SCSS 函数包裹。

---

## 8. CSS 变量作用域丢失——`:root` 默认值不可省略

**现象**：所有使用 `var(--bg-masthead)` 等 CSS 变量的组件显示为黑色/透明，浅色主题切换后 masthead 和 footer 不变色。

**原因**：将 CSS 变量定义从 `:root` 移到 `.theme-dark` 和 `.theme-light` 后，如果 JS 未运行或运行延迟，body 上没有 theme class，所有 CSS 变量都是未定义状态。

**修复**：`:root` 始终保留默认值（深色主题），`.theme-light` 仅覆盖需要变化的变量：
```css
:root { --bg: #0a0a0f; --bg-masthead: rgba(10,10,15,0.92); }
body.theme-light { --bg: #f8f6f0; --bg-masthead: rgba(248,246,240,0.92); }
```

---

## 9. CSS 自定义属性需绕过 SCSS 编译管线

**现象**：在 `_sass/_variables.scss` 中使用 `:root { --bg: #0a0a0f; }` 定义 CSS 变量，构建失败。

**原因**：`_sass/` 下的文件全部经由 libsass 编译器处理。libsass 在处理 `:root` 块中的 CSS 变量时，可能与后续的 `@mixin` 或 SCSS 变量产生不可预见的冲突。具体错误日志无法获取（需要仓库 admin 权限）。

**修复**：将 CSS 变量定义移到 `_includes/theme-vars.html` 中的内联 `<style>` 标签，在布局 `<head>` 中通过 `{% include %}` 注入。这是纯 HTML/CSS，Jekyll 直接输出，不经过 SCSS 编译器。

**最终架构**：
- `_sass/_variables.scss` — 纯 SCSS 变量（`$bg`, `$gold-rgba-02` 等），供 `lighten`/`darken`/`rgba` 等 SCSS 函数使用
- `_includes/theme-vars.html` — CSS 自定义属性（内联 `<style>`），定义 `:root` 和 `body.theme-*` 的主题变量
- `style.scss` — 混合使用：简单颜色用 `var(--x)`，SCSS 函数用 `$x`

---

## 经验总结

| 原则 | 说明 |
|------|------|
| 无本地环境时，每次推送前确认上一次构建成功 | 否则会积累多层问题难以定位 |
| CSS 自定义属性 ≠ SCSS 变量 | 可以共存于同一文件但不能交叉使用 SCSS 函数 |
| `var()` 只能作为简单属性值 | 不能传给 `lighten`/`darken`/`rgba`/任何 SCSS 函数 |
| `:root` 作为 CSS 变量默认值不可省略 | 确保 JS 未运行时页面也有正常外观 |
| CSS 变量定义绕开 SCSS | 放在内联 `<style>` 或独立 `.css` 文件中，不放在 `_sass/` 下 |
| Liquid 兼容性优先 | `where`、`reversed` 等"高级"过滤器不可靠，`if` + `sort` 最稳妥 |
| 目录命名 | Jekyll 中 `_` 前缀是特权符号，自定义目录一律不用 |
| Collections 优于 category | 用 Jekyll Collections 组织多栏目，比 `category` + `for+if` 更干净 |
