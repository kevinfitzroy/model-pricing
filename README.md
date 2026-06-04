# fact-check — 事实性核验工作流

> 📊 **在线展示(工作流 + case 画廊):https://kevinfitzroy.github.io/fact-check/**

一套**与题材无关**的反幻觉事实核验方法论。凡是"要把外部世界的事实/数据/声明,整理成可对比、可追溯、不含幻觉的结论"的任务,都套用它。

> **首要约束:宁可少、宁可留洞,绝不编造。** 一个没有来源的结论,比一个空格更糟。

## 工作流:定义 → 取证 → 独立审计

1. **定义** — 把这个 case 怎么实例化写成 `CASE.md`:被核验单元、可信来源、产出结构、验收标准。
2. **取证** — 逐单元抓一手/官方来源,原文逐字存进 `evidence/`,派生结构化结论(每条引用证据),未知留空标记。
3. **独立审计** — 另起独立 reviewer 逐条核 + 重抓抽样,出 `REVIEW_REPORT.md`。

完整方法论(含 8 条硬规则 R1–R8、输出契约、confidence/verdict 体系)见 **[`WORKFLOW.md`](WORKFLOW.md)**;通用审计清单见 **[`REVIEW.md`](REVIEW.md)**;通用产出 schema 见 **[`schema/output.schema.json`](schema/output.schema.json)**。

## Cases(同一工作流,不同题材)

| case | 做什么 | 状态 | 触发 |
|---|---|---|---|
| **[model-pricing](cases/model-pricing/)** | 各家 AI 模型 API 价格收集对比 | ✅ 已完成([看板](https://kevinfitzroy.github.io/fact-check/cases/model-pricing/)) | `/factcheck model-pricing` |
| **[article-claim-check](cases/article-claim-check/)** | 核验一篇文章里的事实断言 | 🟡 模板就绪 | `/factcheck article <url>` |
| **[news-rumor-check](cases/news-rumor-check/)** | 核实一条新闻/传闻 | 🟡 模板就绪 | `/factcheck news <陈述>` |
| **[product-spec-check](cases/product-spec-check/)** | 核某产品官方规格/价格 | 🟡 模板就绪 | `/factcheck product <产品名>` |

新建 case:复制 [`cases/_TEMPLATE/CASE.md`](cases/_TEMPLATE/CASE.md) 填空,在 [`cases/README.md`](cases/README.md) 登记。

## 怎么触发

装了 **`/factcheck` skill**(`.claude/skills/factcheck/`)后,语音或键入 `/factcheck <case> <输入>` 即可起一个核验任务 —— skill 会读 `WORKFLOW.md` + 对应 `CASE.md`,按三阶段执行并起独立 reviewer 审计。也可直接口述"用核验工作流处理这个链接 …"。

## 目录

```
WORKFLOW.md            通用方法论(宪法)
REVIEW.md              通用审计清单
schema/output.schema.json  通用产出 schema
index.html             Pages 首页:工作流 + case 画廊
cases/
  README.md            case 索引 + 触发语
  _TEMPLATE/CASE.md    新建 case 模板
  model-pricing/       case #1:CASE.md + evidence/ + pricing.json/csv + 看板 + 审计报告
  article-claim-check/ case #2(模板)
  news-rumor-check/    case #3(模板)
  product-spec-check/  case #4(模板)
.claude/skills/factcheck/   /factcheck 触发 skill
```
