# Lagoon 官网（lagoon-website）· 项目 Agent 规范

> 只写本仓库与设备级规范的**差异**。Git 纪律、worktree 路径与生命周期、基线解析、冲突处理、
> PR 流程一律见 `~/.qoder/coder-rules/global-rules.md`，不在这里重复；冲突时以设备级安全条款为准。

Collaboration: solo
Baseline: main
Release: none
Validation: `bash scripts/verify-repository.sh`、`python3 scripts/check-site.py`、`git diff --check`（三者即 CI `verify` 作业的内容）；改动页面、样式或脚本时，另按 `docs/SITE.md`「浏览器验证」在桌面与移动视口实测并在 PR 中贴结论。
Worktree: `~/项目/.wt/Lagoon-site/<slug>`

## 这是什么

Lagoon 的官网与下载入口，源码与产品仓库分离维护：

- 产品仓库（唯一事实来源）：`https://github.com/IGNGserver/opencode-lagoon`；
- 本仓库只承载站点（页面、样式、静态资源、站点文档），不得复制产品源码、安装包或发布资产。

## 技术栈（2026-10-15 定案）

- **零构建静态站点**：`site/` 即发布目录，手写 HTML + CSS + 原生 ES modules，WebGL 着色器直接写在 `site/assets/js/ocean.js`；
  没有 `package.json`、锁文件、打包器或构建步骤，Cloudflare 直接托管 `site/`。
- 不引入框架、npm 依赖或 CDN 运行时依赖；JS 只允许相对路径导入（`scripts/check-site.py` 会拦截裸模块导入）。
- 需要浏览器自动化测试时，Playwright 等工具装在本机构建区（`$BUILD_SCRATCH_ROOT`），不进仓库、不进项目卷。
- 若将来改用框架或构建工具，必须同步更新本文件、`docs/SITE.md`、`docs/HOSTING.md` 与 CI。

## 集成补充

- 无额外门槛：按设备级规范第 5 节（solo）执行；`main` 已设分支保护，必须经 PR 且 CI `verify` 通过。
- 站点部署（Cloudflare）不随合并自动发生，见 `docs/HOSTING.md`。

## 发布

- 无版本化 Release（`Release: none`）；站点以持续部署方式发布，未启用前不得创建 tag 或 Release。

## 项目特殊限制

- 不得提交 `node_modules`、构建产物、`.wrangler/`、`.env*` 与任何凭据；`scripts/verify-repository.sh` 会拦截。
- 站点文案引用产品事实（功能、平台、版本）时必须与产品仓库当前 `README.md` / Release 对齐，不得编造未发布能力；
  正式版、预发布版与规划方向必须分开表述（见 `docs/SITE.md`「内容原则」与「版本更新清单」）。
- 页面里的手机界面是依据产品源码重绘的 HTML 示意，不是截图；改动时以产品仓库对应 Compose 页面为准，并保留“界面示意”标注。
- 正式域名 `https://lagoon.igng.net`，Cloudflare Pages 项目 `lagoon-website`；域名或项目名变更要在 `docs/HOSTING.md` 登记，并同步 `scripts/check-site.py` 的 `ALLOWED_HOSTS`。

## 环境事实

- 开发位置：NAS 共享卷 `~/项目`；仓库锚点 `~/项目/项目网站/Lagoon-site`，任务 worktree 在 `~/项目/.wt/Lagoon-site/<slug>`（`wt` 按锚点目录名命名）。
- 搜索收录：Google 走已验证的域名资源 `sc-domain:igng.net`（子域无需单独验证，`site/robots.txt` 的 Sitemap 行是发现入口）；Bing 走首页的 `msvalidate.01` meta 验证，**删掉该 meta 会让 Bing 站点失去所有权**。
- 生产目标：Cloudflare Pages Direct Upload 项目 `lagoon-website`，正式域名 `https://lagoon.igng.net`；自定义域名绑定在项目上，之后的每次部署都自动服务该域名，`https://lagoon-website.pages.dev` 只作默认别名保留；不写凭据。
