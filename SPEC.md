# SPEC — 模型价格收集任务规范(第一步产出)

> 本文件是整个任务的"宪法"。第二步的执行、第三步的 review 都以本文件为准。
> 本文件**允许演进**:任何范围/口径/字段变更,必须在 [`CHANGELOG.md`](CHANGELOG.md) 留痕(改了什么、为什么、谁发起)。

---

## 0. 一句话目标

收集各 AI 模型供应商**当下头部文本对话模型**的 API 价格,整理成**结构化、可对比、每个数字都能追溯到官方原文**的数据集。

**首要约束:宁可少、宁可留洞,绝不编造。** 一个没有来源的数字,比一个空格更糟。

---

## 1. 范围(v1)

### 1.1 收什么

- **只收文本对话(chat / text completion)模型。** embedding、图像生成、语音、视频、微调、rerank 等**单独计费的能力,v1 一律不收**(将来可在 CHANGELOG 里扩范围)。
- 每家收**当下流行的头部模型**(旗舰 + 主力性价比档),不求全量、不收已下线的老型号。
  - "当下流行的头部模型是哪些" **本身就是要联网核实的数据**,见 §2 硬规则 R1。

### 1.2 覆盖供应商(初始名单,可在 CHANGELOG 增删)

OpenAI、Anthropic、Google (Gemini)、DeepSeek、xAI (Grok)、Mistral、阿里通义 Qwen、字节豆包 Doubao、月之暗面 Kimi (Moonshot)、智谱 GLM。

- **Meta Llama 特殊**:Meta 不自营 token 计费 API,价格来自托管方(Together / Fireworks / Groq / Bedrock 等)。若收录,**必须标明是哪个托管方的价格**,且 `source_type` 不得标 `official`(它不是 Meta 官方定价)。是否收录 v1 待定,默认先不收,见 §6 待办。

---

## 2. 硬规则(违反即判 review 不通过)

- **R1 — 名册联网核实,不信记忆。** 纳入哪些模型、模型的精确 ID/发布状态,**必须来自联网检索到的官方信息**,不得凭训练知识脑补。典型坑:新发布的型号(如 DeepSeek V4 之类)训练知识里可能根本没有,或记成了错的价格。名册连同来源写入 `ROSTER.md`。
- **R2 — 证据先行,无证据不落数。** 任何价格数字写进结构化文件前,必须先有一条**逐字引用的原文证据**(见 §4 证据层)。结构化文件里的每个数字都要能反向指到某条证据。
- **R3 — 官方页为准。** 价格以**官方定价页 / 官方文档**为第一来源。聚合站(如各种 "LLM pricing" 对比站)**只能用于交叉验证**,不能作为唯一来源。
- **R4 — 精确到快照。** 模型身份记 **精确 API model id**(如 `claude-opus-4-8`、`deepseek-chat`、`gpt-4o-2024-08-06`),不能只写"GPT-4o"这种泛称。同一家同名模型有多个带日期快照、价格不同时,分别记录或注明取的是哪个。
- **R5 — 未知显式标记,不猜。** 查不到的字段填 `null` 并把 `confidence` 标 `unknown` 或 `needs_verification`,**绝不用估算值填空冒充已知**。
- **R6 — 原始口径进证据,归一化只在派生层。** 证据层保留来源的**原始单位与币种**(按 token / 按字符、USD / CNY、per 1K / per 1M 原样记)。统一换算只发生在派生层,换算公式见 §3。
- **R7 — 事实与分析物理分开。** 带引用的事实表是高可信资产;对比、排名、"谁更便宜"这类结论是**低可信派生观点**,必须放在单独文件/单独区块并明确标注"这是解读"。事实层不准混入未经核对的结论。
- **R8 — 冲突不静默。** 两个来源数字打架时,**两个都记下来 + 标 `conflict`**,在 notes 写清分歧,不要私自二选一抹掉另一个。

---

## 3. 口径与归一化(派生层规则)

### 3.1 要捕捉的价格维度(任务的多维本质)

模型价格**不是单一指标**,至少包含:

| 维度 | 说明 |
|---|---|
| `input` | 输入(prompt)单价 |
| `output` | 输出(completion)单价;含 reasoning/thinking token 时通常按 output 计,需注明 |
| `cache_read` | 缓存命中(读已缓存输入)单价 |
| `cache_write` | 写缓存单价;**注意分档**(如 Anthropic 有 5min / 1h 两档),分别记 |
| `batch_input` / `batch_output` | Batch API 价(常为半价),作为单独档,不混入实时价 |
| 阶梯 / 上下文阈值价 | 部分模型超过某 token 阈值(如 >128k、>200k)单价跳变,用 `context_tiers` 表达分段 |

### 3.2 单位与币种

