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
| Bichon | `bichon/` | `bichon.oxcafebabe.cn` | 15630 | 发信/邮件服务 |
| Bitwarden | `bitwarden/` | `bitwarden.misakatang.xyz` | 80 | Vaultwarden 密码管理 |
| Dockge | `dockge/` | `dockge.misakatang.xyz` | 5001 | Compose 栈管理面板 |
| Easytier | `easytier/` | — | — | P2P 组网，host 网络 |
| Memos | `memos/` | `memo.misakatang.xyz` | 5230 | 备忘录 |
| OpenList | `openlist/` | `openlist.oxcafebabe.cn` | — | nginx 反代到 `OPEN_LIST_URL` |
| Pocket ID | `pocket-id/` | `id.oxcafebabe.cn` | 1411 | OIDC 身份认证 |
| RSSHub | `rsshub/` | `rsshub.misakatang.xyz` | 1200 | RSS 订阅生成 |
| RustFS | `rustfs/` | `s3` / `s3-console` / `dav.misakatang.xyz` | 9000 / 9001 / 8080 | S3 对象存储 + 控制台 + WebDAV |
| Subconverter | `subconverter/` | `sub.oxcafebabe.cn` | 25500 | 订阅链接转换 |
| Yarr | `yarr/` | `yarr.misakatang.xyz` | 7070 | RSS 阅读 |

> 注：部分服务仍使用 `oxcafebabe.cn`（Bichon、OpenList、Pocket ID、Subconverter），其余已迁移到 `misakatang.xyz`，按需自行统一。

## 环境变量

每个需要变量的服务目录下都有 `.env.example`，复制成 `.env` 后填写：

| 服务 | 变量 |
|---|---|
| `beszel-agent` | `BESZEL_AGENT_KEY`、`BESZEL_AGENT_TOKEN` |
| `bitwarden` | `BITWARDEN_ADMIN_TOKEN` |
| `easytier` | `EASYTIER_NETWORK_SECRET` |
| `openlist` | `OPEN_LIST_URL` |
| `pocket-id` | `APP_URL`、`ENCRYPTION_KEY`（或 `ENCRYPTION_KEY_FILE`）、`TRUST_PROXY`、`MAXMIND_LICENSE_KEY`、`PUID`、`PGID` |
| `rsshub` | `RSSHUB_ACCESS_KEY` |
| `rustfs` | `RUSTFS_ACCESS_KEY`、`RUSTFS_SECRET_KEY` |
| `traefik` | `TRAEFIK_ACME_EMAIL`（Let's Encrypt 通知邮箱） |

## 目录结构

```
.
├── traefik/          # 反向代理
├── beszel/           # 监控面板
├── beszel-agent/     # 监控探针
├── bitwarden/        # 密码管理
├── dockge/           # 栈管理
├── easytier/         # 组网
├── memos/            # 备忘录
├── openlist/         # 反代
├── pocket-id/        # 身份认证
├── rsshub/           # RSS 生成
├── rustfs/           # 对象存储
├── subconverter/     # 订阅转换
└── yarr/             # RSS 阅读
```

## 已移除的服务

`cliproxy`、`octopus`、`picoclaw` 不再包含在本仓库中。
