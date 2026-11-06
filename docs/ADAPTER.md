# 数据适配契约

屏幕只读一个允许列表中的 HTTP 端点，例如：

```text
http://127.0.0.1:PORT/api/v1/overview
```

端点返回 JSON。最小快照如下；不带的数据不凭空生成“未接入”卡。

```json
{
  "schemaVersion": 1,
  "generatedAt": "2026-11-07T02:00:00Z",
  "data": {
    "activity": [
      {
        "eventId": "unique-request-id",
        "client": "codex",
        "clientLabel": "Codex",
        "model": "deepseek-v4.1-flash",
        "modelLabel": "DS v4.1 Flash",
        "provider": "workbuddy",
        "providerLabel": "WorkBuddy",
        "startedAt": "2026-11-07T01:59:50Z",
        "tokensPerSecond": 42.5,
        "turnTokens": 128000
      }
    ],
    "models": [
      {
        "sourceId": "my-proxy",
        "model": "deepseek-v4.1-flash",
        "providerId": "workbuddy",
        "inputTokens": 1200000,
        "outputTokens": 30000,
        "requests": 18,
        "stale": false
      }
    ],
    "quotaPools": [
      {
        "id": "my-vibe-plan",
        "sourceId": "vibe",
        "accountAlias": "personal",
        "unit": "EUR",
        "remaining": "116.20",
        "total": "127.50",
        "stale": false,
        "resetsAt": "2026-11-30T00:00:00Z"
      }
    ]
  }
}
```

## 字段语义

| 字段 | 必须 | 说明 |
| --- | --- | --- |
| `schemaVersion` | 是 | 当前为 `1`。不兼容变化应升版本。 |
| `generatedAt` | 是 | 数据实际观测时间，不是屏幕请求时间。 |
| `activity[].eventId` | 是 | 跨轮询保持稳定的请求 ID，用于去重。 |
| `client` / `provider` | 是 | 客户端与模型供应来源必须分开。 |
| `model` | 是 | 原始模型标识；显示名可另给 `modelLabel`。 |
| `startedAt` / `finishedAt` | 建议 | ISO 8601 时间；缺失不代表 0。 |
| `inputTokens` / `outputTokens` | 可选 | 未知就不提供；缓存和推理不要重复加进总 Token。 |
| `sourceId` | 是 | 额度与用量所属账本；不同来源不自动合并。 |
| `stale` | 是 | 旧快照必须显式标记。 |
| `remaining` / `total` / `used` | 按需 | 十进制字符串或数值；缺少分母就不画百分比条。 |
| `windows[].remainingPercent` | 按需 | 仅用于服务确实提供窗口百分比的情况。 |
| `unlimited` | 按需 | 独立布尔值，不能与 `remaining: 0` 混用。 |

## 适配器边界

1. 只读本机回环地址，不返回 Cookie、密钥、对话正文或原始响应。
2. 如果同一请求经过两个反代，必须选择一份权威账本，或在 `models` 中使用同一账本标识，避免重复累计。
3. 活动证据只能来自真实请求或明确的服务状态；不要在每次轮询时把历史请求刷新成“刚刚活动”。
4. 子代理、审查和守护调用应带角色或独立标签。屏幕主任务排行不应仅凭模型名猜测归属。
5. 没有数据的字段应省略；未知、0、旧值和未接入是不同语义。

## 可复用适配思路

- **Magpie**：在 LCD 传输之外，增加一个回环快照端点，或让独立进程读取其请求记录。
- **sub2api**：把账号、额度、请求与模型统计投影为本快照；屏幕只消费，不直接管理账号。
- **自建反代**：增加一个只读适配进程，读取自己的 SQLite/JSONL/本地 API。不要把屏幕私有的路径和密钥写进通用核心。
