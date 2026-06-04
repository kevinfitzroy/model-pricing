# analysis — 价格对比与解读(⚠️ 分析层,非事实层)

> **本文件是解读,不是事实。** 所有结论基于 `pricing.json`(事实层)在 **snapshot 2026-06-04** 的数据。
> 凡涉及 `confidence=needs_verification` 的条目(snapshot 2026-06-04 第三步 review 后:仅剩**全部 Doubao**)**不纳入下列排名**,或单独标注。
> **第三步 review 更新(2026-06-04)**:DeepSeek V4-pro、Mistral Large 3 / Medium 3.5、Qwen3-Max 经独立重抓已升为 `confirmed`,现可纳入排名(详见 §4)。
> 比较口径:**USD / 1M tokens**;Kimi 按 FX 6.77 换算;Qwen/GLM 为**国际站 USD**(与中国站 CNY 不可直接等同)。

## 1. 旗舰档(各家当前主力旗舰),按 input / output(USD/1M)

| 模型 | input | output | 说明 |
|---|---|---|---|
| OpenAI gpt-5.5 | 5.0 | 30.0 | output 最贵的一档 |
| Anthropic claude-opus-4-8 | 5.0 | 25.0 | 含 1M 上下文标准价 |
| Google gemini-3.1-pro | 2.0 | 12.0 | ≤200k 档;>200k 翻倍 |
| xAI grok-4.3 | 1.25 | 2.50 | 旗舰里 output 极低 |
| Zhipu glm-5.1 | 1.4 | 4.4 | 国际站 USD |
| Moonshot kimi-k2.6 | 0.96 | 3.99 | 由 CNY 6.50/27.0 换算 |

**解读**(指回上表):
- 旗舰梯队里,**美系(OpenAI/Anthropic)output 单价显著高于中系与 xAI**:gpt-5.5 output $30 vs grok-4.3 $2.5、glm-5.1 $4.4、kimi $3.99。
- **xAI grok-4.3 是旗舰里 input+output 都最低**的($1.25/$2.5),数据来自官方 models 页(已确认)。
- gemini-3.1-pro 在美系三家里最便宜,但**注意 >200k 上下文价格翻倍**($4/$18),长上下文场景需重算。

## 2. 性价比/轻量档,按 input(USD/1M)

| 模型 | input | output | confidence |
|---|---|---|---|
| DeepSeek v4-flash | 0.14 | 0.28 | confirmed |
| Qwen3.5-flash | 0.10 | 0.40 | confirmed(国际站) |
| Mistral small-4 | 0.10 | 0.30 | confirmed |
| Gemini 3-flash | 0.50 | 3.0 | confirmed |
| Anthropic haiku-4.5 | 1.0 | 5.0 | confirmed |

**解读**:轻量档里 **DeepSeek v4-flash / Qwen3.5-flash / Mistral small-4 同处 $0.1–0.14 输入区间**,是目前最便宜的一档;其中 deepseek-v4-flash 缓存命中价低至 $0.0028(官方),重复上下文场景成本优势极大。

## 3. 缓存与批处理(影响真实成本的杠杆)

- **缓存命中折扣**普遍可观:Anthropic = 0.1x 输入;Gemini ≈ 0.1x;DeepSeek v4-flash 命中价 $0.0028(约 0.02x);Kimi 命中 ¥1.10 vs 未命中 ¥6.50。
- **Anthropic 独有缓存写分档**(5m=1.25x / 1h=2x 输入),长会话要把写入成本算进去。
- **Batch ~50% 折扣**:OpenAI/Anthropic/Google 均有(见 `batch_*`)。xAI 批价本轮未取到。

## 4. 存疑项的 review 处置(2026-06-04 第三步独立重抓后)

**已澄清并升为 confirmed(现可下结论)**:
- **DeepSeek v4-pro $0.435/$0.87 = 常态官方价**(非限时促销)。独立重抓官方页无促销横幅;此前二手"75% off 后涨回 1.74/3.48"系误读——$0.435 本就是促销到期后的官方价。其性价比定位成立:v4-pro 仍属极低价旗舰。
- **Mistral Large 3 $0.5/$1.5 真的低于 Medium 3.5 $1.5/$7.5**(非抓取错位,独立重抓行列正确)。这是 Mistral 对新一代 Large 3 的真实激进定价,**可据此说"Mistral Large 3 在旗舰里极便宜"**。(注:个别二手站对旧快照 'Large 3 2512' 标 $2/$6,以官方现价为准。)
- **Qwen3-Max** 精确阶梯已取到:≤32K $1.2/$6;32K–128K $2.4/$12;128K–252K $3/$15(国际站 USD)。基础档($1.2/$6)可纳入旗舰对比,但**注意长上下文价格逐档跳升**。

**仍不纳入排名**:
- **全部 Doubao**:官方页 JS 渲染,第三步重抓仍未取到官方价,维持二手 + `needs_verification`,**不排名**。

## 5. 方法论提醒

- 中国厂商(Qwen/GLM)此处用**国际站 USD**,**与国内 CNY 实际报价可能差异很大**;要做"国内市场价"对比,需补采中国站 CNY 价后另出一版。
- 所有"谁更便宜"的判断都**只在 snapshot 当日、且仅对 confirmed 项成立**。换日期、换口径(含缓存/批/阶梯)结论都会变。
