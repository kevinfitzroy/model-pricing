# REVIEW REPORT — 2026-06-04(第三步 review agent)

## 总判定:**PASS-WITH-ISSUES**

B / C / F 三组(溯源、真实性、独立重抓)**全部 PASS** → 不触发整体 FAIL。
剩余 issue 均为**诚实声明的数据缺口**(Doubao 官方价、国内中国站 CNY 价),已正确标 `needs_verification` / 缺口,**无幻觉、无静默二选一**,属非阻塞性 issue(D 组),故判 PASS-WITH-ISSUES 而非 PASS。

## 统计

- 模型:**24** 个(10 家供应商)。
- 非 null 价格数字:**95** 个(review 前 89,补 Qwen3-Max 精确阶梯后 +6)。
- confidence 分布(review 后):**confirmed 21 / needs_verification 3 / conflict 0 / unknown 0**(needs_verification 由 review 前的 7 降到 3,仅剩 3 个 Doubao)。
- source_type:official 21 / secondary 3(3 个 Doubao)。
- 机器检查:schema 校验、CSV↔JSON 一致、95 数字逐字溯源、归一化复算、官方域名校验 —— **全过**。

## 逐组结果

### A. 结构与 schema — PASS
- **A1** `pricing.json` 经 Draft-07 校验 **0 错误**。
- **A2** `pricing.csv` 24 行 ↔ `pricing.json` 24 模型,逐数字/confidence/币种比对 **0 不一致**。
- **A3** 顶层有 `snapshot_date=2026-06-04`;24 条记录 `fetch_date` 全在。
- **A4** 24 条 `evidence_ref` 指向的文件**全部真实存在**,无死链。

### B. 溯源(对抗幻觉核心)— PASS
- **B1/B2 · 证据存在 + 数字匹配**:对 95 个非 null `original_value` 做证据原文逐字/数值匹配,**95/95 命中**(用归一化数字匹配器,正确处理 `$5.00`/`¥6.50`/`0.0028` 等表记形式)。证据里找不到的数字 = 0。
- **B3 · 来源官方**:21 个 `source_type=official` 的 `source_url` 全部落在官方域名(openai/anthropic/google/deepseek/x.ai/mistral/alibabacloud/kimi/z.ai)。3 个 Doubao 正确标 `secondary`。**无"非官方却标 official"**。
- **B4 · 归一化可复算**:USD 原生项 `usd_per_1m==original_value`;CNY 项(Kimi)按 `currency_fx.CNY.to_usd=0.1477` 复算,误差全部 < 1%(¥6.50→$0.9601、¥27→$3.9879、¥1.10→$0.1625,Doubao 同法)。**95/95 可复算**。
- **B5 · per_char 不硬换**:本数据集**无 per_char 计费项**(全部 per_1M_tokens),该项不适用、无违规。

### C. 名册与身份 — PASS
- **C1 · 名册有据**:`ROSTER.md` 每个模型带官方页 URL + 状态,非凭记忆。
- **C2 · 模型真实存在**(联网抽样确认):
  - `gpt-5.5` / `gpt-5.4`:确认 2026-04/03 发布、在售,API 价 $5/$30、$2.5/$15 与数据一致。
  - `kimi-k2.6`:确认 2026-04-20 发布在售;国际站 USD 折算($0.95 cache-miss in / $4.00 out)**反向印证**了中国站 CNY 值 + FX 换算(¥6.50×0.1477≈$0.96、¥27×0.1477≈$3.99)。
  - `deepseek-v4-pro`、`mistral-large-3`、`qwen3-max`、`grok-4.3` 均经官方页独立重抓确认在售(见 F 组)。
  - **无编造/记错的新型号**。
- **C3 · 精确到快照**:全部用精确 model_id(`gpt-5.5`、`claude-opus-4-8`、`deepseek-v4-pro` 等),无"GPT-4o"式泛称。
- **C4 · 范围合规**:24 条全为文本对话模型,无 embedding/图像/语音/微调混入。

