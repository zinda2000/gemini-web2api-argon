# gemini-web2api-argon

[简体中文](README.md) | [English](README.en.md)

将 Google Gemini Web 网页版转换为 OpenAI、Claude 和 Gemini 兼容 API，支持账号可用的 `gemini-4-argon` 等模型。

## 项目亮点

- **紧跟 Gemini Web**：动态读取账号的模型与工具目录，支持 Argon、思考强度和官网工具；实际可用性取决于账号、地区和官网变化。
- **广泛的客户端兼容**：支持流式对话、工具调用、结构化输出，以及图片、音频、视频和文件接口。
- **方便日常使用**：提供多账号调度、自动续签、代理池、访客会话、用量统计、网页管理端和跨平台桌面版。
- **持续维护**：围绕官网协议变化、模型接入、稳定性和客户端兼容性持续迭代。

## 致谢与项目说明

首先感谢 [zexadev](https://github.com/zexadev) 创建并开源 [gemini-web2api-go](https://github.com/zexadev/gemini-web2api-go)，本项目的原始代码源自该项目。也感谢 [Mag1cFall](https://github.com/Mag1cFall) 在 [Gemini-Web2API](https://github.com/Mag1cFall/Gemini-Web2API) 中的后续工作，以及所有贡献者的付出。正是这些开放分享，让本项目得以继续迭代。

Gemini Web 的模型和接口变化较快，及时适配对使用体验尤为重要。我所基于的 Mag1cFall 版本已有一段时间未更新，维护节奏和关注重点可能与这里的需求有所不同。为了能够按自己的节奏快速响应变化、持续维护并分享这些改进，我选择独立发布本项目。衷心感谢各位作者的工作，并充分尊重各项目的方向和维护安排。

本仓库沿用 `gemini-web2api` 可执行文件名和部分内部标识，以兼容现有部署。项目主页、管理端链接与在线更新源指向本仓库；发行包发布后可通过在线更新获取。

## 特性

- **OpenAI 兼容**：`/v1/chat/completions`、`/v1/completions`、`/v1/responses`、`/v1/files`、`/v1/audio/*`、`/v1/videos`、`/v1/models`
- **Claude 兼容**：`/v1/messages`、Files API
- **Gemini 兼容**：`/v1beta/models/{model}:generateContent`、`:streamGenerateContent`、`:predictLongRunning`、Files API
- **流式输出**：文本与思考内容实时增量返回，各协议的思考强度映射到官网 Low、Medium、High，模型名也可带 `(high)`、`(low)`、`(-1)` 等后缀指定思考强度
- **结构化输出**：OpenAI `response_format`、Anthropic `output_format`、Gemini `responseSchema` 与 `responseFormat`（含 `text/x.enum` 枚举）按 JSON Schema 约束回答
- **函数工具**：四种协议的函数工具经网页提示词桥接，模型在调用前写的说明随调用一起返回，调用无法解析时在首个可见输出前换账号重试
- **图片能力**：支持多模态输入、图片生成和图片编辑，生成图返回官网“下载原图”的原尺寸图片
- **官网工具**：Create video、Create music、Canvas、Deep Research、Guided Learning 与 Task 模式以独立模型 ID 提供
- **多账户负载均衡**：按模型可用性调度多个 Google 账户，用量上限与频率限制只冷却对应模型，并发数与调度策略可配置
- **访客会话**：没有可用账号时以 Gemini 网页的未登录会话提供官网未登录可用的模型（当前为 `gemini-3.5-flash-lite`，不含官网工具与附件上传），账号就绪后优先使用账号
- **管理端与桌面版**：网页管理端提供实时日志、活动请求、用量与费用统计、账号导入与管理、模型目录、设置与 Playground；Windows、macOS、Linux 桌面版带托盘、单实例与开机启动
- **运行日志**：彩色分列控制台日志，逐请求记录开始、选号、上传、首个输出、换号、等待与结束
- **请求账本**：SQLite 保存每个请求的用量，按模型、账号与协议统计并按 Gemini API 单价估算费用
- **Gems**：账号自建的 Gem 与 Google 预置 Gem 以 `gem-<Gem ID>` 模型 ID 提供
- **代理池**：多个 HTTP、HTTPS、SOCKS4、SOCKS5 代理组成代理池，每个账号固定绑定其中一个，定时检测并在代理不可用时自动改绑；支持每账号固定代理、直连与系统代理
- **模型映射**：支持将外部模型名映射到当前 Gemini 模型
- **纯协议运行**：完成账号配置后无需浏览器和前端脚本

## 支持的模型

| 模型 ID | 名称 | 输入窗口 / 能力 | 默认 |
| --- | --- | ---: | --- |
| `gemini-3.8-flash` | 3.8 Flash | 32,768–1,048,576 | 是 |
| `gemini-3.1-pro` | 3.1 Pro | 32,768–1,048,576 |  |
| `gemini-3.5-flash-lite` | 3.5 Flash-Lite | 32,768–1,048,576 |  |
| `gemini-4-argon` | 4 Argon | 32,768–1,048,576 |  |
| `gemini-auto` | Auto | 免费账号 |  |
| `gemini-3.8-flash-task`、`gemini-4-argon-task` | Task | 代为执行任务 |  |
| `gemini-3.1-flash-image` | Nano Banana 2 | 图片生成与编辑 |  |
| `gemini-video` | Video | 视频生成 |  |
| `gemini-music` | Music | 音乐生成 |  |
| `gemini-canvas` | Canvas | 文档与代码 |  |
| `gemini-deep-research` | Deep Research | 研究报告 |  |
| `gemini-guided-learning` | Guided Learning | 引导式学习 |  |

- **模型目录**：服务启动时读取各账号的 Gemini Web 模型目录与官网工具，运行后以 `/v1/models` 为准；模型 ID 由官网显示名称生成，账号收到新模型后自动公开
- **目录字段**：`available_account_count` 是可用账号数，`default` 标出默认模型，`thinking_levels` 列出可选思考强度
- **工具模型**：工具 ID 只在账号具备对应官网工具时出现，由该账号的官网默认模型执行
- **图片**：图片 ID 用于对话生图和 `/v1/images/*`，`size` 与 `imageConfig.aspectRatio` 支持 1:1、9:16、3:4、4:3、16:9
- **视频**：视频 ID 的 `imageConfig.aspectRatio` 支持横向 16:9 与竖向 9:16
- **会话历史**：Task、Deep Research 与视频请求使用普通会话，对话保存在账号历史中
- **Deep Research**：研究计划与进度作为思考内容返回，报告作为正文返回，报告中的引用以各协议的引用字段给出网页来源；一次通常需要十几分钟，客户端请求超时要设得更长
- **配额**：上下文窗口、模型权限和配额由账号的套餐与地区决定，账号用量在管理端账号页查看
- **token usage**：文本输入、可见输出和思考摘要按 Gemini tokenizer 计算；计数接口中的图片、音频、视频与 PDF 按 Gemini API 计数规则估算

## 维护与升级

### Windows 隐藏启动

桌面版支持托盘和开机启动，可在托盘菜单中设置。也可以在 PowerShell 中从可执行文件所在目录运行以下命令，为当前用户添加带 `--hidden` 的启动项：

```powershell
$exe = (Resolve-Path .\gemini-web2api.exe).Path
Set-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' -Name 'Gemini-Web2API' -Value "`"$exe`" --hidden"
```

取消该启动项：

```powershell
Remove-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run' -Name 'Gemini-Web2API'
```

### 模型和协议更新

服务启动或重新连接账号时会读取官网模型目录。模型标识变化时可先重新连接账号，并检查 `/v1/models`；这不能保证所有官网协议变动都能自动适配。

如果官网可以正常使用而重新连接后仍有问题，可在浏览器开发者工具中对比 `StreamGenerate` 请求的模型扩展头 `x-goog-ext-525001261-jspb` 和 `f.req` 请求体，再检查以下位置：

- `internal/gemini/client.go`：生成请求、会话模式和模型目录请求；当前版本禁用了临时会话，可能影响官网历史记录。
- `internal/gemini/models.go`：模型目录解析、`clientFeatures` 和 `buildModelHeader`。
- `internal/gemini/payload.go`：`buildGeneratePayload` 中的请求体槽位。

字段和槽位可能随官网变化，请结合实际请求和测试判断；不要公开抓包中的 Cookie、令牌或个人信息。

修改 Go 代码后，可在仓库根目录重新构建 Windows 桌面版（需先安装项目构建依赖并准备前端资源）：

```powershell
go build -tags "desktop,production,nomsgpack" -trimpath -ldflags="-s -w -H windowsgui" -o gemini-web2api.exe ./cmd/gemini-web2api
```

构建前请退出正在运行的程序；修改前端时也需重新构建前端。关于构建和协议的更多细节请参阅仓库中的开发文档。

## 快速开始

### Windows 一键启动

如本项目提供发行包，可从[Releases](https://github.com/zinda2000/gemini-web2api-argon/releases) 下载 `windows-amd64.zip` 发布包，解压后双击 `gemini-web2api.exe`：

- 管理端在桌面窗口中打开，窗口使用系统的 WebView2 运行时；关闭窗口后留在托盘，托盘菜单可复制 API 地址、设置开机启动
- 再次双击 `gemini-web2api.exe` 唤回已运行的窗口
- 账号在管理端的“导入账号”页从本机 Chrome、Cookie 或远程配对导入
- `.env`、`auth/` 与 `data/` 都在 `gemini-web2api.exe` 所在目录

在 cmd 或 PowerShell 中运行命令时写作 `cmd /c start /wait gemini-web2api.exe <参数>`，命令行等程序结束后返回：

```powershell
cmd /c start /wait gemini-web2api.exe --help
cmd /c start /wait gemini-web2api.exe setup --email "name@example.com"
```

### macOS 与 Linux 桌面版

如本项目提供发行包，可从[Releases](https://github.com/zinda2000/gemini-web2api-argon/releases) 下载对应系统的桌面版：

| 系统 | 下载 | 数据目录 |
| --- | --- | --- |
| macOS | `desktop-…-darwin-universal.tar.gz` | `~/Library/Application Support/Gemini-Web2API` |
| Linux | `desktop-…-linux-amd64.tar.gz` 或 `desktop-…-linux-arm64.tar.gz` | `~/.config/Gemini-Web2API` |

- 桌面版都是便携包：macOS 解压后打开 `Gemini-Web2API.app`；Linux 解压后运行 `gemini-web2api`，需要系统的 GTK 4 与 WebKitGTK 6.0
- 桌面版在窗口中打开管理端，关闭窗口后留在托盘；托盘菜单可复制 API 地址、设置开机启动
- 账号在管理端的“导入账号”页从本机 Chrome、Cookie 或远程配对导入
- `.env`、`auth/` 与 `data/` 都在上表的数据目录中

### 手动启动

先构建管理端前端，再编译程序：

```bash
pnpm -C web install
pnpm -C web build
go build -tags nomsgpack -o gemini-web2api.exe ./cmd/gemini-web2api
```

Windows 首次使用时，直接运行交互式配置：

```powershell
.\gemini-web2api.exe setup
```

`setup` 会扫描本机 Chrome 账号，并短暂启动独立临时 Chrome 进程读取 App-Bound 主密钥。现有 Chrome 窗口与 Profile 保持原状。

也可以直接指定 Chrome 中已登录的账号；同一 Profile 登录了多个 Google 账号时，每个账号单独列出、单独导入：

```powershell
.\gemini-web2api.exe setup --email "name@example.com"
```

也可以粘贴浏览器请求头中 `Cookie` 字段的一行值；这种状态在现有 Cookie 有效期内使用，不具备 OAuth 续签能力：

```powershell
.\gemini-web2api.exe setup --cookie "SAPISID=...; __Secure-1PSID=..." --id account-name
```

浏览器扩展导出的 Cookie JSON 与 Netscape `cookies.txt` 文件用 `--cookie-file` 导入：

```powershell
.\gemini-web2api.exe setup --cookie-file cookies.txt --id account-name
```

Cookie 来自登录了多个 Google 账号的浏览器时，用 `--authuser` 指定要使用的账号，取值为官网地址 `gemini.google.com/u/N/app` 中的 `N`，默认 0；管理端 Cookie 导入的“账号序号”与之相同：

```powershell
.\gemini-web2api.exe setup --cookie-file cookies.txt --id account-name --authuser 1
```

服务部署在没有 Chrome 的服务器上时，在管理端“导入账号”页生成配对令牌，然后在登录了 Chrome 的电脑上把账号上传到服务器；令牌从标准输入读取，非本机地址需要 HTTPS，可经 SSH 隧道 `ssh -L 8007:127.0.0.1:8007 <服务器>` 使用 `http://127.0.0.1:8007`：

```powershell
.\gemini-web2api.exe setup --email "name@example.com" --remote https://example.com
```

`setup` 会生成 `auth/<账号>/storage-state.json` 并验证模型目录。Chrome 导入还会保存浏览器退出后续签所需的 OAuth 续签材料。日常启动只需运行：

```powershell
.\gemini-web2api.exe
```

默认读取 `auth` 目录并监听 `127.0.0.1:8007`。Linux 和 macOS 可以在现有 Cookie 有效期内使用已经生成的 `auth` 目录；续签仍需在原 Windows 设备执行。

服务运行期间每 10 分钟为每个账号轮换一次登录 Cookie，与 Gemini 网页相同；响应中变化的 Cookie 写回 `auth/<账号>/storage-state.json`，重启后直接沿用。Chrome 导入的账号在登录态失效时自动续签。

### 可选配置

程序启动时自动读取当前目录的 `.env`，setup 与日常服务使用同一个 `PROXY`。默认配置可以直接使用；需要调整时参照仓库中的 [`.env.example`](.env.example) 编写 `.env`。

```dotenv
# 是否保存官网会话历史；当前版本禁用了临时会话
GEMINI_SAVE_HISTORY=false

# 本地 2API 访问密钥，多个 key 用逗号分隔
PROXY_API_KEY=

# 代理池，多个代理用逗号分隔，支持 HTTP、HTTPS、SOCKS4、SOCKS5
PROXY=
```

### Docker

```bash
docker compose up -d --build
```

- 以上命令从当前源码构建镜像；数据目录挂载到 `./data`，其中放 `.env`、`auth/` 与请求账本
- 端口对外开放时设置 `PROXY_API_KEY`；从容器外打开管理端需要 `ADMIN_AUTH_ENABLED=true` 与 `ADMIN_PASSWORD`
- 账号用管理端的 Cookie 导入、远程配对，或把其他电脑上的 `auth/<账号>/` 复制到 `data/auth/`
- 修改 `data/.env` 后执行 `docker compose restart`

### 代理

`PROXY` 是代理池，多个代理用逗号分隔，例如 `PROXY=socks5://127.0.0.1:1080,http://user:pass@10.0.0.2:7890`，支持 `http`、`https`、`socks4`、`socks4a`、`socks5`、`socks5h`。管理端“设置 → 服务 → 出口代理”按行编辑，保存后生效。

- **分配**：没有固定代理的账号与访客会话各绑定一个代理并一直使用它，新账号分到使用账号最少的代理，账号数相同时取列表中靠前的一个；代理列表变化时以最少的改绑重新均衡
- **检测**：服务启动时、每 5 分钟、账号连接失败时以及在管理端点击“立即检测”时，经每个代理请求 Gemini 首页
- **故障切换**：不可用代理上的账号改绑到可用代理并重新初始化；全部代理不可用时账号保持原代理
- **状态**：设置页的代理池卡片显示每个代理的状态、延迟与使用它的账号
- **系统代理**：`PROXY` 为空时使用环境变量 `HTTPS_PROXY`，未设置时在 Windows 上使用系统代理设置（按协议分列时依次取 https、http 与 socks，socks 为 SOCKS4），都没有时直连

账号也可以设置固定代理，固定代理优先于代理池，填 `direct` 表示该账号直连。在管理端账号页“修改代理”中填写，或在导入时指定：

```powershell
.\gemini-web2api.exe setup --email "name@example.com" --proxy "socks5://127.0.0.1:1080"
```

`setup --proxy` 与管理端导入时填写的代理写入账号认证文件 `auth/<账号>/storage-state.json` 的 `geminiWeb2api.proxy`；不填时导入请求经 `PROXY` 中的一个代理发出，账号运行时从代理池分配。代理出口需要位于 Gemini 支持的地区。

## API 端点

### OpenAI 兼容

```text
POST   /v1/chat/completions
POST   /v1/completions
POST   /v1/responses
GET    /v1/responses/{id}
DELETE /v1/responses/{id}
GET    /v1/responses/{id}/input_items
POST   /v1/responses/{id}/cancel
POST   /v1/responses/input_tokens
POST   /v1/responses/compact
POST   /v1/images/generations
POST   /v1/images/edits
POST   /v1/audio/speech
POST   /v1/audio/transcriptions
POST   /v1/audio/translations
POST   /v1/videos
GET    /v1/videos
GET    /v1/videos/{id}
GET    /v1/videos/{id}/content
DELETE /v1/videos/{id}
POST   /v1/files
GET    /v1/files
GET    /v1/files/{id}
GET    /v1/files/{id}/content
DELETE /v1/files/{id}
GET    /v1/models
```

- **Responses**：默认保存到 `data/requests.db`，可读取、列出输入与删除，`store: false` 不保存；`background: true` 立即返回 `queued`，之后轮询或取消
- **续接**：`previous_response_id` 与会话 ID 在服务重启后的 `SESSION_TTL` 内仍可续接；每轮发送完整历史的客户端在历史与上一轮回答一致时续接同一网页会话，只发送新消息
- **文件**：上传的文件保存在 `data/files/`，生成请求以 `file_id` 引用
- **朗读**：`/v1/audio/speech` 使用官网回答的朗读功能，输出 mp3、opus、wav、flac 或 pcm，`voice` 与 `speed` 不生效
- **转写与翻译**：由所选对话模型完成
- **视频**：`/v1/videos` 使用 `gemini-video`
- **官网没有的能力**：Embeddings、Moderations 与 Realtime 端点返回 404 `model_not_found`

### Claude 兼容

```text
POST   /v1/messages
POST   /v1/messages/count_tokens
POST   /v1/files
GET    /v1/files
GET    /v1/files/{id}
DELETE /v1/files/{id}
GET    /v1/models
```

带 `anthropic-version` 或 `anthropic-beta` 请求头的 `/v1/files` 请求按 Anthropic Files API 返回，消息中以 `source.file_id` 引用。

### Gemini 兼容

```text
POST   /v1beta/models/{model}:generateContent
POST   /v1beta/models/{model}:streamGenerateContent?alt=sse
POST   /v1beta/models/{model}:countTokens
POST   /v1beta/models/{model}:predictLongRunning
GET    /v1beta/operations/{id}
POST   /upload/v1beta/files
GET    /v1beta/files
GET    /v1beta/files/{name}
DELETE /v1beta/files/{name}
GET    /v1beta/models
```

`streamGenerateContent` 不带 `alt=sse` 时返回 JSON 数组。上传的文件以 `fileData.fileUri` 引用，YouTube 链接按官网读取视频内容。`predictLongRunning` 使用 `gemini-video`。

### 服务状态

```text
GET  /readyz
```

认证支持 `Authorization: Bearer xxx`、`?key=xxx`、`x-goog-api-key` 和 `x-api-key`。未设置 `PROXY_API_KEY` 时，浏览器中只有本机地址打开的网页可以调用接口，其他网站的网页需要服务设置 key 并在请求中携带。

### 管理端

浏览器打开 `http://127.0.0.1:8007/` 进入管理端，页面使用 `/api/*` 接口。

- **远程访问**：默认只接受本机访问；设置 `ADMIN_AUTH_ENABLED=true` 与 `ADMIN_PASSWORD`，并让 `LISTEN_ADDR` 监听对外地址后，可从其他设备登录；登录连续失败 5 次后，60 秒内的登录返回 429 并带 `Retry-After`
- **反向代理**：代理需把浏览器请求的原始 `Host` 头透传给服务，nginx 写 `proxy_set_header Host $host;`
- **设置生效**：设置页修改的配置写回 `.env`；监听地址、认证路径与日志格式重启后生效，其余立即生效
- **在线更新**：“关于”页显示 GitHub 最新发行版本与更新内容，命令行版与桌面版确认后下载当前平台的发行包、校验 SHA-256、替换可执行文件并重启到新版本，下载经 `PROXY` 的第一个代理发出，未设置时使用系统代理；Docker 部署随镜像更新

## 使用示例

```bash
curl http://127.0.0.1:8007/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "从 /v1/models 获取的模型 ID",
    "messages": [{"role": "user", "content": "Hello"}],
    "reasoning_effort": "medium",
    "stream": true
  }'
```

OpenAI SDK 的 base URL 使用 `http://127.0.0.1:8007/v1`；Claude 和 Google GenAI SDK 使用 `http://127.0.0.1:8007`。

## 目录结构

```text
cmd/gemini-web2api/  # 程序入口
internal/
  accounts/          # 账号初始化、续签、Cookie 轮换与运行期增删
  adapter/           # OpenAI、Claude、Gemini 协议适配
  admin/             # 管理端接口
  app/               # 配置、实例锁与服务生命周期
  auth/              # 账号状态加载与写回
  balancer/          # 多账户调度
  chromeauth/        # Chrome 与 Cookie 账号导入
  claude/            # Claude 协议类型与流式事件
  config/            # 服务配置
  desktop/           # 桌面窗口与托盘
  gemini/            # Gemini Web 协议客户端
  logging/           # 运行日志与请求追踪
  pricing/           # 等价 API 单价
  requestdb/         # 请求账本
  streamio/          # HTTP 流刷新
  tokencount/        # Gemini tokenizer 本地计数
  webui/             # 内嵌的管理端页面
web/                 # 管理端前端源码
build/               # 桌面版图标与打包配置
```

## 环境变量

| 变量 | 说明 | 默认值 |
| --- | --- | --- |
| `GEMINI_SAVE_HISTORY` | 是否写入 Gemini 官网历史记录 | `false` |
| `GEMINI_AUTH_STATES` | 认证文件或目录，多个路径用逗号分隔 | `auth` |
| `LISTEN_ADDR` | 服务监听地址 | `127.0.0.1:8007` |
| `PROXY_API_KEY` | 本地 2API 访问密钥，多个 key 用逗号分隔 | 空 |
| `PROXY` | 代理池，多个代理用逗号分隔 | 空 |
| `MODEL_MAPPING` | 外部模型名到当前模型 ID 的映射 | 空 |
| `ACCOUNT_COOLDOWN` | 账号失败后的冷却时间 | `2m` |
| `SESSION_TTL` | 显式会话状态的存活时间 | `30m` |
| `INIT_TIMEOUT` | 单账号启动初始化超时 | `20s` |
| `PER_ACCOUNT_CONCURRENCY` | 每个账号同时进行的上游请求数 | `1` |
| `ROUTING_STRATEGY` | 无会话请求的调度策略 | `round-robin` |
| `LOG_LEVEL` | 日志级别：`debug`、`info`、`warn`、`error` | `info` |
| `LOG_FORMAT` | 控制台日志格式：`text` 或 `json` | `text` |
| `REQUEST_BODY_LOG` | 是否在请求账本中保存最近 1000 条请求与响应正文 | `false` |
| `LOG_FILE` | 同时把日志写入 `data/logs/gemini-web2api.log`，单文件 10 MB 轮转 | `true` |
| `LOG_MAX_TOTAL_MB` | 日志目录总量上限（MB），超出时删除最旧的文件，不小于 20 | `500` |
| `ADMIN_AUTH_ENABLED` | 管理端登录 | `false` |
| `ADMIN_USERNAME` | 管理端用户名 | `admin` |
| `ADMIN_PASSWORD` | 管理端密码 | 空 |

`MODEL_MAPPING` 的格式为 `别名:模型 ID`，多项用逗号分隔。启动初始化失败的账号按 `ACCOUNT_COOLDOWN` 间隔重试。`ROUTING_STRATEGY` 为 `round-robin` 时从随机位置开始在账号间轮流分配，为 `fill-first` 时优先使用排在前面的空闲账号。

命令行 `--auth` 和 `--listen` 可以覆盖认证路径与监听地址；`--open-ui` 控制启动后是否在浏览器中打开管理端，不带参数启动时默认打开。

## 故障排除

### Windows 端口被系统保留

如果启动时提示 `LISTEN_ADDR` 配置的端口被占用，任务管理器中又找不到占用进程，可能是 Hyper-V、WSL2 或 Docker 的 NAT 服务保留了端口段。

以下命令需要在管理员权限的 PowerShell 或 CMD 中运行。

```powershell
netsh interface ipv4 show excludedportrange protocol=tcp
```

如果 `8007` 落在输出的 `Start Port` 和 `End Port` 范围内，可以修改 `LISTEN_ADDR`，或重启 WinNAT 服务后再次检查：

```powershell
net stop winnat
net start winnat
```

端口空闲后，也可以将 `8007` 加入持久保留：

```powershell
netsh int ipv4 add excludedportrange protocol=tcp startport=8007 numberofports=1 store=persistent
```

## 社区

感谢 [LINUX DO](https://linux.do/) 提供开源交流社区，欢迎佬友交流使用体验与改进建议。

## 注意

Gemini Web 内部协议可能随官网更新，模型 ID 以服务启动后返回的动态目录为准。欢迎提 Issue 和 PR。

开发与贡献见 [docs/development.md](docs/development.md)，认证、请求载荷和流式解码原理见 [docs/protocol.md](docs/protocol.md)，日志与请求账本见 [docs/logging.md](docs/logging.md)。