- **证据层**:原样记录来源的 `currency`(USD / CNY / …)和 `unit`(`per_1M_tokens` / `per_1K_tokens` / `per_char` / …)。
- **派生层**:统一归一化到 **USD per 1M tokens**,字段名加 `_usd_per_1m` 后缀;同时保留 `*_original` 原始值与其 `currency`/`unit`。
- **换算规则**:
  - `per_1K_tokens → per_1M_tokens`:× 1000。
  - `per_char → per_1M_tokens`:需注明字符↔token 的换算假设(各家不同),**该假设写进 notes**;拿不准就**不强行换**,USD 归一化字段留 `null` 并标 `needs_verification`。
  - **CNY → USD**:汇率会引入自身的时效问题。做法:**保留 CNY 原值**,USD 归一化用一个**记录在案的汇率**(汇率数值 + 日期 + 来源写进 `README.md` 和该行 notes)。汇率属于"分析层假设",reviewer 要能用它复算。

### 3.3 快照时效

整个数据集是**某一时点的快照**。每条记录带 `fetch_date`,数据集整体在 `README.md` 顶部标 **snapshot date**。读者不得误以为是实时价。

---

## 4. 磁盘布局(证据层 vs 派生层,物理隔离)

```
model-pricing/
  SPEC.md            # 本文件:任务定义+规则+口径+工作流
  schema.json        # 字段数据字典(机器可读,JSON Schema)
  REVIEW.md          # 第三步 agent 验收清单
  CHANGELOG.md       # 范围/schema 演进留痕
  ROSTER.md          # 纳入的模型名册(带来源,联网核实,会演进)— 第二步产出
  README.md          # 口径说明 + snapshot date + 汇率假设 + 怎么读 — 第二步产出

  evidence/          # ⬅ 事实/证据层:原始逐字引用,不可篡改、不做换算
    openai/
      2026-06-04.md  # 官方 URL + 抓取时间戳 + 逐字引用的价格原文
    anthropic/
      2026-06-04.md
    ...

  pricing.json       # ⬅ 派生层:归一化结构化数据,每个数字引用 evidence(机器源)
  pricing.csv        # ⬅ 派生层:扁平宽表,给人看的对比视图(由 json 导出)

  analysis.md        # ⬅ 分析层:对比/排名/结论,明确标注"这是解读"(R7)
```

- **evidence/ 一条记录长什么样**:provider、抓取的官方 URL、抓取日期时间、然后是**从页面逐字摘录**的价格文本(原币种原单位,别改写别换算)。一个 provider 一个抓取日期一个文件,便于重抓和 diff。
- **pricing.json 一条记录**:见 `schema.json`。关键是每个数字带 `evidence_ref`(指到 evidence 文件 + 锚点)。

---

## 5. 工作流(三步,允许回路)

### 第一步 · 定义(本文件所在阶段)

产出 `SPEC.md` / `schema.json` / `REVIEW.md` / `CHANGELOG.md`。**这一步的产出要先被 review/认可,再进第二步。**

### 第二步 · 执行(尽量多收证据 → 落盘 → 初步派生)

1. **核实名册**:联网检索各家**官方**信息,确认当下头部文本对话模型及其精确 model id、是否在售,写入 `ROSTER.md`(每个型号带来源 URL + 日期)。(R1)
2. **逐家抓官方定价页**:把价格原文**逐字摘录**进 `evidence/<provider>/<date>.md`,带 URL 和抓取时间戳。(R2/R3/R6)
3. **派生归一化数据**:按 `schema.json` 把证据整理进 `pricing.json`(再导出 `pricing.csv`),**每个数字回填 `evidence_ref`**,按 §3 归一化,未知留 `null` + 标记。(R4/R5/R6)
4. **写 `README.md`**:口径说明、snapshot date、汇率假设、字段含义、"这是某日快照非实时"声明。
5. **写 `analysis.md`**:对比/排名/结论,**与事实层分开**,每条结论指回事实表。(R7)

> **回路**:第二步若发现第一步没预料到的维度(例如某家新出一种缓存档),**回头改 SPEC/schema,并在 CHANGELOG 记一笔**,再继续。第一步不冻结。

### 第三步 · Review(由 agent 执行)

按 [`REVIEW.md`](REVIEW.md) 逐条机械核验,输出 `REVIEW_REPORT`。前两步留痕越清楚,这步越快。

---

## 6. 待办 / 第一步遗留的决策(进第二步前可再确认)

- Meta Llama 是否收录、若收用哪个托管方为代表(默认暂不收,见 §1.2)。
- CNY→USD 用哪个汇率源、锁定到哪一天(第二步抓取时确定并记入 README)。
- `per_char` 计费的国内厂商,字符↔token 换算假设取多少(或干脆不归一化、只在 CNY 原值层对比)。
