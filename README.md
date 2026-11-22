# cc-router-data-masking

[cc-router](https://github.com/finch-xu/cc-router) 的隐私与敏感数据脱敏规则插件（`type: "rules"`，`rulesApi: 1`）。

纯声明式规则，零可执行代码，全部由 cc-router 的 Rust 线性时间正则引擎在请求发往上游模型之前本地执行。替换结果具有确定性，且使用分类型占位符（如 `[REDACTED_API_KEY]`、`[REDACTED_PASSWORD]`），既避免泄露敏感凭据，又不影响模型理解代码上下文。

## 脱敏能力与开关

安装后默认停用，可在 cc-router 插件设置页按需勾选以下开关后启用：

| 参数 | 默认 | 脱敏范围 | 替换占位符 |
|---|---|---|---|
| **API 密钥与云凭据** | 开 | Anthropic / OpenAI / AWS (`AKIA`/`ASIA`) / 阿里云 (`LTAI`) / 腾讯云 (`AKID`) / GitHub / GitLab / Stripe / Slack / Google / Groq / HuggingFace / npm / PyPI / SendGrid / Telegram Bot Token | `[REDACTED_API_KEY]` |
| **私钥块** | 开 | PEM / RSA / EC / OpenSSH / PGP 私钥块 | `[REDACTED_PRIVATE_KEY]` |
| **JWT / Auth / 环境变量密码** | 开 | JWT Token、`Authorization: Bearer/Basic/Token`、`API_KEY=` / `DB_PASSWORD=` 等常见敏感变量赋值（保留变量名） | `[REDACTED_JWT]` / `[REDACTED_TOKEN]` / `[REDACTED_SECRET]` |
| **数据库连接串密码** | 开 | PostgreSQL / MySQL / MariaDB / MongoDB / Redis / AMQP / SQL Server URI 中的密码段（保留协议、用户名与主机） | `[REDACTED_PASSWORD]` |
| **个人联系方式** | 关 | 邮箱地址、中国大陆手机号 | `[REDACTED_EMAIL]` / `[REDACTED_PHONE]` |
| **证件与银行卡号** | 关 | 中国大陆 18 位身份证号、常见银联 / Visa / MasterCard 卡号 | `[REDACTED_ID_CARD]` / `[REDACTED_CARD]` |
| **内网私有 IP** | 关 | `10.x` / `172.16–31.x` / `192.168.x`（保留 `127.0.0.1` 等本地调试地址） | `[REDACTED_INTERNAL_IP]` |
| **严格模式（`all_text`）** | 关 | 额外扫描 `system` 提示词与历史助手消息中的核心密钥与数据库密码（可能降低提示词缓存命中率） | 同上 |

> 默认仅扫描用户消息与工具输出（`scope: "user_text"`），不动 `system` 与历史助手消息，以尽量保持提示词缓存命中率。

## 安装方式

- **从社区商店安装**：在 cc-router →「插件」→「社区」中搜索 **隐私与密钥脱敏** 安装。
- **从链接安装**：在 cc-router →「插件」→「从链接安装」粘贴本仓库链接。
- **本地加载调试**：克隆本仓库后，在 cc-router →「插件」→「从文件夹加载」选择本目录，并在设置页点击「试运行」确认生效范围后启用。

## 本地校验

```bash
git clone --depth 1 https://github.com/finch-xu/cc-router .cc-router
cargo run --release --manifest-path .cc-router/src-tauri/Cargo.toml \
  -p cc-router-plugin-spec --bin ccr-plugin-check -- .
```

## License

[MIT](./LICENSE)