### D. 完整性与诚实性 — PASS-WITH-ISSUES
- **D1 · 未知显式**:查不到的字段(grok-4.3 的 cache_read/batch、全部 Doubao 官方价、中国站 CNY 价)均 `null`+`unknown`/`needs_verification`,**无估算值冒充**。
- **D2 · 冲突未抹**:无 `confidence=conflict` 项;但抽查发现两处"官方 vs 二手"分歧,均**已在 evidence notes 记下且以官方为准(R3)、未静默**:
  - Grok 4.20:二手 $2/$6 vs 官方 $1.25/$2.50 → 取官方。
  - Mistral Large 3:二手旧快照 $2/$6 vs 官方现价 $0.5/$1.5 → 取官方。
- **D3 · 多维齐全**:cache_read / cache_write(Anthropic 5m+1h)/ batch / context_tiers(Gemini 3.1 Pro、Qwen3-Max)均已覆盖。**遗留缺口(issue,非阻塞)**:见下"未解决清单"。

### E. 事实层 / 分析层隔离 — PASS
- **E1 · 物理分开**:对比/排名只在 `analysis.md`;`pricing.json` / `evidence/` 仅含事实 + 溯源,无渗入。
- **E2 · 结论有据**:`analysis.md` 各结论可指回事实表数字,且该数字已过 B 组。
- **E3 · 假设透明**:FX(1 CNY=0.1477 USD @2026-06-03)、国际站/中国站口径差异在 README + analysis 写明。

### F. 独立抽样复抓(最强核验)— PASS
独立重抓 **9 家**官方页(只剩 Kimi 用 C2 搜索交叉印证),与 `evidence/`+`pricing.json` 比对,**实质一致,无对不上的数字**。明细见末尾"F 组记录"。

## 我做的修正清单(每处均有独立官方原文为据)

| # | 模型 | 字段/状态 | 原值 → 新值 | 依据(官方 URL + 原文) |
|---|---|---|---|---|
| 1 | DeepSeek **deepseek-v4-pro** | confidence | `needs_verification` → **confirmed** | 独立重抓 https://api-docs.deepseek.com/quick_start/pricing :v4-pro Input(miss)$0.435 / Output $0.87,**"No promotional or discount pricing noted"**。澄清此前二手"涨回 1.74/3.48"系误读——$0.435 即促销到期后官方价;今已过 2026-05-31 仍为 $0.435。值不变,仅升 confidence + 改 notes。 |
| 2 | Mistral **mistral-large-3** | confidence | `needs_verification` → **confirmed** | 独立重抓 https://mistral.ai/pricing/ 逐行:Large 3 = **$0.5 / $1.5**,行列正确非错位。值不变。 |
| 3 | Mistral **mistral-medium-3.5** | confidence | `needs_verification` → **confirmed** | 同上,Medium 3.5 = **$1.5 / $7.5**。Large 真低于 Medium 属实(新一代激进定价)。值不变。 |
| 4 | Alibaba **qwen3-max** | prices + context_tiers + confidence | 仅区间(input/output=null,`needs_verification`)→ **基础档 $1.2/$6 + 两档 context_tiers + confirmed** | 独立重抓 https://www.alibabacloud.com/help/en/model-studio/models :0–32K **$1.2/$6**、32K–128K **$2.4/$12**、128K–252K **$3/$15**(国际站 USD)。pricepertoken 等二手交叉一致。 |
| 5 | xAI **grok-4.3** | notes(值/状态不变) | — | 两次独立重抓 https://docs.x.ai/docs/models(#models)确认该页**确无 cached/batch 列** → cache_read/batch 维持 null+unknown 正确(R5)。notes 补记重抓结论 + 二手冲突处置。 |

同步更新:`pricing.csv`(上述 confidence/值)、`analysis.md`(§4 存疑项处置)、`ROSTER.md`(状态)、`CHANGELOG.md`(留痕)、各对应 `evidence/*.md`(追加"review 独立重抓 2026-06-04"段 + 逐字原文)。

改后复跑全部机器检查:schema PASS、CSV↔JSON PASS、95 数字逐字溯源 PASS、归一化复算 PASS。

