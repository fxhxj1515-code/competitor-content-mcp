# 对标拆解 · 内容获客（MCP Server）

一个**托管式远程 MCP 服务**：让 AI 直接帮你拆解同行账号、挖真实痛点、写口播/短视频脚本。

> Competitor teardown & content studio as a **remote MCP server**.
> Analyze competitor accounts, extract real customer pain points, and generate scripts — right inside your AI client.

---

## 一、它能做什么

四个工具，都是"说一句话就出结果"：

| 工具 | 干什么 | 典型说法 |
|---|---|---|
| `submit_competitor_analysis` | 拆解对标账号：定位、客群、选题套路、变现路径、强弱项（**每条结论都附素材依据**） | 「帮我拆一下这个对标账号，内容我贴在下面」 |
| `submit_pain_points` | 挖真实痛点：按八类焦虑框架产出「用户会说的原话」+ 使用场景 | 「家政行业，想入行的中年女性，给我 30 条真实痛点」 |
| `submit_script` | 出口播/短视频脚本：前 3 秒钩子、分秒脚本、画面、字幕、合规自查 | 「用第二个痛点写一条 30 秒抖音口播脚本」 |
| `get_task` | 取结果（前三个都是异步的，返回任务号后用它取） | —— |

**产出示例**（痛点工具的真实输出）：

> 「客户当着我的面打电话跟中介说我干活不行，我就站在厨房门口，一句话都不敢说」

---

## 二、怎么接入

**服务地址**：`https://kpo.hwzs.club/mcp`（Streamable HTTP）

### 第一步：拿到 API Key

这个服务不自带模型额度 —— 所有 AI 调用都走**你自己的云境链账户**（你自己的 key，费用你自己承担，我们不碰你的密钥）。

还没有 Key 的话，从这里注册获取：

**https://www.yunjinglink.com/normal/login/?registration=1&code=800285**

### 第二步：在你的 MCP 客户端里配置

通用配置片段（大多数 MCP 客户端都支持这种写法）：

```json
{
  "mcpServers": {
    "yunjinglink-content": {
      "type": "streamableHttp",
      "url": "https://kpo.hwzs.club/mcp",
      "headers": { "Authorization": "Bearer 你的云境链APIKey" }
    }
  }
}
```

### 第三步：直接用自然语言

不用记命令，也不用点名工具，直接说你要干什么（见上面那张表里的"典型说法"）。

### 在 WorkBuddy 里接入

WorkBuddy 用户可以直接在**连接器市场**搜「对标拆解 · 内容获客」安装，装完会弹表单让你填 Key。
本仓库的 `connector/` 目录就是提交给它的那份包（`connector-meta.json` / `mcp.json` / `token-schema.json` / `icon.svg` / `skills/`）。

---

## 三、我们怎么处理你的密钥（重要）

- 你的 Key **只在你本机**（客户端配置里），我们**不保存、不落盘、不进日志**
- 请求经过我们服务器时只是**透传**给云境链；我们只记录 Key 的 **SHA256 前 8 位**，用于排查"是不是同一个人的 Key 出问题"
- 任务记录（入参与产出）在我们服务器上**保留 7 天**后自动清理
- 我们**不会**把你的素材或产出拿去当语料、或提供给第三方

---

## 四、常见问题

**Q：为什么每次生成要等一会儿？**
三个 submit 工具都是异步的：提交**立刻**返回任务号（几十毫秒），真正生成在后台跑（通常 20 秒 ~ 2 分钟）。用 `get_task` 取结果，没跑完隔 10 秒再查。

**Q：报"缺少或无效的 API Key"？**
去客户端配置里检查 `Authorization` 那一行，Key 要完整（`sk-` 开头）。重新填一次即可。

**Q：报"额度不足"？**
你的云境链账户余额用完了，去云境链后台充值后重试。

**Q：素材太长会怎样？**
超过上限会被**截断**，并在返回的 `notes` 里明确告诉你"截了什么"。不会静默丢弃。

**Q：为什么不把提示词和语料开源？**
服务端包含我们真实的行业语料与语言参数（这是这个工具"说得像人话"的原因）。仓库只提供**接入配置与工具说明**，服务本身是托管收费能力。

---

## 五、许可与免责

- 本仓库内容（接入配置、工具说明、图标）由**江港科技**提供，**保留所有权利**；欢迎按上面的配置接入使用我们的托管服务。
- 本服务是内容生产辅助工具，**不承诺任何效果**（不保证流量、涨粉、成交）。
- 生成内容发布前请自行核对平台规则与当地法规。

---

## English (short)

**Competitor Teardown & Content Studio** — a hosted **remote MCP server** (`https://kpo.hwzs.club/mcp`, Streamable HTTP) that helps you analyze competitor accounts, extract real pain points, and generate short-video/voice-over scripts.

Four tools: `submit_competitor_analysis`, `submit_pain_points`, `submit_script`, `get_task` (the first three are asynchronous — they return a task id, poll with `get_task`).

You need your own **Yunjinglink API key** (all model calls are billed to your own account; we never store your key — it is only passed through as a `Bearer` header, and only a SHA-256 prefix is logged for troubleshooting).

Configure it in any MCP client:

```json
{ "mcpServers": { "yunjinglink-content": {
  "type": "streamableHttp",
  "url": "https://kpo.hwzs.club/mcp",
  "headers": { "Authorization": "Bearer YOUR_API_KEY" } } } }
```

Contact: 江港科技 (hwzs.club)
