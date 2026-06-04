# ROSTER — 纳入的模型名册(v1,文本对话)

> 第二步产出。依据 SPEC R1:名册来自联网检索,**最终以官方定价页抓取确认为准**。
> 下表状态:`candidate` = 搜索发现的候选,价格/精确 id 待官方页核实;`confirmed` = 已抓官方页确认。
> snapshot 起始日期:**2026-06-04**。

下面的"官方定价页"是第二步抓取存证(`evidence/`)的目标 URL。搜索摘要里出现的具体数字**一律不算数**,以官方页为准。

| 供应商 | 纳入模型(精确 id) | 官方页(已抓) | 状态 | 备注 |
|---|---|---|---|---|
| **OpenAI** | gpt-5.5, gpt-5.5-pro, gpt-5.4 | developers.openai.com/api/docs/pricing | ✅ confirmed | openai.com/api/pricing 抓取 403,改 developers 子站 |
| **Anthropic** | claude-opus-4-8, claude-sonnet-4-6, claude-haiku-4-5 | platform.claude.com/.../pricing | ✅ confirmed | 缓存写 5m/1h 已录;4.7+ 新分词器多吃 token |
| **Google Gemini** | gemini-3.1-pro-preview, gemini-3.5-flash, gemini-3-flash-preview | ai.google.dev/gemini-api/docs/pricing | ✅ confirmed | 3.1 Pro >200k 阶梯已录 |
| **DeepSeek** | deepseek-v4-flash, deepseek-v4-pro | api-docs.deepseek.com/quick_start/pricing | ✅ confirmed(第三步独立重抓) | pro $0.435/$0.87 确认为常态价非促销;旧 id 2026-07-24 弃用 |
| **xAI (Grok)** | grok-4.3 | docs.x.ai/docs/models | ✅ in/out confirmed | cached/batch 官方页确无→保持 unknown;x.ai/api 抓取 403 |
| **Mistral** | mistral-large-3, mistral-medium-3.5, mistral-small-4 | mistral.ai/pricing | ✅ confirmed(第三步独立重抓) | Large 真低于 Medium,非错位,属实 |
| **阿里通义 Qwen** | qwen3-max, qwen3.5-flash | alibabacloud.com/help/en/model-studio/models | ✅ confirmed(第三步补全阶梯) | **国际站 USD**;max 精确阶梯已取;中国站 CNY 待补采 |
| **字节豆包 Doubao** | Doubao-Seed-2.0-pro / lite / mini | volcengine(JS 未取证) | ❌ needs_verification | 官方页 JS 渲染,仅二手数据 |
| **月之暗面 Kimi** | kimi-k2.6 | platform.kimi.com/docs/pricing/chat-k26 | ✅ confirmed | **中国站 CNY**,按 FX 换算 |
| **智谱 GLM** | glm-5.1, glm-5, glm-4.6 | docs.z.ai/guides/overview/pricing | ✅ confirmed | **Z.ai 国际站 USD**;旗舰是 5.1 非 4.6;中国站 CNY 待补采 |

## 范围确认(已与用户对齐)

- 仅**文本对话**模型;embedding/图像/语音/微调等不收。
- **Meta Llama 不收**(无官方 token 定价)。
- 国内厂商:**保留 CNY 原值 + 用记录在案汇率换算 USD**,两列并存。

## 待官方页核实的重点疑点

1. 各家"最新旗舰"的**精确 model id 和是否在售**(尤其训练知识里没有的新版本)。
2. DeepSeek V4-pro 的**促销价 vs 原价**、旧 id 弃用时间。
3. Kimi / GLM 的**当前真实型号与价格**(搜索数据存疑、偏旧)。
4. 各家**缓存价、batch 价、上下文阶梯价**是否齐全。
