# 托管方案（Cloudflare）

> 站点当前以 Cloudflare Pages 托管，采用 Direct Upload。本文记录方案边界、
> 已具备的前提与上线检查清单；未完成项表示自定义域名或自动化发布尚未启用，
> 不应据此宣称正式域名已上线。

## 已确认的前提（2026-10-15 更新）

- Cloudflare 账号已就绪，zone 与 Pages/Workers 均可用；
- 本仓库为独立公开仓库，CI 走 GitHub Actions；
- Pages 项目 `lagoon-website` 已创建，默认地址为 `https://lagoon-website.pages.dev`；自定义域名尚未选定。

## 站点产物（2026-10-15 定案）

- 发布目录：`site/`；**无构建命令**（零构建静态站点，见 `AGENTS.md`「技术栈」）；
- `site/_headers`：安全响应头（含 CSP）与静态资源缓存策略，Pages 与 Workers 静态资源都识别该文件；
- `site/404.html`：Pages / Workers 静态资源的默认 404 页；
- 当前 `og:image` 为相对路径 `assets/img/og.jpg`；**域名定案后必须改为绝对 URL**（部分社交平台不解析相对路径），
  同时补 `<link rel="canonical">` 与 `og:url`，并视需要新增 `sitemap.xml`。

## 方案选择

| 方案 | 状态 | 说明 |
| --- | --- | --- |
| 方案 | 状态 | 说明 |
| --- | --- | --- |
| Cloudflare Pages（Git 集成） | 未启用 | 创建时 Cloudflare Git 安装返回 8000011，未建立自动同步 |
| Cloudflare Pages（Direct Upload） | **当前方案** | 零构建发布 `site/`，通过 Pages API 上传静态资源 |
| Workers 静态资源 | 未采用 | 当前不需要边缘逻辑 |

当前生产部署：`f129e204`，对应提交 `8c4284f0caac82202f492108fc6e06dfbb3efacb`，
部署地址为 `https://f129e204.lagoon-website.pages.dev`，项目规范地址为
`https://lagoon-website.pages.dev`。

## 上线检查清单

- [x] 前端技术栈与构建命令定案，登记到 `AGENTS.md` 并接入 CI（零构建，发布目录 `site/`）；
- [x] 创建 Cloudflare 项目：Pages 项目 `lagoon-website`，生产分支标记为 `main`，发布目录为 `site/`；
- [ ] 选定 zone 与子域（候选：`igng.net` 子域或独立域名），配置自定义域并验证 HTTPS；
- [ ] 若启用 CI 上传：在 GitHub 仓库 Secrets 配置最小权限的 `CLOUDFLARE_API_TOKEN` 与 `CLOUDFLARE_ACCOUNT_ID`；当前未配置，凭据不得提交；
- [x] 手动 Direct Upload 已完成：Pages API 部署 `f129e204`，固定提交 `8c4284f0caac82202f492108fc6e06dfbb3efacb`；
- [ ] 自动发布工作流：当前未启用 Git 集成或 GitHub Actions，后续变更需按 Direct Upload 流程重新发布；
- [ ] 把 `og:image` 改为绝对 URL，补 canonical / `og:url`；
- [x] 上线后验证：默认 Pages 地址首页与资源返回 200、404 返回 404、HTTPS 与 CSP/安全响应头生效，浏览器控制台无错误；
- [ ] 评估是否把官网域名回填为产品仓库的 homepage（产品仓库当前未设置 homepage）。

## 回滚

- Pages / Workers：在 Cloudflare 控制台回滚到上一个部署版本，或按对应 CLI 的 rollback 命令处理；
- 当前生产部署 ID：`f129e204-4207-4df1-b13a-a3cc3f9f6dbb`；首次部署前没有可回滚的旧版本；
- 域名变更：修改 DNS 记录前记录原值，回滚即恢复原记录。
