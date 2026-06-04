# CHANGELOG — 范围 / schema / 口径的演进留痕

> SPEC 不冻结。任何对**收集范围、字段 schema、口径换算规则**的改动,在这里记一笔:
> 改了什么、为什么改(通常是第二步抓取时发现了新维度)、谁发起。
> 这是"第一步允许演进"这条原则的物证,也是第三步 review 的审计线索。

格式:`## YYYY-MM-DD — 标题` + 改动点 + 原因。

---

## 2026-06-04 — 重构:方法论抽象为通用「事实性核验工作流」

把原本绑在"模型价格"这一题材上的方法论**抽象成与题材无关的通用工作流**,模型价格降级为其中一个 case。

- 新增 `WORKFLOW.md`(通用方法论,泛化自原 SPEC 的八条硬规则/三阶段/输出契约)、通用 `REVIEW.md`、`schema/output.schema.json`、`cases/` 结构与 `_TEMPLATE`。
- 原 `SPEC.md` 拆分:通用部分 → `WORKFLOW.md`;价格专属部分 → `cases/model-pricing/CASE.md`(删除 SPEC.md)。
- 现有产物(evidence/、pricing.json/csv、analysis.md、ROSTER.md、REVIEW_REPORT.md、schema.json、看板 index.html)迁入 `cases/model-pricing/`。
- 预制三个 case 模板:`article-claim-check`(文章断言核验)、`news-rumor-check`(新闻/传闻核实)、`product-spec-check`(产品规格核验)。
- 新增 `/factcheck` skill(`.claude/skills/factcheck/`)作为触发入口。
- Pages 首页 `index.html` 重写为"工作流 + case 画廊";定价看板降为 `cases/model-pricing/` 子页。
- 仓库改名 `model-pricing` → `fact-check`。
- 发起:用户(evan)—— "方法论独立出来做成工作流,适配不同 case"。

> 以下为重构前、model-pricing 作为独立项目时的历史留痕。

## 2026-06-04 — 初始 SPEC 成型(第一步)

- 确立三步工作流:定义 → 执行(证据先行)→ agent review。
- **范围 v1**:只收文本对话模型;每家收当下头部模型;Meta Llama 默认暂不收(无官方 token 定价)。
- **核心原则**:无来源不落数(R2);名册联网核实不信记忆(R1);未知显式不猜(R5);事实层/分析层物理分开(R7)。
- **口径**:原始口径进证据层,统一归一化到 USD/1M tokens 只在派生层;CNY 用记录在案的汇率换算。
- 产出 `SPEC.md` / `schema.json` / `REVIEW.md` / 本文件。
- 发起:用户(evan) + Maestro 讨论对齐。

## 2026-06-04 — 第二步执行:抓取存证 + 派生产出

抓取 10 家官方页,产出 `evidence/`、`pricing.json`、`pricing.csv`、`README.md`、`analysis.md`。执行中发现并处理的新情况:

- **R1 验证生效**:训练知识严重过时,联网纠正了多处——OpenAI 已到 **GPT-5.5**、Anthropic **Opus 4.8**、Gemini **3.x**、DeepSeek **V4**、GLM **当前旗舰是 5.1 不是 4.6**。凭记忆必错。
- **新口径维度:国际站 vs 中国站**。国内厂商(Qwen/GLM)国际站以 USD 计、中国站以 CNY 计,**是不同产品不同价**。本版 Kimi=中国站 CNY,Qwen/GLM=国际站 USD,已逐行标注;**中国站 CNY 价列为待补采缺口**。
- **抓取层障碍**:OpenAI 官网、x.ai/api 返回 403;通义/豆包/GLM 中国站、Kimi 入口页为 JS 渲染,WebFetch 取不到表格。对策:换 developers/docs/国际静态子站;豆包实在取不到 → 标 `secondary + needs_verification`,**未用二手冒充官方**(守 R3/R5)。
- **存疑项(已按 R8 标记,留第三步核)**:DeepSeek V4-pro 疑促销价;Mistral Large<Medium 反常;Qwen3-Max 仅区间;xAI 缺 cached/batch;Doubao 全量官方未证。
- **FX 假设入账**:1 CNY=0.1477 USD(USD/CNY 6.7684 @2026-06-03),记入 `pricing.json.currency_fx` 与 README。
- schema 未改(现有字段足够表达上述情况:`source_type=secondary`、`confidence=needs_verification`、`context_tiers`、`*_orig`/`*_usd_per_1m` 均已就位)。

> 下一步(第三步):agent 按 `REVIEW.md` 验收,重点独立重抓 needs_verification 项 + 补采国内 CNY 价。

## 2026-06-04 — 第三步 review:独立重抓 + 修正 + 缺口处置

review agent 按 `REVIEW.md` A–F 逐组核验,并独立重抓官方页核实 7 个 needs_verification 项。机器检查全过(schema 校验、CSV↔JSON 一致、95 个数字逐字溯源、归一化复算)。本轮数据修正(每处均有独立官方原文为据,详见 `REVIEW_REPORT.md` 与各 evidence 文件的"review 独立重抓"段):

- **DeepSeek v4-pro**:`needs_verification → confirmed`。独立重抓 api-docs.deepseek.com 确认 $0.435/$0.87 为常态官方价、无促销;澄清此前"涨回 1.74/3.48"为二手误读($0.435 即促销到期后官方价)。
- **Mistral large-3 / medium-3.5**:`needs_verification → confirmed`。独立重抓 mistral.ai/pricing 核对行列正确,Large 3($0.5/$1.5)真低于 Medium 3.5($1.5/$7.5),非抓取错位。
- **Qwen3-max**:`needs_verification → confirmed`,且**补全精确阶梯**:基础档 ≤32K $1.2/$6;`context_tiers` 32K–128K $2.4/$12、128K–252K $3/$15(国际站 USD)。
- **xAI grok-4.3**:维持 confirmed;两次独立重抓确认官方 models 页确无 cached/batch 列 → 保持 null+unknown(未猜)。
- 同步更新 `pricing.csv`、`analysis.md`(存疑项处置)、ROSTER 状态。

**未解决阻塞(诚实声明)**:
- **Doubao(pro/lite/mini)**:官方页(volcengine / BytePlus)JS 渲染,第三步重抓仍未取到官方价,维持 `secondary + needs_verification`,**未用二手冒充官方**(守 R3/R5)。
- **国内中国站 CNY 价(Qwen / GLM)**:help.aliyun.com、bigmodel.cn 均 JS 渲染/不可达,未取到 → 维持缺口,未编造。
- 发起:review agent(第三步)。
