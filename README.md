# AI 模型 API 价格快照 · 2026-06-04

> ⚠️ **这是 2026-06-04 当日的快照,不是实时价。** 模型价格变动很快,引用前请回官方页核对。
> 全部价格已归一化到 **USD / 1M tokens**;每个数字都可追溯到官方原文(见 [`evidence/`](evidence/))。

---

## TL;DR(关键结论)

- **旗舰 output 单价,美系最贵**:GPT-5.5 `$30`、Claude Opus 4.8 `$25` ≫ Grok 4.3 `$2.5`、GLM-5.1 `$4.4`、Kimi K2.6 `$3.99`、DeepSeek V4-pro `$0.87`。
- **最便宜的输入档挤在 $0.1–0.14**:DeepSeek V4-flash `$0.14`、Qwen3.5-flash `$0.10`、Mistral Small 4 `$0.10`。
- **xAI Grok 4.3 是旗舰里最便宜的**(`$1.25 / $2.50`);**DeepSeek V4-flash 缓存命中价低到 `$0.0028`**,重复上下文场景碾压。
- **数据可信度**:24 个模型,**21 个官方逐字确认 / 3 个待核**(豆包官方页是 JS 渲染抓不到,如实标缺口,没编)。第三步独立审计判定 **PASS-WITH-ISSUES**。

---

## 旗舰档对比(各家当前旗舰 · USD / 1M tokens)

| 供应商 | 模型 | 输入 | 输出 | 缓存命中 | 口径 |
|---|---|--:|--:|--:|---|
| OpenAI | gpt-5.5 | 5.0 | 30.0 | 0.50 | |
| Anthropic | claude-opus-4-8 | 5.0 | 25.0 | 0.50 | 1M 上下文标准价 |
| Google | gemini-3.1-pro | 2.0 | 12.0 | 0.20 | ≤200k 档;>200k 翻倍 |
| xAI | grok-4.3 | 1.25 | 2.50 | — | cache/batch 官方未列 |
| Zhipu | glm-5.1 | 1.4 | 4.4 | 0.26 | 🌏 国际站 USD |
| Moonshot | kimi-k2.6 | 0.96 | 3.99 | 0.16 | 🇨🇳 由 ¥6.50/¥27 换算 |
| DeepSeek | deepseek-v4-pro | 0.435 | 0.87 | 0.0036 | |
| Mistral | mistral-large-3 | 0.5 | 1.5 | — | 新一代激进低价 |
| Alibaba | qwen3-max | 1.2 | 6.0 | — | 🌏 国际站;≤32k 档,阶梯见下 |
| ByteDance | Doubao-Seed-2.0-pro | 0.47 | 2.36 | — | ⚠️ 二手,官方未证 |

## 性价比 / 轻量档(USD / 1M tokens)

| 供应商 | 模型 | 输入 | 输出 | 缓存命中 |
|---|---|--:|--:|--:|
| DeepSeek | deepseek-v4-flash | 0.14 | 0.28 | 0.0028 |
| Qwen | qwen3.5-flash 🌏 | 0.10 | 0.40 | — |
| Mistral | mistral-small-4 | 0.10 | 0.30 | — |
| Google | gemini-3-flash | 0.50 | 3.0 | 0.05 |
| Zhipu | glm-4.6 🌏 | 0.6 | 2.2 | 0.11 |
| Anthropic | claude-haiku-4-5 | 1.0 | 5.0 | 0.10 |
| OpenAI | gpt-5.4 | 2.50 | 15.0 | 0.25 |

> 🌏 = 厂商**国际站 USD** 价;🇨🇳 = **中国站 CNY** 价(按 1 CNY=0.1477 USD 换算)。二者是不同产品、不同数字 —— 详见下方口径。完整字段(batch 价、上下文阶梯、cache 写价)见 [`pricing.csv`](pricing.csv) / [`pricing.json`](pricing.json)。

---

## 几条值得注意的口径陷阱

