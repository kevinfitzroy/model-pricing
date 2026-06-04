# REVIEW — 通用审计清单(第三步,reviewer 执行)

> 你是**独立 reviewer**。职责:**不信任**第二步产出,逐条机械核验,把问题揪出来。默认怀疑;无法验证的判 **FAIL**,不替它圆场。
> 你**不改数据**(或所有改动全部留痕),只出 `cases/<case>/REVIEW_REPORT.md`。
> 本清单与题材无关;每个 case 的题材专属检查项见该 case 的 `CASE.md`「审计补充」段。

先读:`WORKFLOW.md`、目标 case 的 `CASE.md`、`schema/output.schema.json`、该 case 的结构化产出 + `evidence/` + `analysis.md`。

## A. 结构与 schema(机器先跑)
- **A1** 结构化产出能被 `schema/output.schema.json` + case 专属 schema 校验通过。
- **A2** 人读视图(CSV 等)与机器源逐项一致。
- **A3** 有 `snapshot_date`;每条记录有 `fetch_date`。
- **A4** 每条记录的 `evidence_ref` 指向的文件/锚点真实存在(无死链)。

## B. 溯源(对抗幻觉核心,逐条逐结论)
- **B1 证据存在** — 每个结论的 `evidence_ref` 能定位到 `evidence/` 一条逐字引用。
- **B2 原文匹配** — 证据原文里**确实出现该结论的值/原句**(数值或字符串匹配)。证据里没有的 = 幻觉 → FAIL。
- **B3 来源一手** — `source_type=official/primary` 的 `source_url` 确属一手/官方;非一手而标成一手 → FAIL。
- **B4 加工可复算** — 任何换算/归一化能用原始值 + 已声明规则重算一致(R6)。算不出却给了值 → FAIL。

## C. 身份与范围(R1/R4)
- **C1 范围有据** — 被核验单元清单来自联网核实,非凭记忆。
- **C2 对象真实** — 抽样联网确认对象真实存在/现状属实,尤其训练知识里没有的新事物,警惕编造或记错。
- **C3 精确身份** — 用精确标识(精确 id / 精确原句 / 精确型号),无泛称冒充。
- **C4 范围合规** — 全部落在 CASE.md 声明的范围内,无越界条目。

## D. 完整性与诚实性(R5/R8)
- **D1 未知显式** — 查不到的字段 `null` + `unknown`/`needs_verification`,无估算冒充。
- **D2 冲突未抹** — `conflict` 项 notes 写清分歧;抽查有无"本该冲突却静默二选一"。
- **D3 维度齐全** — CASE.md 要求的维度/断言没被漏;来源有、产出却缺的标为缺口。

## E. 事实层 / 分析层隔离(R7)
- **E1 物理分开** — 对比/判断/结论只在 `analysis.md`,未渗入证据层/结构化产出。
- **E2 结论有据** — analysis 每条结论指回已过 B 组的事实;脑补结论 → FAIL。
- **E3 假设透明** — 换算、口径、判定标准等假设已写明。

## F. 独立抽样复抓(最强核验,必做)
- **F1** 随机抽 N=3~5 个单元,**自己联网重新取证**,与 evidence/ + 产出比对;实质不一致 → 该条 FAIL,提示是否整体重做。
- **F2** 检查 `snapshot_date`/`fetch_date` 时效;证据明显过旧则提示时效风险。

## 输出 `REVIEW_REPORT.md`
总判定(PASS / FAIL / PASS-WITH-ISSUES)+ 统计 + A–F 逐组结果(FAIL 列出具体单元+字段+原因)+ 修正清单(若改了,每条附依据)+ 仍阻塞/缺口清单 + F 组复抓记录。

判定准则:**B / C / F 任一 FAIL = 整体 FAIL**(溯源与真实性是底线);D/E 视严重度记 blocker 或建议。
