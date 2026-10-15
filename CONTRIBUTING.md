# 贡献与开发

## 开始任务

1. 阅读根目录 `AGENTS.md` 与 `docs/SITE.md`。
2. 在仓库锚点 `/home/lvziw/项目/项目网站/Lagoon-site` 执行 `git fetch origin --prune`，解析远端默认分支，再创建独立 worktree（见 `docs/WORKSPACE.md`）。
3. 只在任务 worktree 中修改文件；不要在主目录开发。
4. 提交前运行 `bash scripts/verify-repository.sh`、`python3 scripts/check-site.py` 与 `git diff --check`，并按 `docs/SITE.md`「浏览器验证」实测页面，确认没有凭据、构建产物或无关改动。
5. 推送任务分支并按 PR 模板提交 PR；solo 模式下验证通过可由 Agent 合并。

## 提交

使用 Conventional Commits（`feat:` `fix:` `docs:` `chore:` …），说明"为什么"而不是罗列文件。
按设备级规范添加 `Assisted-by: <harness>/<model>` trailer；不要添加虚假的作者信息。

## 边界

- 本仓库与产品仓库（`IGNGserver/opencode-lagoon`）分离：产品源码、安装包、发布资产不进本仓库。
- 站点为零构建静态站点（见 `AGENTS.md`「技术栈」）：不引入框架脚手架、依赖清单或锁文件。
- 部署、域名与 Cloudflare 项目变更必须在 PR 描述中写明影响与回滚方式。
