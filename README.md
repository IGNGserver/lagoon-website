# Lagoon 官网 · Lagoon Website

Lagoon 的产品官网与下载入口。本仓库只承载官网站点，产品源码与发版在独立仓库维护：

- 产品仓库（唯一事实来源）：<https://github.com/IGNGserver/opencode-lagoon>
- 产品定位：面向多种 coding agent harness 的原生 Android 控制端，首发深度适配 OpenCode 2.x
- 托管目标：Cloudflare（Pages / Workers 待定，见 [`docs/HOSTING.md`](docs/HOSTING.md)）

> **状态**：首版站点已完成（零构建静态站点，发布目录 `site/`），尚未部署上线。

## 仓库结构

| 路径 | 内容 |
| --- | --- |
| [`site/`](site) | 站点本体：`index.html`、`404.html`、`assets/`（样式、脚本、字体、图片）、`_headers` |
| [`docs/SITE.md`](docs/SITE.md) | 官网定位、视觉语言、页面结构、内容原则、版本更新清单、浏览器验证 |
| [`docs/HOSTING.md`](docs/HOSTING.md) | Cloudflare 托管方案与上线检查清单 |
| [`docs/WORKSPACE.md`](docs/WORKSPACE.md) | 本机 worktree 工作区约定 |
| [`scripts/verify-repository.sh`](scripts/verify-repository.sh) | 仓库契约检查（本地与 CI 共用） |
| [`scripts/check-site.py`](scripts/check-site.py) | 站点检查：本地引用、锚点、外链、元信息、体积预算 |
| [`.github/workflows/ci.yml`](.github/workflows/ci.yml) | CI：仓库契约、站点检查、whitespace、凭据扫描 |

## 开发

本地预览（无需安装任何依赖）：

```sh
python3 -m http.server 4173 --bind 127.0.0.1 --directory site
```

工作区、分支、验证与 PR 流程见 [`AGENTS.md`](AGENTS.md) 与 [`CONTRIBUTING.md`](CONTRIBUTING.md)。

## 许可

[MIT](LICENSE)

站点内自托管的字体 Sora 与 JetBrains Mono 采用 SIL Open Font License 1.1，许可证随字体置于 [`site/assets/fonts/`](site/assets/fonts)。
