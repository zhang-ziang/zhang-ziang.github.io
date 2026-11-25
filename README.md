# Viglong 的海岸线 · Blowfish 版

从 **Hexo 6 + flex-block** 迁移到 **Hugo + Blowfish v3**。原 Hexo 站点完整保留在 `../Hexo`，随时可回滚。

## 这次迁移守住了什么

| 项目 | 状态 |
| --- | --- |
| 文章 | 37 篇全部转换，其中 36 篇已发布、1 篇设为草稿 |
| URL | 每篇 front matter 写死 `url`，36 篇已发布页面与旧站**逐字一致** |
| 题图 | 33 篇沿用原 `cover`，改为 `featureimage`；4 篇无 cover 的回退到站点 banner |
| 首页 hero | `/image/loundraw1.png`（与旧站同图） |
| 评论 | Valine，appId/appKey 不变，`path: window.location.pathname` |
| 页面 | 关于 `/about/`、友链 `/links/`、留言板 `/message/` 均保留原 URL |

校验脚本产出的对照清单：`../_migration/verify.txt`、`../_migration/url-snapshot.txt`。

## 为什么 URL 必须写死

Valine 用 `window.location.pathname` 当评论的 key。Hexo 的 permalink `:title` 取的是**文件名**（不是 front matter 的 title），而 Hugo 的 `:title` 取的是标题，两者语义相反；再加上 Hugo 默认会把路径小写化（`AI随想` 会变成 `ai随想`）。任何一处走偏，旧评论就对不上号。

所以这里不依赖 token 语义，直接在每篇的 front matter 写死：

```yaml
url: "/2024/05/24/20240523整理整理吧/"
```

## 目录结构

```
blowfish-site/
├─ config/_default/
│  ├─ hugo.toml            # baseURL / 语言 / permalinks / 分类法
│  ├─ params.toml          # 主题参数：hero、卡片、评论开关
│  ├─ languages.zh-cn.toml # 站点标题、作者、社交链接
│  ├─ menus.zh-cn.toml     # 顶栏与页脚菜单
│  └─ markup.toml          # goldmark / 代码高亮 / TOC
├─ content/
│  ├─ posts/               # 37 篇文章
│  ├─ about/ links/ message/
├─ assets/image/           # 41 张图（Blowfish 从 assets 取图，不是 static）
├─ static/                 # favicon 系列
├─ layouts/partials/comments.html   # Valine 挂载点
└─ themes/blowfish/        # git submodule
```

> 注意：Blowfish 的 hero 通过 `resources.Get` 取图，只认 `assets/`。放在 `static/` 里图片不会显示。

## 本地预览

```powershell
hugo server -D
# 打开 http://localhost:1313/
```

Hugo 需要 **extended** 版。若没装：

```powershell
winget install Hugo.Hugo.Extended
```

## 部署

推送到 GitHub 的 `main` 分支后，`.github/workflows/deploy.yml` 会构建并推 `public/` 到 **gh-pages** 分支——与旧 Hexo 的部署方式保持一致，GitHub Pages 的源不用改。

需要确认的仓库信息：

- 仓库：`zhang-ziang/zhang-ziang.github.io`
- 分支：`gh-pages`
- 旧 Hexo 配置里写的 `git@github.com:zhang-ziang/github.io.git` 是重命名前的旧名，靠 GitHub 的 redirect 还能用，建议顺手改掉

## 与旧站的差异

| 旧站 | 新站 | 影响 |
| --- | --- | --- |
| `/atom.xml` | `/index.xml`（`/atom.xml` 也已保留） | 无影响：已通过自定义 output format 生成 Atom 格式的 `/atom.xml`，旧订阅地址继续可用 |
| `/search/` 独立页面 | 顶栏搜索图标（⌘K / Ctrl+K） | 旧 URL 不再存在 |
| 首页列表 `/page/2/` | 全部文章在 `/posts/`，分页在 `/posts/page/2/` | 旧分页链接失效 |
| 无归档页 | `/posts/` 按年份分组；`/archives/` 通过 alias 重定向到 `/posts/` | 旧链接仍可用 |

## 部署状态

- 2026-11-25 已推送至 `main` 并通过 GitHub Actions 发布到 `gh-pages`。
- 线上验证：首页、文章页、`/atom.xml`、`/index.xml`、`/about/`、`/links/`、`/message/`、`/archives/` 全部 200。

## 待办

- [ ] 第 37 篇 `20240709准研究生.md` 目前是 `draft: true`（旧站从未发布过它，按要求保持草稿）。要发布就删掉那行，再 `hugo server -D` 确认。
- [ ] **Valine 连通性待确认**：从外部查 LeanCloud，应用本身存活（`/1.1/date` 返回 200），但 `Comment` 类返回 401。可能是类权限限制，也可能是 AppKey 已轮换。请在浏览器打开任意老文章，看评论区有没有内容；若为空，去 LeanCloud 控制台复制当前 AppKey，替换 `layouts/partials/comments.html` 里那一行即可。
- [ ] 旧站 `/search/`、`/page/N/` 已确认可放弃，无需补 alias。
- [ ] Google Analytics：旧配置里的 `G-W8NB0838L7` 在 hexo 侧未真正生效，需要就在 `config/_default/hugo.toml` 里打开 `googleAnalytics`。
- [ ] 可选性能优化：41 张原图共 29.6 MB，转 WebP 可压到 6 MB 左右。
