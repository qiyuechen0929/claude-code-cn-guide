# Claude Code 中国大陆使用指南

> 从零开始，在大陆网络环境下用上 Claude：终端版 Claude Code 写代码、桌面版 Claude Desktop 做助手，官方直连与第三方模型接入全覆盖，一册搞定。

- **作者**：陈启粤
- **最后更新**：2026-08-09
- **适用读者**：中国大陆开发者（尤其想低成本、直连使用 Claude 的用户）
- **阅读建议**：先看"快速选型"判断走哪条路，再按需进入**终端版区**或**桌面版区**精读对应章节。

---

## 快速选型：你该用哪个版本、哪条路？

Claude 有两个官方客户端形态，账号和订阅**通用**（一个账号两边都能用），但适用场景不同：

| | **终端版 Claude Code** | **桌面版 Claude Desktop** |
|---|---|---|
| 形态 | 命令行 Agent，直接操作代码库 | 图形界面，聊天 + 传文件 |
| 最适合 | 写代码、改代码、跑测试、多文件重构 | 日常问答、写文档、看图片/PDF |
| 安装 | npm（需 Node.js） | 官网下载安装包 |
| 接第三方模型 | 环境变量 / settings.json | 项目 `.env`（沙箱读不到系统环境变量） |
| 看图片 | 纯文本模型需视觉桥 | 官方模型直接支持 |

同时，用官方模型还是第三方模型，也是两条路：

| | 方案 A：官方直连 | 方案 B：接入第三方模型 |
|---|---|---|
| 体验 | 原汁原味 Claude（Opus/Sonnet/Haiku） | DeepSeek / GLM / Kimi / Qwen 等 |
| 网络 | 需要稳定的海外网络（非大陆 IP） | 国内可直接访问 |
| 付费 | 订阅（$20/月起）或 API 按量 | 国内平台按量，性价比高 |
| 门槛 | 海外邮箱 + 支付方式，风控严格 | 注册国内大模型平台，充几块钱即可 |
| 适合谁 | 追求最强能力、能搞定订阅与网络 | 想省心省钱、直连稳定、中文友好 |

**最快跑起来**：注册 DeepSeek/GLM，用 **cc-switch** 一键切换，10 分钟内用上终端版。
**要完整官方体验**：美区 App Store / Google Play 内购订阅，最稳。

> 💡 两条路不冲突，可以**并行**：日常开发用国产模型省钱，复杂任务切回官方 Claude。

---

# 第一篇 · 终端版 Claude Code（写代码主力）

## 1.1 认识终端版 Claude Code

Claude Code 是 Anthropic 出品的**命令行编程 Agent**。它在终端里运行，能直接读写你的代码、执行命令、跑测试，是"让 AI 帮你写代码"的核心工具。

**一个会话是怎么工作的（理解原理，出问题才知道往哪查）：**

1. 你在终端输入 `claude`，它读取配置文件（`settings.json`、`CLAUDE.md`）；
2. 你提需求，Claude Code 把需求 + 上下文发给**背后配置的模型 API**；
3. 模型决定"要做什么"（读文件？改文件？跑命令？），并以**工具调用**的格式返回；
4. Claude Code 负责执行这些操作，把结果再喂回给模型，循环往复；
5. 直到任务完成，或你按 `Ctrl+C` / `/clear` 结束。