## 仍阻塞 / 未解决清单(诚实声明,均为数据缺口,非幻觉)

1. **Doubao(Doubao-Seed-2.0-pro/lite/mini)官方价未取证**(3 条)。
   - 卡点:https://www.volcengine.com/docs/82379/1544106(中国站)与 https://docs.byteplus.com/en/docs/ModelArk/1544106(国际站)均 **JS 渲染**,WebFetch 两次重抓仍只得导航壳/breadcrumb,**无价格表格**。
   - 处置:维持 `source_type=secondary` + `confidence=needs_verification`,**未用搜索摘要冒充官方**(守 R3/R5)。需浏览器渲染/截图工具方可取官方价。
2. **国内中国站 CNY 价(通义 Qwen、智谱 GLM)缺口**。
   - 卡点:https://help.aliyun.com/zh/model-studio/models(JS 渲染,只取到模型列表,且已出现 "千问3.7-Max" 等更新名)、https://open.bigmodel.cn/pricing(JS 渲染)、https://docs.bigmodel.cn/cn/guide/start/model-pricing(404)。
   - 处置:Qwen/GLM 现录的是**国际站 USD** 价(已逐行注明),中国站 CNY 价**维持缺口、未编造**。
3. (提示性,非阻塞)Qwen 中国站页出现 **"千问3.7-Max"**,暗示可能有比 qwen3-max 更新的型号;但无官方价,本轮不动。
4. (提示性)Anthropic Opus 4.7+ **新分词器同文本多吃 ≤35% token**——影响实际成本非单价,已在 notes 注明,读者需知晓。

## F 组独立复抓记录(抓了哪几家、比对结果)

| 供应商 | 重抓 URL | 比对结果 |
|---|---|---|
| OpenAI | developers.openai.com/api/docs/pricing | ✅ 一致(gpt-5.5 $5/$0.50/$30 + batch $2.50/$15;5.5-pro $30/$180;5.4 $2.50/$0.25/$15) |
| Anthropic | platform.claude.com/docs/.../pricing | ✅ 一致(Opus4.8 $5/$6.25/$10/$0.50/$25 + batch $2.50/$12.50;Sonnet4.6、Haiku4.5 全对) |
| Google Gemini | ai.google.dev/gemini-api/docs/pricing | ✅ 一致(3.1Pro ≤200k $2/$12/cache$0.20/batch$1/$6;>200k $4/$18/$0.40/$2/$9;3.5Flash、3Flash 全对) |
| DeepSeek | api-docs.deepseek.com/quick_start/pricing | ✅ 一致 + **促销疑点澄清**(v4-pro $0.435/$0.87 常态价,见修正 #1) |
| xAI | docs.x.ai/docs/models(×2) | ✅ grok-4.3 $1.25/$2.50 一致;**确认无 cached/batch 列**(见修正 #5) |
| Mistral | mistral.ai/pricing/ | ✅ **行列正确**(见修正 #2/#3),Large 3 真便宜非错位 |
| Alibaba Qwen | alibabacloud.com/help/en/model-studio/models | ✅ **取到精确阶梯**(见修正 #4);中国站 CNY 仍缺(阻塞 #2) |
| Moonshot Kimi | (WebSearch 交叉印证) | ✅ 国际站 $0.95/$4.00 反向印证中国站 ¥6.50/¥27 + FX 换算 |
| ByteDance Doubao | volcengine 1544106 / byteplus 1544106 | ❌ **JS 渲染仍未取到官方价**(阻塞 #1) |
| Zhipu GLM | docs.z.ai/.../pricing(国际站复核) | ✅ 国际站 USD 一致(5.1 $1.4/$4.4/$0.26 等);中国站 CNY 仍缺(阻塞 #2) |

**F 组结论**:抽到的项无一出现实质不一致(数字对不上 / 来源不对);唯一"抓不到"的是 Doubao 与中国站 CNY 价,本就标为缺口/二手,不构成 FAIL,无需整体重抓。