- **上下文阶梯**:Gemini 3.1 Pro 超 200k token 输入价翻倍(`$2→$4`,输出 `$12→$18`);Qwen3-Max 三档:≤32k `$1.2/$6`、32k–128k `$2.4/$12`、128k–252k `$3/$15`(国际站)。
- **缓存分读写**:Anthropic 缓存**写**还分 5 分钟(1.25×)/ 1 小时(2×)两档;命中读统一 0.1×输入。
- **Batch ~5 折**:OpenAI / Anthropic / Google 均有批处理半价(xAI 官方页未列具体数值)。
- **分词器**:Claude Opus 4.7+ 换了新分词器,**同样文本可能多吃至多 35% token** —— 单价没涨,但单次请求实际成本可能升高。
- **国内厂商"站"不一致**:Kimi 取中国站 CNY;Qwen / GLM 取国际站 USD(中国站 CNY 价待补);豆包官方价抓不到、暂用二手。要做"国内市场价"横评需另补中国站 CNY 一版。

---

## 数据可信度与方法论 ⭐

这个数据集的重点不是"找到价格",而是**保证每个数字都不是编的**。采用三步法:

1. **定义**([`SPEC.md`](SPEC.md)):先把规则、口径、字段、验收标准钉死。8 条硬规则,核心是"无官方来源不落数""未知显式标记不猜""事实层与分析层物理分开"。
2. **执行**:逐家抓**官方定价页**,把价格原文逐字存进 [`evidence/`](evidence/)(带 URL + 抓取日期),再据此派生结构化数据,每个数字反向引用证据。
3. **审计**([`REVIEW_REPORT.md`](REVIEW_REPORT.md)):独立 agent 跑验收清单 + 重抓 9 家官方页交叉核对。

**第三步审计结论:PASS-WITH-ISSUES** —— 95 个价格数字逐字溯源 100% 命中、归一化全部可复算、官方域名校验全过;唯一的 issue 是 3 个豆包型号官方价抓不到(JS 渲染),已诚实标为缺口,**无幻觉、无静默二选一**。

### 已知缺口(诚实声明)

- **豆包 Doubao**:官方定价页 JS 渲染,WebFetch 取不到表格,现为二手数据(`needs_verification`)。
- **通义 Qwen / 智谱 GLM 的中国站 CNY 价**:同因 JS 渲染未取到,现用国际站 USD 替代,已逐行标注。
- 补这两个缺口需要能渲染 JS 的浏览器/截图工具。

---

## 口径

- **单位**:统一 USD / 1M tokens(`*_usd_per_1m`),同时保留官方原值与币种。
- **汇率**:`1 CNY = 0.1477 USD`(USD/CNY=6.7684 @2026-06-03,来源 exchange-rates.org)—— 这是可复算的换算假设。
- **覆盖范围(v1)**:仅文本对话模型;不含 embedding/图像/语音/微调;Meta Llama 因无官方 token 计费未收。
- **`confidence`**:`confirmed`=官方逐字确认;`needs_verification`=仅二手/有疑点;`unknown`=查不到(留空,未猜)。

## 文件导览

| 文件 | 内容 |
|---|---|
| [`pricing.csv`](pricing.csv) / [`pricing.json`](pricing.json) | 结构化全量数据(每格带证据引用 + confidence) |
| [`evidence/`](evidence/) | 各家官方页逐字原文(事实层,不可改) |
| [`analysis.md`](analysis.md) | 对比与解读(分析层,与事实分开) |
| [`SPEC.md`](SPEC.md) · [`schema.json`](schema.json) · [`REVIEW.md`](REVIEW.md) | 方法论 / 字段字典 / 验收清单 |
| [`REVIEW_REPORT.md`](REVIEW_REPORT.md) | 第三步独立审计报告 |
| [`ROSTER.md`](ROSTER.md) · [`CHANGELOG.md`](CHANGELOG.md) | 模型名册 / 演进留痕 |

**怎么核一个数字**:任取 `pricing.json` 一行 → 看它的 `evidence_ref` → 打开对应 `evidence/<厂商>/2026-06-04.md` → 对照逐字原文与官方 URL。