**关键点**：第 3 步"工具调用"是否稳定，取决于你接的模型。官方 Claude 最稳定；纯文本的第三方模型（如 DeepSeek 非思考模式）偶尔会把"该调工具"直接输出成文字——**这不是配置坏了，是模型能力差异**。见 [第四篇 FAQ](#第四篇--中国大陆常见问题-faq)。

**核心概念速览：**

| 概念 | 是什么 | 一句话说明 |
|---|---|---|
| **Claude Code** | 命令行编程 Agent | 在终端里用自然语言让它写代码、改文件、跑命令 |
| **MCP** | Model Context Protocol | 让 Claude 安全连接外部工具/数据的"插头标准" |
| **Skill** | 技能包 | 把某个领域的专业工作流打包成"菜谱"，Claude 照着做 |
| **Agent / Subagent** | 子代理 | 把一个大任务拆给多个"小 Claude"并行干 |
| **Token** | 计费单位 | 模型处理文本的最小单位，约等于 1~2 个汉字 |
| **上下文窗口** | 一次对话能记住的内容量 | 越大越能处理长文件、大项目 |
| **cc-switch** | 配置切换工具 | 图形化切换 Claude Code 背后接的模型 |
| **Anthropic 兼容接口** | 各家模型提供的"Claude 格式"API | 有了它，第三方模型能直接当 Claude 用 |

---

## 1.2 安装 Claude Code（终端版）

### 前提准备

| 依赖 | 要求 | 国内获取方式 |
|---|---|---|
| **Node.js** | v18+（建议 v20+） | https://nodejs.org.cn/ 或 https://npmmirror.com/mirrors/node/ 下载 LTS，安装时勾选 "Add to PATH" |
| **Git** | Windows 必需 | `winget install Git.Git` 或 https://git-scm.com/downloads |

### 安装

```bash
# 方式一：官方源直接装（网络好时）
npm install -g @anthropic-ai/claude-code

# 方式二：国内镜像源（推荐）
npm install -g @anthropic-ai/claude-code --registry=https://registry.npmmirror.com
```

### 验证

```bash
claude --version
```

能输出版本号即成功。后续在任意项目目录执行 `claude` 即可启动。

### 常见安装问题

| 问题 | 解法 |
|---|---|
| `EPERM` / 权限报错（Windows） | 用**管理员身份**打开 PowerShell 再执行；或改 npm 全局目录：`npm config set prefix "$env:APPDATA\npm"` |
| npm 很慢 / 超时 | 永久换镜像：`npm config set registry https://registry.npmmirror.com` |
| 找不到 `claude` 命令 | npm 全局 bin 没进 PATH：Windows 加 `%AppData%\npm`，macOS/Linux 检查 `~/.npm-global/bin` 或 `~/.nvm/versions/node/<版本>/bin` |

### 安装完成后做什么？

- 进入你的项目目录，执行 `claude`；
- 首次运行会让选登录方式（官方账号）或走第三方模型（跳过登录，见 [1.4](#14-接入第三方模型国内直连)）；
- 建议先在项目根目录放一份 `CLAUDE.md`（见 [1.5](#15-配置进阶模型--记忆--权限)），让 Claude 懂你的项目。

---

## 1.3 官方直连：订阅与网络

想用官方 Claude 模型，走这一节。

### 订阅档位

| 档位 | 说明 | 官方入口 |
|---|---|---|
| **Pro** | $20/月，适合个人日常 | https://claude.ai/settings/billing |
| **Max** | $100 / $200/月，适合重度使用 | 同上 |
| **Team** | 多人协作，按席位计费 | https://claude.ai/upgrade |

### 中国大陆用户订阅的核心痛点与解法

**1. 支付被拒**：Stripe 风控会拒绝绝大多数中国大陆发行的信用卡（含双币卡）。实测成功率较高的方案：

- ✅ **美区 App Store / Google Play 内购（最稳定）**：苹果/谷歌作为中间支付方，封号风险最低。流程：
  1. 注册美区 Apple ID（地区选美国，无需绑卡）；
  2. 在 App Store 登录美区账号，用**美区礼品卡**充值（买正规渠道，避免黑卡）；
  3. 下载 Claude App，App 内购买 Claude Pro 订阅；
  4. 订阅绑定美区 Apple ID，电脑端登录同一 Claude 账号同样生效。
- ⚠️ **虚拟信用卡**：2025 下半年起 Anthropic 大幅收紧风控，虚拟卡（WildCard、DuPay 等）成功率下降、封卡率升高，新用户不建议。
- ⚠️ **国内全币种信用卡官网直付**：实测基本必被封号，不建议。
- ⚠️ **代充服务**：务必选择"用户ID + 充值码"方式、不需要账号密码的平台，避免黑卡连坐封号。

**2. 账号风控红线**（社区血泪总结）：
- 使用稳定的**独享 IP**（自建 VPS 优于公共机场，多人共用的"脏 IP"是封号主因之一）；
- 注册、登录、支付全程保持**同一固定 IP 和同一地区**，不要频繁跳区；
- 不要用临时邮箱、接码平台注册；支付信息与账单地址保持一致；
- 刚绑卡就大额消费、凌晨高频操作、多人共享账号，都容易触发风控。

**3. 手机验证**：不支持 +86 手机号，需要用海外邮箱（Gmail/ProtonMail）+ 海外接码（如 sms-activate.io，支持支付宝）。

### 网络环境与登录

**地区限制**：Claude 不仅封锁中国大陆 IP，**也不向中国香港、澳门开放**。需要稳定的海外网络（美区、新加坡、英区等）。

**代理工具推荐：**

| 工具 | 平台 | 说明 |
|---|---|---|
| **Clash Verge** | Win/macOS/Linux | 开源，内置 TUN 模式，推荐 |
| **v2rayN** | Windows | 经典，配合 TUN 或系统代理 |
| **Shadowsocks / ClashX** | macOS | Mac 常用 |

**终端版务必开 TUN 模式**：命令行程序不走普通 SOCKS/HTTP 代理，使用 Clash Verge 等代理客户端时，请开启 **TUN/系统代理模式**，否则 `claude` 可能提示网络错误或超时。

> Clash Verge 开启方法：设置 → 打开 **TUN 模式**（需要管理员权限），或在"系统代理"里把代理设为全局。开启后命令行流量也会走代理。

**登录方式**：首次运行 `claude` 会输出一个链接，用浏览器打开并授权登录（需要能访问 claude.ai 的网络环境）。浏览器也要走代理。

### 官方 API 方式（不订阅，按量付费）

- 申请 API Key：https://console.anthropic.com/
- 计费方式：按 token 用量计费，价格见 https://docs.anthropic.com/en/docs/about-claude/pricing
- 需要**海外信用卡**充值；同样受网络与风控限制。
- 适合：有自己业务想调用 Claude、或想精确控制用量的开发者。

---

## 1.4 接入第三方模型（国内直连）

这一节讲怎么把终端版的"大脑"换成国内可直接访问的第三方模型。核心就三个环境变量：

| 环境变量 | 作用 | 示例 |
|---|---|---|
| `ANTHROPIC_BASE_URL` | 把 API 地址指向第三方平台的 Anthropic 兼容接口 | `https://api.deepseek.com/anthropic` |
| `ANTHROPIC_AUTH_TOKEN` | 第三方平台的 API Key | `sk-...` |
| `ANTHROPIC_MODEL` | 指定模型名 | `deepseek-chat` / `glm-4.7` |
| `ANTHROPIC_SMALL_FAST_MODEL` | 指定轻量模型（背景小任务用） | 同 `ANTHROPIC_MODEL` 或更便宜的型号 |

好消息是：**DeepSeek、智谱 GLM 等平台已原生提供 Anthropic 兼容接口**，不需要额外转换。只提供 OpenAI 兼容接口的平台，需要用网关中转（见 1.4.4）。

### 1.4.1 服务商总览与注册入口

| 服务商 | Anthropic 兼容地址 | 推荐模型 | 注册/控制台 | 特点 |
|---|---|---|---|---|
| **DeepSeek** | `https://api.deepseek.com/anthropic` | `deepseek-chat`、`deepseek-reasoner` | https://platform.deepseek.com/ | 性价比之王，中文好，兼容接口稳定 |
| **智谱 GLM** | `https://open.bigmodel.cn/api/anthropic` | `glm-4.7`、`glm-4.7-thinking` | https://open.bigmodel.cn/ | 编码能力强，兼容接口完善 |
| **Kimi（月之暗面）** | `https://api.moonshot.cn/anthropic` | `kimi-k2-0711-preview` 等 | https://platform.moonshot.cn/ | 长上下文，中文强 |
| **通义 Qwen（阿里云）** | 需用 DashScope OpenAI 兼容接口 + 网关 | `qwen3-coder` 等 | https://dashscope.aliyun.com/ | 有免费额度，需网关转换 |
| **MiniMax** | `https://api.minimaxi.com/anthropic` | `MiniMax-Text-01` 等 | https://platform.minimaxi.com/ | 性价比高 |
| **SiliconFlow 硅基流动** | 仅 OpenAI 兼容，需网关 | `Qwen/Qwen3-Coder` 等 | https://siliconflow.cn/ | 聚合多家开源模型 |

> ⚠️ 模型名随时会更新，注册后在控制台的"模型列表/文档"里确认当前可用的模型名，再填到 `ANTHROPIC_MODEL`。

**各服务商怎么选（结合国内开发者实际）：**

| 场景 | 推荐 | 理由 |
|---|---|---|
| 日常开发、性价比优先 | **DeepSeek `deepseek-chat`** | 价格极低、中文好、兼容接口稳 |
| 复杂推理、长链路任务 | **DeepSeek `deepseek-reasoner`** 或 **GLM 思考模型** | 推理更强，适合重构、debug |
| 长上下文、长文档处理 | **Kimi K2** | 上下文窗口大，中文长文强 |
| 写前端/UI、快速迭代 | **GLM-4.x** / **Qwen-Coder** | 编码指令遵循好 |
| 想免费试水 | **阿里云百炼 Qwen** | 新用户常送免费额度 |
| 本地私有化、离线 | **Ollama + 开源模型** | 数据不出本机，见 1.4.5 |

> 💡 **小贴士**：把 `ANTHROPIC_MODEL` 设成你的"主力模型"，把 `ANTHROPIC_SMALL_FAST_MODEL` 设成更便宜/更快的型号（如 `deepseek-chat`），Claude Code 会在后台小任务（生成标题、简短摘要等）上自动用便宜模型，省不少钱。

### 1.4.2 方案一：环境变量直连（最简单，推荐先试）

**Windows（PowerShell，临时生效）：**

```powershell
$env:ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
$env:ANTHROPIC_AUTH_TOKEN="你的DeepSeek-API-Key"
$env:ANTHROPIC_MODEL="deepseek-chat"
$env:ANTHROPIC_SMALL_FAST_MODEL="deepseek-chat"
claude
```

**Windows（永久生效）：** 设置 → 系统 → 高级系统设置 → 环境变量，新建上面 4 个用户变量。

**macOS / Linux（临时生效）：**

```bash
export ANTHROPIC_BASE_URL="https://api.deepseek.com/anthropic"
export ANTHROPIC_AUTH_TOKEN="你的DeepSeek-API-Key"
export ANTHROPIC_MODEL="deepseek-chat"
export ANTHROPIC_SMALL_FAST_MODEL="deepseek-chat"
claude
```

**macOS 注意**：默认终端是 zsh，永久生效要写入 `~/.zshrc`（不是 `~/.bashrc`）。

**推荐做法：写入 settings.json（永久、跨终端、不会丢）**

编辑 `C:\Users\<你的用户名>\.claude\settings.json`（Windows）或 `~/.claude/settings.json`（macOS/Linux）：

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "你的DeepSeek-API-Key",
    "ANTHROPIC_MODEL": "deepseek-chat",
    "ANTHROPIC_SMALL_FAST_MODEL": "deepseek-chat"
  }
}
```

保存后重启 `claude` 生效。换服务商时改这一个文件即可。

> 🔑 在哪里拿 API Key？登录各平台控制台后，在"API Keys / API 密钥"页面创建。各平台入口见 1.4.1 表格。

### 1.4.3 方案二：cc-switch 图形化一键切换（新手最省心）

**CC Switch** 是免费开源的跨平台桌面工具，可视化统一管理 Claude Code / Codex / Gemini CLI 等工具的配置，内置 50+ 供应商预设，一键切换、托盘快捷切换。

- 项目主页 / 下载：https://github.com/farion1231/cc-switch
- Windows 下载 `.msi` 安装包，Mac 用 `brew install --cask cc-switch`

**使用步骤：**

1. 打开 CC Switch → 选择 **Claude**；
2. 添加供应商 → 选择预设（如 **DeepSeek**）→ 填入 API Key → 保存；
3. 点"启用/切换"，它会自动帮你写好 `settings.json`，无需手改；
4. 在系统托盘可以一键在多个供应商间切换。

> 它的本质就是把 1.4.2 里的环境变量配置用图形界面替你管理了，原理完全一致。

### 1.4.4 方案三：网关中转（LiteLLM / claude-code-router）

适合：平台只提供 **OpenAI 兼容接口**（如阿里云 DashScope、硅基流动、Ollama 本地模型），需要把 Claude Code 的 Anthropic 请求格式转成 OpenAI 格式。

**用 LiteLLM Proxy：**

```bash
pip install 'litellm[proxy]' --break-system-packages
```

写 `config.yaml`，把模型映射到各家 OpenAI 兼容接口：

```yaml
model_list:
  - model_name: claude-model          # 给 Claude Code 看的名字
    litellm_params:
      model: openai/qwen3-coder       # 实际模型
      api_base: https://dashscope.aliyuncs.com/compatible-mode/v1
      api_key: sk-你的DashScope-Key
```

启动代理：

```bash
litellm --config config.yaml --port 4000
```

然后配置 Claude Code 指向本地网关：

```bash
export ANTHROPIC_BASE_URL="http://localhost:4000"
export ANTHROPIC_AUTH_TOKEN="sk-任意"
export ANTHROPIC_MODEL="claude-model"
```

> 参考：LiteLLM 文档 https://docs.litellm.ai/ ；另一常用工具 `claude-code-router`：https://github.com/musistudio/claude-code-router

### 1.4.5 方案四：中转站接入（用官方 Claude 模型）

如果既想用官方 Claude 模型，又不想折腾海外订阅，可以找国内 Anthropic 兼容中转服务，把 `ANTHROPIC_BASE_URL` 指向中转站、`ANTHROPIC_AUTH_TOKEN` 填中转站 Key 即可，配置方法与 1.4.2 完全一样，只是 URL 换成中转站给的地址。

**选择中转站务必核对的四件事：**

1. 是否**官方 `/v1/messages` 接口**（逆向反代不支持 Prompt Cache，长期成本高）；
2. 是否**透传 Prompt Cache**（决定真实 token 成本，差距可达 5–10 倍）；
3. **汇率/计费倍率**是否透明合理；
4. 节点是否稳定、是否支持退款、运营时间多久。

> ⚠️ 安全提醒：中转站会经手你的代码与 Key，优先选择知名、运营久、有口碑的服务商，先小额充值试用。也可以自建（如 Cloudflare Worker 反代），但反代必须**按字节流转发请求体**，禁止解析重组 JSON，否则会破坏 cache 哈希导致全部缓存 miss。

### 1.4.6 方案五：本地模型（Ollama，免费 + 离线）

适合：对隐私敏感、想完全离线、或想零成本尝试的用户。用 **Ollama** 在本地跑开源模型，数据不出本机。

1. **安装 Ollama**：https://ollama.com/ 下载对应系统版本；
2. **拉取模型**：`ollama pull qwen2.5-coder:14b`（或 `deepseek-coder`、`qwen3` 等，看 https://ollama.com/library）；
3. **验证本地服务**：`curl http://localhost:11434/api/tags` 能返回模型列表即可；
4. **配置 Claude Code 指向本地**：

   ```bash
   export ANTHROPIC_BASE_URL="http://localhost:11434/v1"   # Ollama 的 OpenAI 兼容端点
   export ANTHROPIC_AUTH_TOKEN="ollama"                     # 本地无鉴权，填任意值
   export ANTHROPIC_MODEL="qwen2.5-coder:14b"
   ```

> ⚠️ 本地小模型（7B/14B）能力有限，工具调用不稳定是正常现象；至少 32B 以上才有可用体验，且需要较好显卡。适合把这里当"备用通道"，不适合当主力。

### 1.4.7 五种接入方式对比

| 方式 | 需要 | 难度 | 成本 | 备注 |
|---|---|---|---|---|
| 环境变量直连 | 国内大模型平台 Key | ⭐ | 按量，低 | 最简单，推荐先试 |
| cc-switch 图形化 | 同上 | ⭐ | 按量，低 | 省心，管理多个供应商 |
| 网关中转 | OpenAI 兼容接口 + 网关 | ⭐⭐⭐ | 按量 | 只针对无 Anthropic 兼容接口的平台 |
| 中转站 | 中转站 Key | ⭐⭐ | 有溢价 | 用官方模型，需仔细评估渠道 |
| 本地 Ollama | 本地算力 | ⭐⭐ | 免费（电费） | 离线隐私，模型能力有限 |

### 1.4.8 接入后怎么验证成功？

1. **看版本信息**：启动 `claude`，欢迎语会显示当前模型名。或输入 `/model` 查看；
2. **发一条指令**：随便说一句"用一句话介绍你自己"，能正常回复说明 API 通了；
3. **看连接状态**：输入 `/status`，看 base URL、模型、用量是否正常。

**如果报错，对照排查：**

| 报错现象 | 最常见原因 |
|---|---|
| `401` / `AuthenticationError` | `ANTHROPIC_AUTH_TOKEN` 填错或没填 |
| `404` / `Not Found` | `ANTHROPIC_BASE_URL` 拼错（多 `/`、少 `/`、没带 `/anthropic` 等） |
| `400` / `Model not found` | `ANTHROPIC_MODEL` 填的模型名不在该平台列表里 |
| 一直转圈 / 超时 | 网络问题，或没开 TUN；尝试调大 `API_TIMEOUT_MS` |
| `429` / `rate limit` | 平台限流：等一会儿、换低价时段、或换模型 |

> 各平台的完整 API 文档：DeepSeek https://api-docs.deepseek.com/ 、智谱 https://open.bigmodel.cn/dev/api 、Kimi https://platform.moonshot.cn/docs/ 、阿里云百炼 https://help.aliyun.com/zh/model-studio/ 。

---

## 1.5 配置进阶：模型 / 记忆 / 权限

### 1.5.1 三个核心配置文件

| 文件 | 路径（Windows） | 作用 |
|---|---|---|
| `settings.json` | `C:\Users\<用户名>\.claude\settings.json` | 全局设置（环境变量、模型、权限、MCP） |
| `CLAUDE.md` | 项目根目录（随项目提交 git） | 项目级指令，告诉 Claude 项目背景、规范、常用命令 |
| `~/.claude/CLAUDE.md` | `C:\Users\<用户名>\.claude\CLAUDE.md` | 个人全局偏好，所有项目生效 |

> 优先级（从高到低）：项目级 `CLAUDE.md` > 个人 `~/.claude/CLAUDE.md` > `settings.json` 里的指令。`CLAUDE.md` 建议控制在 200 行以内，只写 Claude 无法从代码推断的信息。

### 1.5.2 模型相关配置（完整版）

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "你的API-Key",
    "ANTHROPIC_MODEL": "deepseek-chat",
    "ANTHROPIC_SMALL_FAST_MODEL": "deepseek-chat",
    "API_TIMEOUT_MS": "3000000",
    "CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC": "1",
    "CLAUDE_CODE_MAX_OUTPUT_TOKENS": "8192",
    "DISABLE_TELEMETRY": "1"
  }
}
```

**常用环境变量完整清单：**

| 变量 | 说明 |
|---|---|
| `ANTHROPIC_API_KEY` | 官方 API Key（用第三方模型时改为 `ANTHROPIC_AUTH_TOKEN`） |
| `ANTHROPIC_AUTH_TOKEN` | 第三方平台/中转站的 Key，等效替代 `ANTHROPIC_API_KEY` |
| `ANTHROPIC_BASE_URL` | API 地址（核心） |
| `ANTHROPIC_MODEL` | 主力模型名 |
| `ANTHROPIC_SMALL_FAST_MODEL` | 轻量/便宜模型名，跑背景小任务 |
| `API_TIMEOUT_MS` | 请求超时（毫秒），国内建议 3000000 |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | 关闭非必要流量 |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 限制单次输出最大 token |
| `DISABLE_TELEMETRY` / `CLAUDE_CODE_DISABLE_TELEMETRY` | 关闭遥测 |
| `HTTPS_PROXY` / `HTTP_PROXY` / `NO_PROXY` | 走代理 / 绕过代理 |
| `TERM` | 终端类型，渲染异常时设为 `xterm-256color` |

### 1.5.3 权限模式

Claude Code 默认会询问每个可能修改系统的操作，也可以设置更自由的模式：

```bash
# 启动时直接允许所有工具（信任该目录时用）
claude --dangerously-skip-permissions
```

- 交互中按 **Shift+Tab** 循环切换权限模式（默认/自动接受/全放开）。
- 在 `settings.json` 中用 `permissions.allow` / `permissions.deny` 精确控制：

```json
{
  "permissions": {
    "allow": ["Bash(npm run dev)", "Read"],
    "deny": ["Bash(rm -rf *)"]
  }
}
```

> 权限规则里 `Bash(命令)` 支持前缀匹配，如 `Bash(npm *)` 表示所有 npm 命令免确认。**永远不要把 `--dangerously-skip-permissions` 用在不信任的项目上。**

### 1.5.4 常用斜杠命令

在交互界面输入 `/help` 查看全部命令。常用：

| 命令 | 作用 |
|---|---|
| `/model` | 查看/切换当前模型 |
| `/clear` | 清空当前对话上下文 |
| `/compact` | 压缩长对话（省 token） |
| `/config` | 打开配置文件 |
| `/mcp` | 查看/管理 MCP 连接 |
| `/status` | 查看连接与用量状态 |
| `/context` | 查看当前上下文占用情况 |
| `/rewind` | 回退到之前的对话状态 |
| `/resume` | 恢复之前被打断的会话 |
| `/permissions` | 查看/修改权限设置 |
| `/doctor` | 检查环境与配置是否有问题 |
| `claude --version` / `claude update` | 版本 / 升级 |

### 1.5.5 与 IDE 集成

| 编辑器 | 集成方式 |
|---|---|
| **VS Code / Cursor** | 官方扩展：扩展市场搜 "Claude Code"，或命令行 `claude --install-vscode` 一键安装 |
| **JetBrains（IDEA/PyCharm 等）** | 官方插件：插件市场搜 "Claude Code" |
| **Neovim / Emacs** | 社区插件，配置较复杂，查官方文档 |

> IDE 集成后，可以直接在编辑器里选中代码 → 让 Claude 解释/重构/补测试，命令面板（Ctrl+Shift+P）里搜 "Claude" 即可。

---

# 第二篇 · 桌面版 Claude Desktop（日常助手）

## 2.1 认识桌面版 Claude Desktop

**Claude Desktop（桌面版）** 是 Claude 的图形界面客户端，官方下载入口：https://claude.ai/download（Windows/macOS）。

**它和终端版的分工：**

| | 终端版 Claude Code | 桌面版 Claude Desktop |
|---|---|---|
| 界面 | 命令行 | 图形界面 |
| 主打 | 写代码、改代码 | 聊天、写文档、看图 |
| 传文件 | 读项目文件 | 拖拽/粘贴图片、PDF、Word、Excel |
| 接第三方模型 | 环境变量/settings.json | 项目 `.env` |

**桌面版能做什么（国内用得最多的几个功能）：**

- **多模态问答**：直接粘贴/拖入图片、PDF、文档，让 Claude 解读（**官方模型自带视觉，无需额外工具**）；
- **长文档写作**：写方案、报告、邮件、翻译，可上传 Word/Excel/PDF 作为上下文；
- **MCP 扩展**：在设置里添加 MCP 服务器，让桌面版也能连 GitHub、Notion、数据库等（见第三篇）；
- **Projects**：把相关文件、指令固定成一个"项目"，每次对话自动带上，适合长期维护的文档/代码；
- **Artifacts**：让 Claude 直接生成可预览的网页、代码、图表等交互产物。

**官方下载与入口：**

- Claude 桌面版：https://claude.ai/download
- Claude 官网（网页版入口）：https://claude.ai
- Claude 桌面版汉化（第三方补丁）：https://github.com/javaht/claude-desktop-zh-cn（给官方桌面版打中文补丁，支持 macOS / Windows）
- Claude 移动端：App Store / Google Play 搜 "Claude"
- Claude 帮助中心：https://support.anthropic.com

---

## 2.2 桌面版安装与汉化

### 安装

1. 访问 https://claude.ai/download 下载 Windows/macOS 安装包；
2. 双击安装，登录你的 Claude 账号即可使用；
3. 官方模型可直接用；想接第三方模型见 [2.4](#24-桌面版接入第三方模型)。

### 汉化（可选）

官方桌面版界面为英文，中文用户可用社区汉化补丁：

- 项目：**Claude Desktop Chinese Patch** https://github.com/javaht/claude-desktop-zh-cn
- 支持 macOS & Windows，按仓库 README 操作（通常是把补丁应用到安装目录或资源文件）。
- ⚠️ 汉化属于第三方修改，升级官方版本后可能需要重新打补丁；介意的话可等官方中文。

---

## 2.3 桌面版订阅与网络（官方模型）

桌面版用官方 Claude 模型，订阅、网络要求和终端版**完全一样**：

- **订阅**：见 [1.3 订阅档位](#13-官方直连订阅与网络)。美区 App Store / Google Play 内购最稳。
- **网络**：桌面版也需要能访问 claude.ai 的网络环境。桌面版走系统网络，**开系统代理/TUN 即可**（桌面应用不像命令行那样必须 TUN，但保持 TUN 更省心）。
- **账号**：与终端版共用同一 Claude 账号与订阅。

> ⚠️ **桌面版的风控提醒**：桌面版登录的账号如果被风控（支付、IP 问题），同样会被封。风控红线见 [1.3](#13-官方直连订阅与网络)。

---

## 2.4 桌面版接入第三方模型

桌面版想用 DeepSeek/GLM 等国产模型，**关键差异**在于：**桌面版的沙箱读不到 Windows 系统环境变量**，所以不能像终端版那样靠系统环境变量，要把配置写进文件。

### 方案一：项目目录 `.env` 文件（推荐，已验证）

在 Claude 能读取到的项目目录里放一个 `.env`，把模型配置写进去：

```
ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
ANTHROPIC_AUTH_TOKEN=你的DeepSeek-API-Key
ANTHROPIC_MODEL=deepseek-chat
ANTHROPIC_SMALL_FAST_MODEL=deepseek-chat
```

> 这是本机多次验证可行的方案：桌面版读不到 Windows 系统环境变量，但能读项目文件夹里的 `.env`。你的**剪贴板图片识别桥**项目正是靠 `.env` 方案打通桌面版的。

### 方案二：`settings.json`

桌面版也读取 `~/.claude/settings.json`（与终端版共享），可以在 `env` 块里写同样的配置：

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "你的DeepSeek-API-Key",
    "ANTHROPIC_MODEL": "deepseek-chat",
    "ANTHROPIC_SMALL_FAST_MODEL": "deepseek-chat"
  }
}
```

