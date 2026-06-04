# Cases — 事实性核验工作流的实例

每个 case 是 [`../WORKFLOW.md`](../WORKFLOW.md) 在某个题材上的实例化。通用方法论(三阶段、八条硬规则、输出契约)在 WORKFLOW;**这里只放每个 case 的"专属部分"**(被核验单元是什么、什么算一手来源、产出长什么样)。

## 现有 case

| case | 做什么 | 输入 | 状态 | 触发语 |
|---|---|---|---|---|
| [`model-pricing/`](model-pricing/) | 各家 AI 模型 API 价格收集对比 | (无,固定范围) | ✅ 已完成 | `/factcheck model-pricing` |
| [`article-claim-check/`](article-claim-check/) | 核验一篇文章里的事实性断言/观点 | 文章 URL | 🟡 模板就绪 | `/factcheck article <url>` |
| [`news-rumor-check/`](news-rumor-check/) | 核实一条新闻/传闻 | 事件/传闻陈述 | 🟡 模板就绪 | `/factcheck news <陈述或链接>` |
| [`product-spec-check/`](product-spec-check/) | 核某产品的官方规格/参数/价格 | 产品名(+ 待核声明) | 🟡 模板就绪 | `/factcheck product <产品名>` |

## 怎么触发

**方式 A · Claude Code skill(推荐)**:`.claude/skills/factcheck/SKILL.md`。语音/键入 `/factcheck <case> <输入>`,skill 会读 WORKFLOW + 对应 CASE.md 并按三阶段执行。

**方式 B · 直接口述**:"用核验工作流处理这个链接 …",我读对应 `CASE.md` 照做。

## 新建一个 case

1. 复制 [`_TEMPLATE/CASE.md`](_TEMPLATE/CASE.md) → `cases/<新case>/CASE.md`,填空。
2. 在上表登记 + 写触发语。
3. 在 `.claude/skills/factcheck/SKILL.md` 的 case 分派表里加一行(可选)。
