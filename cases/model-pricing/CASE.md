# CASE — model-pricing(各家 AI 模型 API 价格)

> 套用 [`../../WORKFLOW.md`](../../WORKFLOW.md)。下面是本 case 的专属实例化。
> 这是工作流的**第一个 case**,方法论最初就是从这里抽象出来的。

## 1. 目标 & 输入
- **目标**:收集各供应商**当下头部文本对话模型**的 API 价格,归一化、可对比、每个数字可溯源官方页。
- **输入**:无(固定范围,见下)。

## 2. 被核验单元
- 一个单元 = 一个 `(provider, model_id)` 的定价。
- 精确身份(R4):精确 API model id(如 `gpt-5.5`、`claude-opus-4-8`、`deepseek-v4-pro`),不用"GPT-5"式泛称;同名多快照价差时注明取哪个。

## 3. 可信来源
- **一手/官方**:各厂商官方定价页 / 官方文档。
- **二手(仅交叉)**:pricepertoken、各类 "LLM pricing" 聚合站。
- 官方域名表(供 reviewer):openai.com·developers.openai.com / anthropic.com·platform.claude.com / ai.google.dev / api-docs.deepseek.com / x.ai·docs.x.ai / mistral.ai / alibabacloud.com·help.aliyun.com / volcengine.com / moonshot.cn·kimi.com / bigmodel.cn·z.ai。

## 4. 产出结构
- `pricing.json`(机器源)+ `pricing.csv`(人读宽表),套通用 schema + 本目录 [`schema.json`](schema.json)(价格专属:input/output/cache_read/cache_write_5m+1h/batch/context_tiers,原值 + usd_per_1m)。
- `verdict`:不使用(数据类 case)。
- 分析层:[`analysis.md`](analysis.md)。可视化:[`index.html`](index.html)(Chart.js 看板)。

## 5. 范围(v1)
- 收:**仅文本对话模型**;每家当下头部(旗舰+主力)。
- 不收:embedding / 图像 / 语音 / 微调 / rerank;**Meta Llama**(无官方 token 计费,价格来自托管方)。

## 6. case 专属口径
- **单位**:统一 USD / 1M tokens(`*_usd_per_1m`),保留原值与币种。
- **汇率**:CNY→USD 用记录在案汇率(当前 1 CNY=0.1477 USD @2026-06-03),写进产出 `assumptions`/`currency_fx`,reviewer 可复算。
- **多维**:input、output、cache_read(命中)、cache_write(注意 Anthropic 分 5m/1h)、batch(~5折)、上下文阶梯(Gemini Pro >200k、Qwen-Max 分档)。
- **国际站 vs 中国站**:国内厂商两套价不同产品不同数字,逐行标 🌏国际站USD / 🇨🇳中国站CNY,缺的标缺口。

## 7. 审计补充
- 价格数字逐字匹配证据(注意 `$5.00`/`¥6.50`/`0.0028` 表记);USD 直录、CNY 按 FX 复算;官方域名校验;新发布型号必联网确认在售(R1)。

## 8. 触发语
- `/factcheck model-pricing`

## 现状
v1 已完成:24 模型 / 21 官方确认 / 3 待核(豆包官方页 JS 渲染未取证)。审计判定 PASS-WITH-ISSUES,见 [`REVIEW_REPORT.md`](REVIEW_REPORT.md)。snapshot 2026-06-04。
