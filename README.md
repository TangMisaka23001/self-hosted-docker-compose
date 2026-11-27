# self-hosted-docker-compose

自托管服务的 Docker Compose 配置集合。每个服务一个目录，通过 Traefik 统一反代，由 Dockge 管理。

## 前置要求

- Docker Engine + Docker Compose v2
- 外部网络 `traefik-public`（由 `traefik` 服务创建，所有走 Traefik 的服务都加入该网络）
- 域名解析指向本机公网地址
- 使用 Let's Encrypt 自动签发证书（Traefik 的 `leresolver`）

## 使用方式

1. 先启动 Traefik，创建 `traefik-public` 网络：

   ```bash
   cd traefik
   docker compose up -d
   ```

2. 每个服务：把 `.env.example` 复制为 `.env` 并填写，然后启动：

   ```bash
   cp .env.example .env   # 没有 .env.example 的服务跳过
   docker compose up -d
   ```

3. `.env` 已被 `.gitignore` 忽略（规则 `**/.env`），不会进版本库；`.env.example` 是 tracked 模板。

## 服务清单

| 服务 | 目录 | 域名 | 端口 | 说明 |
|---|---|---|---|---|
| Traefik | `traefik/` | — | 80 / 443 | 反向代理，创建 `traefik-public` 网络 |
| Beszel | `beszel/` | `beszel.misakatang.xyz` | 8090 | 服务器监控面板 |
| Beszel Agent | `beszel-agent/` | — | — | 监控探针，host 网络 |
| Bichon（已弃用） | `bichon/` | `bichon.oxcafebabe.cn` | 15630 | 发信/邮件服务 |
| Bitwarden | `bitwarden/` | `bitwarden.misakatang.xyz` | 80 | Vaultwarden 密码管理 |
| DeepSeek Harness | `deepseek-harness/` | `dsh.misakatang.xyz` | 3080 | DeepSeek Harness 服务 |
| Dockge | `dockge/` | `dockge.misakatang.xyz` | 5001 | Compose 栈管理面板 |
| Easytier | `easytier/` | — | — | P2P 组网，host 网络 |
| Memos | `memos/` | `memo.misakatang.xyz` | 5230 | 备忘录 |
| Obscura | `obscura/` | — | 3000 | MCP 服务，仅 EasyTier 内网访问 |
| OpenList（已弃用） | `openlist/` | `openlist.oxcafebabe.cn` | — | nginx 反代到 `OPEN_LIST_URL` |
| Pocket ID（已弃用） | `pocket-id/` | `id.oxcafebabe.cn` | 1411 | OIDC 身份认证 |
| RSSHub | `rsshub/` | `rsshub.misakatang.xyz` | 1200 | RSS 订阅生成 |
| RustFS（已弃用） | `rustfs/` | `s3.misakatang.xyz` / `s3-console.misakatang.xyz` | 9000 / 9001 | S3 对象存储 + 控制台 |
| Subconverter（已弃用） | `subconverter/` | `sub.oxcafebabe.cn` | 25500 | 订阅链接转换 |
| Uptime Kuma | `uptime-kuma/` | `uptime.misakatang.xyz` | 3001 | 状态监控 |
| Yarr | `yarr/` | `yarr.misakatang.xyz` | 7070 | RSS 阅读 |

> 注：标记为“已弃用”的服务配置仍保留在仓库中，但不再维护。

## 环境变量

每个需要变量的服务目录下都有 `.env.example`，复制成 `.env` 后填写：

| 服务 | 变量 |
|---|---|
| `beszel-agent` | `BESZEL_AGENT_KEY`、`BESZEL_AGENT_TOKEN` |
| `bitwarden` | `BITWARDEN_ADMIN_TOKEN` |
| `deepseek-harness` | `PROXY_USERNAME`、`PROXY_PASSWORD` |
| `easytier` | `EASYTIER_NETWORK_SECRET` |
| `obscura` | `OBSCURA_MCP_TOKEN` |
| `openlist`（已弃用） | `OPEN_LIST_URL` |
| `pocket-id`（已弃用） | `APP_URL`、`ENCRYPTION_KEY`（或 `ENCRYPTION_KEY_FILE`）、`TRUST_PROXY`、`MAXMIND_LICENSE_KEY`、`PUID`、`PGID` |
| `rsshub` | `RSSHUB_ACCESS_KEY` |
| `rustfs`（已弃用） | `RUSTFS_ACCESS_KEY`、`RUSTFS_SECRET_KEY` |
| `traefik` | `TRAEFIK_ACME_EMAIL`（Let's Encrypt 通知邮箱） |

## 目录结构

```
.
├── traefik/          # 反向代理
├── beszel/           # 监控面板
├── beszel-agent/     # 监控探针
├── bichon/           # 发信/邮件服务（已弃用）
├── bitwarden/        # 密码管理
├── deepseek-harness/ # DeepSeek Harness
├── dockge/           # 栈管理
├── easytier/         # 组网
├── memos/            # 备忘录
├── obscura/          # MCP 服务
├── openlist/         # 反代（已弃用）
├── pocket-id/        # 身份认证（已弃用）
├── rsshub/           # RSS 生成
├── rustfs/           # 对象存储（已弃用）
├── subconverter/     # 订阅转换（已弃用）
├── uptime-kuma/      # 状态监控
└── yarr/             # RSS 阅读
```

## 已弃用的服务

RustFS、Pocket ID、OpenList、Subconverter、Bichon 已弃用；相关目录和配置仅作参考，不再维护或推荐使用。

## 已移除的服务

`cliproxy`、`octopus`、`picoclaw` 不再包含在本仓库中。