改完重启桌面版生效。

### 各平台配置参数速查

| 服务商 | `ANTHROPIC_BASE_URL` | 推荐模型 |
|---|---|---|
| DeepSeek | `https://api.deepseek.com/anthropic` | `deepseek-chat` |
| 智谱 GLM | `https://open.bigmodel.cn/api/anthropic` | `glm-4.7` |
| Kimi | `https://api.moonshot.cn/anthropic` | `kimi-k2-0711-preview` |
| MiniMax | `https://api.minimaxi.com/anthropic` | `MiniMax-Text-01` |
| 中转站 | 以你渠道给的地址为准 | 以渠道为准 |

> 服务商注册、Key 获取、模型选择详见 [1.4.1 服务商总览](#141-服务商总览与注册入口)。

---

## 2.5 桌面版与图片：视觉桥（重要 · 基于真实环境验证）

> 这条来自在本机反复验证的结论，写在这里帮你和同样用纯文本模型的同学排雷。

**问题**：如果你的桌面版走的是 DeepSeek 等**纯文本模型**通道，直接粘贴图片，模型**是看不到的**——因为纯文本模型收不到图片消息，图片会落成临时文件而不是进入对话。

**解决办法（已验证可行）**：用**剪贴板图片识别桥（ClipSight）**，让 Claude 通过视觉 API 读取图片后转成文字再分析。

### 剪贴板图片识别桥项目

- **GitHub 仓库**：https://github.com/qiyuechen0929/ClipSight
- **功能**：监视器脚本抓取剪贴板/临时目录的图片 → 存到 `received/` → 调用视觉模型（如 GLM-4.1V）识别 → 把识别结果给 Claude 分析。
- **支持服务商**：7 家（智谱 GLM / 阿里云 DashScope / OpenAI / Kimi / 硅基流动 / Ollama / 自定义），全走 OpenAI 兼容接口。
- **安装配置**：仓库 README 有详细步骤，含中文配置向导（`setup.bat`）。
- **`.env` 打通桌面版**：该项目用 `.env` 存配置，桌面版也能读到（见 [2.4](#24-桌面版接入第三方模型)）。

**典型工作流：**

1. 截图 / 复制图片到剪贴板；
2. 监视器自动把图片存到 `received/`；
3. 对 Claude 说"识别图片"；
4. 视觉桥调用视觉 API 识别 → 把文字结果给 Claude → Claude 分析回答。

> 若使用官方 Claude 模型（本身具备视觉能力），则不存在此问题，直接粘贴即可。

---

## 2.6 桌面版常见问题（专属）

**Q：桌面版粘贴图片，对方（模型）说看不到？**
A：如果走的是纯文本模型通道（如 DeepSeek），图片不会进入对话。用剪贴板图片识别桥让 Claude 通过视觉 API 读图（见 [2.5](#25-桌面版与图片视觉桥重要--基于真实环境验证)）。官方 Claude 模型无此问题。

**Q：桌面版和终端版配置是分开的吗？**
A：它们共享 `~/.claude/settings.json` 里的核心配置，但**桌面版的沙箱可能读不到 Windows 系统环境变量**，需要把配置写进项目目录的 `.env` 或 `settings.json` 文件里。

**Q：桌面版汉化后打不开 / 闪退？**
A：汉化补丁可能不兼容当前版本。去 https://github.com/javaht/claude-desktop-zh-cn 看 issues 或等更新；卸载补丁恢复原版。

**Q：桌面版一直转圈 / 连不上？**
A：确认网络能访问 claude.ai；若接的第三方模型，确认 `.env` / `settings.json` 配置正确、Key 有效、余额充足。

---

# 第三篇 · 通用：Skill 与 MCP 工具

## 3.1 先分清两个概念

- **MCP = 连接**：让 Claude 安全地访问外部系统（文件、GitHub、数据库、浏览器）。
- **Skill = 技能**：把某个领域的专业工作流打包成"菜谱"，Claude 按步骤执行。

一句话类比：MCP 是厨房里的工具和食材，Skill 是菜谱。两者配合使用。

**MCP 是怎么工作的（10 秒理解）：**

```
Claude 想要 GitHub 的 issue 列表
        │
        ▼
Claude ──(MCP 协议)──▶ MCP Server（GitHub）
        ◀──(返回结果)───
```

MCP Server 是一个独立的小程序，它把外部系统"翻译"成 Claude 能调用的工具。Claude 不直接连 GitHub，而是通过 MCP Server 中间转发。好处：安全（Claude 只能调用你授权的工具）、标准化（各家都按同一协议）。

> 终端版和桌面版都支持 MCP/Skill，配置方式略有差异（终端版用 `claude mcp` 命令，桌面版在设置里添加），但原理与下面的推荐清单通用。

## 3.2 必装 MCP 推荐

| MCP | 用途 | 官方/主页 |
|---|---|---|
| **Firecrawl** | 网页转 Markdown，抓取文档做调研 | https://github.com/mendableai/firecrawl-mcp-server |
| **GitHub** | 直接操作 PR、Issues、仓库搜索 | https://github.com/github/github-mcp-server |
| **Filesystem** | 跨目录访问文件 | https://github.com/modelcontextprotocol/servers |
| **Brave Search** | 联网搜索能力 | https://github.com/modelcontextprotocol/servers/tree/main/src/brave-search |
| **PostgreSQL** | 自然语言查数据库 | https://github.com/modelcontextprotocol/servers/tree/main/src/postgres |
| **Context7** | 注入最新版本文档，减少 API 幻觉 | https://github.com/upstash/context7 |
| **Memory** | 跨会话持久化项目知识 | https://github.com/modelcontextprotocol/servers/tree/main/src/memory |
| **Figma** | 设计稿转代码 | https://github.com/modelcontextprotocol/servers |

**终端版 MCP 安装命令：**

```bash
# HTTP 远程服务（推荐，2026 主流）
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Stdio 本地服务
claude mcp add --transport stdio github -- npx -y @modelcontextprotocol/server-github

# Windows 特殊写法（需 cmd /c）
claude mcp add --transport stdio my-server -- cmd /c npx -y 某个包

# 管理
claude mcp list
claude mcp get <服务名>
claude mcp remove <服务名>
```

**桌面版 MCP 添加**：设置 → Developer/开发者 → MCP Servers → 添加服务器（填名称 + 命令或 URL），修改后重启桌面版生效。

**作用域**（终端版）：`local`（单项目）< `project`（团队共享，提交到 git 的 `.mcp.json`）< `user`（全局）。优先级 Local > Project > User。

**MCP 选型原则（别贪多）：**

- 问自己："什么操作我在反复复制粘贴/切换标签页？" 每个 MCP 解决一个具体痛点；
- 从 1~2 个开始（比如 GitHub + Filesystem），用顺了再加；
- 装太多 MCP 会占上下文、拖慢响应，还可能互相冲突；
- **安全红线**：PostgreSQL MCP 永远别连生产库；GitHub MCP 可开只读模式。

## 3.3 Skill 推荐

**安装方式：**

- 官方插件市场：`/plugin marketplace add anthropics/skills` 后 `/plugin install` 对应技能；
- 手动：项目级放 `.claude/skills/<skill-name>/SKILL.md`，用户级放 `~/.claude/skills/`；
- npm：`npx skills add webapp-testing -y -g`。

**第一梯队（文档必备）：**

| Skill | 用途 |
|---|---|
| **docx** | 生成/编辑 Word 文档 |
| **xlsx** | Excel 表格处理 |
| **pdf** | PDF 生成/读取/处理 |
| **data-analysis** | 数据分析 |

**第二梯队（开发提效）：**

| Skill | 用途 |
|---|---|
| **frontend-design** | 高质量前端 UI 生成 |
| **refactor** | 多文件安全重构 |
| **pr-review / code-review** | 代码审查 |
| **security-review** | 安全审计 |
| **webapp-testing** | Web 应用 E2E 测试 |
| **systematic-debugging** | 结构化 bug 排查 |
| **skill-creator** | 制作自己的技能 |

**成体系的技能集合（GitHub 仓库）：**

- **claude-toolbox**：11 个工作流技能，形成完整开发流水线（design → review → implement → test）。https://github.com/serpro69/claude-toolbox
- **@code-whisperer/skills**：18 个实战技能 + 5 个 CLAUDE.md 模板（feature-team、security-team、audit-swarm 等）。https://www.npmjs.com/package/@code-whisperer/skills
- **obra/superpowers**：著名技能集合，含 brainstorming、planning、TDD 等。https://github.com/obra/superpowers

## 3.4 手写一个自己的 Skill（5 分钟上手）

Skill 就是一个文件夹 + 一个 `SKILL.md`，结构如下：

```
~/.claude/skills/my-skill/
└── SKILL.md          # 技能说明（必须）
└── reference.md      # 可选的补充资料，被 SKILL.md 引用
└── scripts/          # 可选的脚本/模板
```

`SKILL.md` 示例：

```markdown
---
name: 安全与规范审查
description: 对提交的代码进行全面的安全漏洞排查和格式校验
triggers:
  - 审查变更代码
  - 执行安全排查
allowed-tools:
  - Read
  - Bash(git diff HEAD)
---

# 安全与规范审查

## 步骤
1. 运行 `git diff HEAD` 获取变更；
2. 按以下清单逐项检查：注入风险、硬编码密钥、依赖漏洞、格式规范；
3. 输出报告：问题列表 + 严重程度 + 修复建议。
```

- `description` 写清楚"什么时候用"，Claude 会根据它自动判断是否调用；
- `triggers` 写触发词，出现这些词时优先使用；
- 正文用 Markdown 写步骤和输出格式，越具体越好；
- 复杂技能用 `@reference.md` 引用外部规则，省对话内存。

## 3.5 配置 CLAUDE.md 的最佳实践

- 只写 Claude 无法从代码推断的信息：项目目标、目录结构约定、构建/测试命令、编码规范、常见坑。
- 用 `@path/to/file` 引用外部文档，避免把 CLAUDE.md 撑爆。
- 示例：

```markdown
# 项目说明
- 本仓库是 xxx 前端项目，基于 React 18 + Vite。
- 开发：npm run dev | 构建：npm run build | 测试：npm test
- 提交规范：遵循 Conventional Commits（feat:/fix:/docs:/chore:）。
- 坑：CI 里别用 npm ci，依赖有本地补丁，用 npm install。
```

**四个作用域（优先级从高到低）：**

| 作用域 | 位置 | 说明 |
|---|---|---|
| 项目级 | `./CLAUDE.md` | 随仓库走，团队共享 |
| 个人全局 | `~/.claude/CLAUDE.md` | 所有项目生效的个人偏好 |
| 本地个人 | `./CLAUDE.local.md` | 仅本机生效，不提交 git |
| 企业级 | `~/.claude/` 配置 | 组织统一规定 |

> 推荐控制在 200 行以内。写得太多反而稀释重点。

## 3.6 其他实用工具

| 工具 | 用途 | 地址 |
|---|---|---|
| **CC Switch** | 国内模型一键切换 | https://github.com/farion1231/cc-switch |
| **ccusage / ccflare** | 用量与成本分析 | https://github.com/SteveBeynon/claude-code-usage |
| **Claude Squad** | 多实例管理 | https://github.com/smtg-ai/claude-squad |
| **claude-code-router** | 请求路由（多模型分流） | https://github.com/musistudio/claude-code-router |
| **剪贴板图片识别桥（ClipSight）** | 桌面版纯文本模型读图 | https://github.com/qiyuechen0929/vision-bridge |

---

# 第四篇 · 中国大陆常见问题 FAQ

## 4.1 安装类（终端版）

**Q：`npm install` 很慢或超时怎么办？**
A：使用国内镜像源：`npm install -g @anthropic-ai/claude-code --registry=https://registry.npmmirror.com`，或永久设置 `npm config set registry https://registry.npmmirror.com`。

**Q：提示找不到 `claude` 命令？**
A：npm 全局 bin 目录没进 PATH。Windows 上把 `%AppData%\npm`（或安装 Node 时的 npm 全局目录）加入 PATH；macOS/Linux 检查 `~/.npm-global/bin` 或 `~/.nvm/versions/node/<版本>/bin`。重启终端再试。

**Q：`claude` 启动报 Node 版本过低？**
A：Claude Code 需要 Node.js v18+（建议 v20+）。升级 Node：Windows 重新从 https://nodejs.org.cn/ 装 LTS，或用 `nvm`（https://github.com/nvm-sh/nvm）。

## 4.2 网络类

**Q：`claude` 提示网络错误 / 一直转圈 / 401？**
A：按顺序排查：
1. 是否开了代理但没开 **TUN/系统代理模式**（终端程序不走普通 SOCKS）；
2. 若是接入第三方模型，`ANTHROPIC_BASE_URL` 是否填对（各平台给的地址要完整，如 DeepSeek 就是 `https://api.deepseek.com/anthropic`，见 1.4.1 表格）；
3. `ANTHROPIC_AUTH_TOKEN` 是否填的是平台 API Key，且余额充足；
4. 把 `API_TIMEOUT_MS` 调大（如 `3000000`）。

**Q：官方直连老被封号 / 不稳？**
A：用稳定独享 IP、全程固定同一地区、避免公共机场"脏 IP"、支付与注册信息一致。核心还是解决支付环节，详见 [1.3](#13-官方直连订阅与网络)。

**Q：公司内网有 HTTP 代理，Claude Code 走代理吗？**
A：Claude Code 默认读取系统代理。若代理导致连不上，可设置 `NO_PROXY` 排除目标域名，或把代理地址填进环境变量 `HTTPS_PROXY` / `HTTP_PROXY`。注意部分国内 API 域名需要绕过代理直连。

## 4.3 模型与配置类

**Q：接入第三方模型后，怎么确认生效了？**
A：启动 `claude` 后输入 `/status` 或 `/model` 查看当前模型与连接状态；也可以看启动欢迎语里显示的模型名。

**Q：`ANTHROPIC_MODEL` 填什么名字？**
A：填对应平台"模型列表/API 文档"里的**模型标识**（如 `deepseek-chat`、`glm-4.7`）。不是平台显示名称。各平台文档：DeepSeek https://api-docs.deepseek.com/ 、智谱 https://open.bigmodel.cn/dev/api 、Kimi https://platform.moonshot.cn/docs/ 、阿里云 DashScope https://help.aliyun.com/zh/model-studio/ 。

**Q：接入第三方模型后，代码能力/工具调用不稳定怎么办？**
A：这是**模型差异**，不是配置坏了。不同模型对"调用工具"的遵循程度不同（纯文本模型偶尔会把该调工具的操作输出成文字）。对策：描述需求时明确"用工具去查/去改"，分步骤下达指令；或换编码更强的模型（GLM-4.x 系列、Kimi 编码版）。

**Q：settings.json 里同时配了官方和第三方，怎么切换？**
A：用 cc-switch 一键切换最方便；手改的话就是改 `settings.json` 里 `env` 块的 4 个变量，改完重启 `claude`。

## 4.4 账号与安全类

**Q：用第三方模型还需要登录 Claude 账号吗？**
A：不需要。Claude Code 登录官方账号是为了用官方模型；走第三方模型时，凭据就是平台的 API Key，可以跳过官方登录。

**Q：API Key 泄露了怎么办？**
A：立即去对应平台控制台删除/重建 Key。不要把 Key 写进会提交 git 的文件（如 CLAUDE.md），用 `settings.json` 或 `.env`，并确保 `.gitignore` 排除 `.env`。

**Q：用中转站安全吗？**
A：有一定风险（代码经手第三方 + Key 依赖对方）。优先知名渠道、小额试用、不传敏感项目。详见 [1.4.5](#145-方案四中转站接入用官方-claude-模型)。

**Q：我的代码会发给模型厂商吗？**
A：会。无论官方还是第三方，Claude Code 都会把项目文件（上下文里的部分）发给模型 API 处理。**敏感/保密项目不要用公共模型**，或用本地 Ollama（[1.4.6](#146-方案五本地模型ollama免费--离线)）私有部署。官方默认也有数据保留政策，可在官方控制台关闭数据用于训练。

## 4.5 MCP / 插件类

**Q：`claude mcp add` 报错 / MCP 连不上？**
A：1) 确认包已安装（`npx` 首次运行会下载，可能较慢）；2) 确认路径正确，Windows 用 `cmd /c` 写法；3) `claude mcp list` 看是否注册成功；4) 换 HTTP transport 的远程 MCP 更省事；5) 看 `/mcp` 里的报错日志。

**Q：装了 MCP 后 Claude 变笨/变慢？**
A：MCP 工具会占用上下文、增加决策负担。只保留常用的，把不常用的移除或用作用域隔离。

**Q：MCP 和 Skill 我该先学哪个？**
A：从 Skill 开始——零配置、即装即用（文档类 docx/pdf/xlsx 最实用）；需要连外部系统（GitHub/数据库/浏览器）时再上 MCP。

## 4.6 疑难杂症

**Q：`claude` 打开后一直显示"loading"或白屏（终端 UI 异常）？**
A：尝试换一个终端（Windows Terminal / VS Code 集成终端），或设置 `TERM` 环境变量（macOS/Linux：`export TERM=xterm-256color`）。部分终端字体不支持特殊字符也会渲染异常。

**Q：对话太长变慢/费用高？**
A：用 `/compact` 压缩上下文，或 `/clear` 重开。把 `ANTHROPIC_SMALL_FAST_MODEL` 配成便宜模型跑背景任务。

**Q：Claude Code 有更新，怎么升级？**
A：`claude update`（推荐），或重跑 npm 安装命令。

---

# 第五篇 · 官方网址与参考链接汇总

## 官方（Anthropic）

| 资源 | 网址 |
|---|---|
| Claude Code 官方文档 | https://docs.anthropic.com/en/docs/claude-code/overview |
| Claude 官网 | https://claude.ai |
| Claude 桌面版下载 | https://claude.ai/download |
| Claude 帮助中心 | https://support.anthropic.com |
| Anthropic API 控制台 | https://console.anthropic.com/ |
| 官方模型定价 | https://docs.anthropic.com/en/docs/about-claude/pricing |

## 国内大模型平台

| 平台 | 控制台/注册 | API 文档 |
|---|---|---|
| DeepSeek | https://platform.deepseek.com/ | https://api-docs.deepseek.com/ |
| 智谱 GLM | https://open.bigmodel.cn/ | https://open.bigmodel.cn/dev/api |
| Kimi（月之暗面） | https://platform.moonshot.cn/ | https://platform.moonshot.cn/docs/ |
| 阿里云百炼（Qwen） | https://dashscope.aliyun.com/ | https://help.aliyun.com/zh/model-studio/ |
| MiniMax | https://platform.minimaxi.com/ | https://platform.minimaxi.com/document |
| 硅基流动 SiliconFlow | https://siliconflow.cn/ | https://docs.siliconflow.cn/ |

## 常用工具与生态

| 工具 | 地址 |
|---|---|
| CC Switch（模型一键切换） | https://github.com/farion1231/cc-switch |
| Claude 桌面版汉化补丁 | https://github.com/javaht/claude-desktop-zh-cn |
| **剪贴板图片识别桥（ClipSight，你的项目）** | **https://github.com/qiyuechen0929/ClipSight** |
| claude-code-router（请求路由） | https://github.com/musistudio/claude-code-router |
| LiteLLM（网关） | https://github.com/BerriAI/litellm |
| claude-code-usage（用量统计） | https://github.com/SteveBeynon/claude-code-usage |
| claude-squad（多实例管理） | https://github.com/smtg-ai/claude-squad |
| Ollama（本地模型） | https://ollama.com/ |
| MCP 官方服务仓库 | https://github.com/modelcontextprotocol/servers |
| Firecrawl MCP | https://github.com/mendableai/firecrawl-mcp-server |
| GitHub MCP | https://github.com/github/github-mcp-server |
| claude-toolbox（技能集合） | https://github.com/serpro69/claude-toolbox |
| obra/superpowers（技能集合） | https://github.com/obra/superpowers |

## Node.js 与镜像

| 资源 | 地址 |
|---|---|
| Node.js 官网中文镜像 | https://nodejs.org.cn/ |
| npmmirror（淘宝 npm 镜像） | https://npmmirror.com/ |
| Node 镜像下载 | https://npmmirror.com/mirrors/node/ |
| Git for Windows | https://git-scm.com/downloads |

---

## 附录 A：终端版命令行速查

### 安装 / 升级

```bash
npm install -g @anthropic-ai/claude-code --registry=https://registry.npmmirror.com  # 安装（国内镜像）
claude --version       # 查看版本
claude update          # 升级
claude doctor          # 体检：检查环境/配置是否正常
```

### 启动 / 会话

```bash
claude                 # 在当前目录启动
claude "写一个快速排序"  # 直接带初始提示启动
claude -p "命令"        # 非交互单次执行（脚本/CI 用）
claude -c               # 继续上次会话
claude --resume         # 恢复指定会话
claude --dangerously-skip-permissions  # 跳过权限确认（慎用）
```

### 配置 / MCP

```bash
claude config list                    # 查看配置
claude config set <key> <value>       # 设置配置
claude mcp list                       # 列出 MCP
claude mcp add --transport http <名> <url>
claude mcp add --transport stdio <名> -- cmd /c npx -y <包>   # Windows
claude mcp remove <名>
```

### 会话内快捷键

| 快捷键 | 作用 |
|---|---|
| `Shift+Tab` | 循环切换权限模式 |
| `Ctrl+C` | 中断当前任务 |
| `Ctrl+D` | 退出 Claude Code |
| `↑` / `↓` | 历史命令 |
| `Esc` | 取消/返回 |

---

## 附录 B：实战工作流示例

### 场景 1：让 Claude 接手一个陌生项目

```bash
cd 项目目录
claude
```

第一句话这样问最有效：

> "请先阅读 README 和 CLAUDE.md，梳理这个项目的技术栈、目录结构、启动方式，然后列一份 10 行的项目摘要，告诉我应该从哪里开始。"

（Claude 会自动读文件、分析，给你项目概览。）

### 场景 2：修一个 bug

> "运行 `npm test` 把失败的测试列出来，逐个定位根因，修复后重新跑测试确认通过。不要改无关代码。"

要点：**明确命令、明确范围**（"不要改无关代码"），第三方模型尤其吃这一套。

### 场景 3：重构一个模块

> "把 `src/utils/` 下的工具函数按职责拆分成多个文件，保持对外接口不变，更新引用，最后跑一遍测试确认没破坏。先给我看重构方案再动手。"

要点：**先方案后动手**，降低返工。

### 场景 4：写周报 / 技术文档

> "根据这个仓库近一周的 git log，生成一份中文周报，分『新增功能、修复问题、技术债务』三块，语气简洁专业。"

### 场景 5：处理粘贴的图片（桌面版 + 纯文本模型）

1. 截图/复制图片；
2. 用**剪贴板图片识别桥**（https://github.com/qiyuechen0929/ClipSight ）把图片转成文字（见 [2.5](#25-桌面版与图片视觉桥重要--基于真实环境验证)）；
3. 把识别出的文字发给 Claude 分析。

> 提示词技巧汇总：**给上下文、给命令、给范围、给输出格式、先方案后动手**。这五点对任何模型都有效，对国产模型尤其重要。

---

## 附录 C：成本优化指南

第三方模型按 token 计费，以下技巧能大幅省钱：

### 1. 用好 `ANTHROPIC_SMALL_FAST_MODEL`

把主力模型设贵一点（如 `deepseek-reasoner`），轻量模型设便宜（如 `deepseek-chat`）。Claude Code 会在背景小任务自动用便宜模型。

### 2. 及时压缩 / 清理上下文

- `/compact`：压缩长对话（最常用省钱手段）；
- `/clear`：任务完成后重开，别让上下文越滚越大；
- 一个项目一个会话，别混着聊。

### 3. 控制 `CLAUDE.md` 体量

`CLAUDE.md` 每次请求都会带上，写得越长越费 token。控制在 200 行内，用 `@引用` 拆分。

### 4. 选择合适档位的模型

| 任务 | 建议模型档位 |
|---|---|
| 改几行代码、问答 | 便宜模型（deepseek-chat） |
| 重构、复杂 debug、架构设计 | 思考模型（deepseek-reasoner / glm-thinking） |
| 大文件、长文档 | 长上下文模型（Kimi K2） |

### 5. 平台省钱技巧

- 多用各平台**免费额度/新用户优惠**（阿里百炼、SiliconFlow 常送）；
- 关注平台的**低价时段**（部分平台夜间更便宜）；
- DeepSeek 官方价格页：https://api-docs.deepseek.com/quick_start/pricing

---

## 附录 D：安全最佳实践

### 1. 密钥管理

- 不要把 API Key 写进会提交 git 的文件；
- 用 `settings.json` 或 `.env`，`.gitignore` 排除 `.env`；
- 平台控制台支持的话，给 Key 设**额度上限**，防泄露后被盗刷。

### 2. 权限控制

- 不信任的项目别用 `--dangerously-skip-permissions`；
- 用 `permissions.deny` 拦截危险命令（如 `rm -rf`）；
- 定期检查 `/permissions` 里的授权。

### 3. 数据隐私

- 官方模型：在 https://console.anthropic.com/ 的 Settings 里可关闭"数据用于训练"；
- 敏感项目：用本地 Ollama（[1.4.6](#146-方案五本地模型ollama免费--离线)），数据不出机器；
- 中转站：评估服务商信誉，不传高敏项目。

### 4. 供应链安全

- 安装 MCP / Skill 时认准官方或高星仓库，警惕恶意"套壳"插件；
- 不要执行来路不明的 `.sh` / `.ps1` / npm 包；
- 定期 `claude update` 更新到带安全修复的版本。

### 5. 账号安全（官方直连用户）

- 独享 IP + 固定地区；
- 不共享账号；
- 支付信息与注册信息一致；
- 被封号后：联系 https://support.anthropic.com 申诉，同时准备"中转站/第三方模型"的备用通道。

---

> **本文档由陈启粤整理编写，最后更新于 2026-08-09。**
> 如有错误或过时信息，欢迎指正；涉及具体配置请以各官方文档为准。

