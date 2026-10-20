# bytechef-lzcapp

[ByteChef](https://github.com/bytechefhq/bytechef) 的 LazyCat LPK 打包 —— 开源 AI 智能体与工作流自动化平台（Java 单容器，内置 H2）。

## 形态

- 单容器镜像：`bytechef/bytechef`，端口 8080，数据在 `/root/.bytechef`
- 数据库：**H2 内嵌**（`BYTECHEF_DATABASE=h2`），无需外部 PostgreSQL
- 镜像交付：**lazycat 模式**（官方商店要求镜像来自懒猫镜像源，由 Action 转存并改写 manifest）
- 发布目标：**双商店**（官方商店 + 喵喵商店）

## 版本发现

上游镜像只有 `YYYYMMDD` 日期 tag（无可用 SemVer tag），因此使用 date channel 映射：

- `tag_regex: ^20[0-9]{6}$` 只匹配 20xx 开头的八位日期
- `version_regex` 拆出年/月/日，`version_template` 映射成 `2026.10.13` 形式
- `sort: semver` 保证取最新日期

## 官方商店强制要求（已满足）

| 要求 | 处理 |
|------|------|
| 密码类应用必须提供凭据 | 部署参数 login_email / login_password，登录与注册页自动填充 |
| 上传/下载必须接文件选择器拦截 | 注入 lzc-file-chooser-inject.js（工作流导入、CSV 导入、图标上传） |
| 图标不超过 200 KB | 512x512 PNG，约 21 KB |
| 截图（PC 2-8 张） | 3 张上游官方截图 |
| 仅 PC 平台 | unsupported_platforms: ios, android, tvos |

## 注意

H2 模式是上游推荐的「无需外部数据库」评估方案；如需 AI 知识库与 Copilot（pgvector），需改用 PostgreSQL 部署。

## 结构

- `package.yml` — LPK v2 包元数据 + 平台限制
- `lzc-manifest.yml` — 单服务、免密登录注入、文件选择器注入
- `lzc-deploy-params.yml` — 登录邮箱与密码部署参数
- `content/lazycat-injects/` — 文件选择器拦截脚本
- `.github/screenshots/` — 官方商店截图
- `.github/lazycat-action.yml` — 双商店配置
- `.github/workflows/lazycat.yml` — 每日自动跟版 + 双商店发布
