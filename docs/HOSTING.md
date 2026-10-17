# 托管方案（Cloudflare）

> 站点当前以 Cloudflare Pages 托管，采用 Direct Upload。本文记录方案边界、
> 已具备的前提与上线检查清单；未完成项表示自定义域名或自动化发布尚未启用，
> 不应据此宣称正式域名已上线。

## 已确认的前提（2026-10-15 更新）

- Cloudflare 账号已就绪，zone 与 Pages/Workers 均可用；
- 本仓库为独立公开仓库，CI 走 GitHub Actions；
- Pages 项目 `lagoon-website` 已创建；正式域名 `https://lagoon.igng.net` 已于 2026-10-17 绑定为自定义域名，默认地址 `https://lagoon-website.pages.dev` 保留为别名。

## 站点产物（2026-10-15 定案）

- 发布目录：`site/`；**无构建命令**（零构建静态站点，见 `AGENTS.md`「技术栈」）；
- `site/_headers`：安全响应头（含 CSP）与静态资源缓存策略，Pages 与 Workers 静态资源都识别该文件；
- `site/404.html`：Pages / Workers 静态资源的默认 404 页；
- 当前 `og:image` 为相对路径 `assets/img/og.jpg`；**域名定案后必须改为绝对 URL**（部分社交平台不解析相对路径），
  同时补 `<link rel="canonical">` 与 `og:url`，并视需要新增 `sitemap.xml`。

## 方案选择

| 方案 | 状态 | 说明 |
| --- | --- | --- |
| Cloudflare Pages（Git 集成） | 未启用 | 创建时 Cloudflare Git 安装返回 8000011，未建立自动同步 |
| Cloudflare Pages（Direct Upload） | **当前方案** | 零构建发布 `site/`，通过 Pages API 上传静态资源 |
| Workers 静态资源 | 未采用 | 当前不需要边缘逻辑 |

当前生产部署：`5214d2ca-6662-41e0-9ba6-d843e5e6b48c`，对应提交 `f9ead1c`（2026-10-17，切换到
正式域名后重新发布）；项目规范地址为 `https://lagoon.igng.net`，自定义域名与项目内所有部署同源，
`https://5214d2ca.lagoon-website.pages.dev` 只是本次部署的临时地址。
上一版为 `f129e204-4207-4df1-b13a-a3cc3f9f6dbb`（提交 `8c4284f0caac82202f492108fc6e06dfbb3efacb`）。

- 自定义域名记录：proxied CNAME `lagoon.igng.net` → `lagoon-website.pages.dev`，zone `igng.net`
  （zone id `d3f06d3e7ef8cccf99e93ce9c4ea4f44`，record id `f22e0dd39c0f914101dab3de2d5a9727`）。

## 搜索收录

- Bing Webmaster Tools：站点 `https://lagoon.igng.net/` **已验证所有权**（2026-10-17），方式是首页
  `<meta name="msvalidate.01" content="B411826ACAE5C5CD20EA5574C3F8A6E4">`；该值是账号级 auth code，与
  其它 `*.igng.net` 站点相同，**删掉这条 meta 会让 Bing 失去所有权记录**。已提交
  `https://lagoon.igng.net/sitemap.xml`（抓取状态 Success，1 条 URL）与首页。
- Google Search Console：无需为本子域单独验证，域名资源 `sc-domain:igng.net`（权限 `siteFullUser`）已覆盖。
  2026-10-17 用 URL Inspection 查询首页为 `URL is unknown to Google`，属新建域名的正常状态；
  Google 需要先抓 `site/robots.txt` 的 `Sitemap:` 行才会发现 `site/sitemap.xml`。
- GSC 的 sitemap 提交与 Request Indexing 只能在控制台 UI 做：公开 API 的 `sitemaps.submit` 已下线
  （`GET .../sitemaps` 仍可读，`PUT .../feedpath/...` 一律 404）。
- `site/sitemap.xml` 只列首页；新增页面时要同步补该文件与 `ALLOWED_HOSTS`。

## 上线检查清单

- [x] 前端技术栈与构建命令定案，登记到 `AGENTS.md` 并接入 CI（零构建，发布目录 `site/`）；
- [x] 创建 Cloudflare 项目：Pages 项目 `lagoon-website`，生产分支标记为 `main`，发布目录为 `site/`；
- [x] 选定 zone `igng.net` 与子域 `lagoon`，自定义域名 `lagoon.igng.net` 已配置并验证 HTTPS（2026-10-17）；
- [ ] 若启用 CI 上传：在 GitHub 仓库 Secrets 配置最小权限的 `CLOUDFLARE_API_TOKEN` 与 `CLOUDFLARE_ACCOUNT_ID`；当前未配置，凭据不得提交；
- [x] 手动 Direct Upload 已完成：首版 `f129e204`（提交 `8c4284f`），切换到正式域名后重新发布为 `5214d2ca`（提交 `f9ead1c`）；
- [x] 正式域名验证通过：`https://lagoon.igng.net/` 首页 200、未知路径 404、`/sitemap.xml` 200、`robots.txt` 指向正式域名、HTTPS 与安全响应头生效；
- [ ] 自动发布工作流：当前未启用 Git 集成或 GitHub Actions，后续变更需按 Direct Upload 流程重新发布；
- [x] `og:image` 已改为绝对 URL，并补 `canonical` / `og:url` / `sitemap.xml` 与 `robots.txt` 的 Sitemap 行（2026-10-17）；
- [x] 上线后验证：默认 Pages 地址首页与资源返回 200、404 返回 404、HTTPS 与 CSP/安全响应头生效，浏览器控制台无错误；
- [ ] 评估是否把官网域名回填为产品仓库的 homepage（产品仓库当前未设置 homepage）。

## 回滚

- Pages / Workers：在 Cloudflare 控制台回滚到上一个部署版本，或按对应 CLI 的 rollback 命令处理；
- 发布命令（零构建，直接发布 `site/`；自定义域名绑定在项目上，所以每次发布都直接服务 `https://lagoon.igng.net`）：
  `npx wrangler pages deploy site --project-name lagoon-website --branch main`；凭据只以环境变量 `CLOUDFLARE_API_TOKEN` / `CLOUDFLARE_ACCOUNT_ID` 注入（账号 `d08c9ec0a1765b7040866053cec24e8b`），不得写进仓库或 CI 长期 Secrets；
- 当前生产部署 ID：`5214d2ca-6662-41e0-9ba6-d843e5e6b48c`；回滚点：`f129e204-4207-4df1-b13a-a3cc3f9f6dbb`；
- 域名变更：修改 DNS 记录前记录原值，回滚即恢复原记录；当前是 proxied CNAME `lagoon.igng.net` → `lagoon-website.pages.dev`，改动必须同步 Pages 自定义域名与 `ALLOWED_HOSTS`。
