# 托管方案（Cloudflare）

> 目标：站点以 Cloudflare 托管（Pages / Workers 静态资源二选一，未定案）。本文记录方案边界、
> 已具备的前提与上线检查清单；**清单未完成不得上线**。

## 已确认的前提（2026-10-13 核实）

- Cloudflare 账号已就绪，zone 与 Pages/Workers 均可用；
- 本仓库为独立公开仓库，CI 走 GitHub Actions；
- 本项目对应的 Pages/Workers 项目尚未创建，域名与子域未选定。

## 站点产物（2026-10-15 定案）

- 发布目录：`site/`；**无构建命令**（零构建静态站点，见 `AGENTS.md`「技术栈」）；
- `site/_headers`：安全响应头（含 CSP）与静态资源缓存策略，Pages 与 Workers 静态资源都识别该文件；
- `site/404.html`：Pages / Workers 静态资源的默认 404 页；
- 当前 `og:image` 为相对路径 `assets/img/og.jpg`；**域名定案后必须改为绝对 URL**（部分社交平台不解析相对路径），
  同时补 `<link rel="canonical">` 与 `og:url`，并视需要新增 `sitemap.xml`。

## 候选方案（实现时二选一）

| 方案 | 说明 | 何时适用 |
| --- | --- | --- |
| Cloudflare Pages（Git 集成） | 连接本仓库，push / PR 自动构建与预览 | 静态输出，希望 PR 预览部署 |
| Cloudflare Pages（Direct Upload） | CI 构建后 `wrangler pages deploy` 上传 | 需要构建缓存或自定义流水线 |
| Workers 静态资源 | `wrangler deploy` 发布 assets | 需要边缘逻辑（重定向、API 代理等）时 |

决策点：是否需要边缘逻辑、构建时长与缓存、预览部署方式。

## 上线检查清单（后续任务执行）

- [x] 前端技术栈与构建命令定案，登记到 `AGENTS.md` 并接入 CI（零构建，发布目录 `site/`）；
- [ ] 创建 Cloudflare 项目（建议名 `lagoon-website`，与仓库同名），确定 Pages / Workers；
- [ ] 选定 zone 与子域（候选：`igng.net` 子域或独立域名），配置自定义域并验证 HTTPS；
- [ ] 若走 CI 上传：在 GitHub 仓库 Secrets 配置 `CLOUDFLARE_API_TOKEN`（最小权限：仅目标账户 Pages/Workers 编辑）与 `CLOUDFLARE_ACCOUNT_ID`；凭据不得提交；
- [ ] 部署工作流：PR 预览 + `main` 合并后自动发布（或手动发布），并记录回滚方式；
- [ ] 把 `og:image` 改为绝对 URL，补 canonical / `og:url`；
- [ ] 上线后验证：首页 200、HTTPS 证书、重定向、产品仓库链接、下载链接指向 Releases、`_headers` 生效且 CSP 未拦截资源；
- [ ] 评估是否把官网域名回填为产品仓库的 homepage（产品仓库当前未设置 homepage）。

## 回滚

- Pages / Workers：在 Cloudflare 控制台回滚到上一个部署版本，或按对应 CLI 的 rollback 命令处理；
- 域名变更：修改 DNS 记录前记录原值，回滚即恢复原记录。
