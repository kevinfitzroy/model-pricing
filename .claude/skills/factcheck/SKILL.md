---
name: factcheck
description: 跑「事实性核验工作流」的一个 case。当用户要核验事实/数据/声明——给文章链接核断言、核某产品官方规格、核新闻传闻、收集模型价格等——用本 skill。它读 WORKFLOW.md + 对应 cases/<case>/CASE.md,按"定义→取证→独立审计"三阶段执行,守八条反幻觉硬规则。触发形如 /factcheck article <url>、/factcheck product <名>、/factcheck news <陈述>、/factcheck model-pricing。
---

# factcheck — 事实性核验工作流执行器

你被调用来跑一个事实性核验 case。**核心铁律:宁可留洞,绝不编造**(详见硬规则 R1–R8)。

## 第 0 步:读规范(必须先做)
1. 读仓库根的 `WORKFLOW.md`(通用方法论:三阶段、八条硬规则、输出契约、confidence/verdict)。
2. 读 `cases/README.md` 找到匹配的 case。
3. 读该 case 的 `cases/<case>/CASE.md`(题材专属:单元、来源、产出、口径、审计补充)。

## 第 1 步:解析输入,定位 case
参数第一段是 case,其余是输入:

| 触发 | case 目录 | 输入 |
|---|---|---|
| `/factcheck article <url>` | `cases/article-claim-check/` | 文章 URL |
| `/factcheck news <陈述/链接>` | `cases/news-rumor-check/` | 传闻陈述或链接 |
| `/factcheck product <产品名>` | `cases/product-spec-check/` | 产品名(+ 可选待核声称) |
| `/factcheck model-pricing` | `cases/model-pricing/` | 无(固定范围) |

- 用户用自然语言(可能语音转写)说"核一下这篇文章 <url>"也照样映射到对应 case,按意图理解。
- 不确定是哪个 case,或输入缺失 → 先问清,别瞎跑。
- 没有现成 case 但任务符合工作流 → 用 `cases/_TEMPLATE/CASE.md` 起一个新 case 并登记。

## 第 2 步:执行(collector 角色)
按 CASE.md 在 `cases/<case>/`(或新输入则建带时间戳的子目录)下:
1. **核实范围/清单**——联网确认要核哪些单元,不靠记忆(R1)。
2. **逐单元取证**——抓一手/官方来源,逐字原文存进 `evidence/<源>/<日期>.md`(URL+抓取日期)(R2/R3)。
3. **派生结构化产出**——套 `schema/output.schema.json`(+ case 专属 schema),每条带 `evidence_ref` + `confidence`/`verdict`,未知留空显式标记(R4/R5/R6)。
4. **分析层**——对比/判断写进 `analysis.md`,标"这是解读"(R7)。
5. 抓不到/JS渲染/打不开 → **如实标缺口**,不要用搜索摘要冒充一手(R3/R5)。冲突标 `conflict` 不静默(R8)。

## 第 3 步:独立审计(reviewer 角色)
**另起一个 subagent** 当独立 reviewer(别自己审自己):让它读 `WORKFLOW.md` + `REVIEW.md` + 该 case 的 CASE.md「审计补充」,跑 A–F 清单 + 独立重抓抽样,出 `cases/<case>/REVIEW_REPORT.md`。B/C/F 任一 FAIL = 整体 FAIL。

## 收尾
向用户简报:总判定 + confirmed/needs_verification 计数 + 最关键发现 + 仍存的缺口。**诚实说明抓不到的部分**,不粉饰。
