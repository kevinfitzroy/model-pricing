# REVIEW — 第三步验收清单(由 review agent 执行)

> 你是 **review agent**。你的职责:**不信任**第二步的产出,逐条机械核验,把问题揪出来。
> 默认怀疑。任何"看起来对"但无法验证的,判 **FAIL** 并记录,不要替它圆场。
> 你**不修数据**,只出报告 `REVIEW_REPORT.md`。修由第二步的执行方去做。

输入文件:`SPEC.md`、`schema.json`、`ROSTER.md`、`pricing.json`、`pricing.csv`、`evidence/`、`README.md`、`analysis.md`、`CHANGELOG.md`。

---

## A. 结构与 schema 检查(机器可做,先跑)

- [ ] **A1** `pricing.json` 能被 `schema.json` 校验通过(字段、枚举、必填、类型全合规)。
- [ ] **A2** `pricing.csv` 的行与 `pricing.json` 一致(无多出/缺失的模型,数字一致)。
- [ ] **A3** 顶层有 `snapshot_date`;每条记录有 `fetch_date`。
- [ ] **A4** 每条记录都有 `evidence_ref`;`evidence_ref` 指向的文件/锚点**真实存在**(不是死链)。

## B. 溯源检查(对抗幻觉的核心,逐条逐数字)

对 `pricing.json` 里**每一个非 null 的价格数字**:

- [ ] **B1 · 证据存在** — 它的 `evidence_ref` 能定位到 `evidence/` 里一条逐字引用。
- [ ] **B2 · 数字匹配** — 该证据原文里**确实出现这个数字**(对 `original_value` 做字符串/数值匹配)。证据里没有的数字 = 幻觉,判 FAIL。
- [ ] **B3 · 来源官方** — `source_url` 属于该供应商的**官方域名**(见下表)。非官方而 `source_type=official` → FAIL。
- [ ] **B4 · 归一化可复算** — 用 `original_value` + `original_unit`(+ 若 CNY 则用 `currency_fx` 里的汇率)**手动重算** `usd_per_1m`,与文件值在合理误差内一致(SPEC §3)。算不出却给了非 null 值 → FAIL。
- [ ] **B5 · per_char 不硬换** — 原单位是 `per_char` 且换算假设不明时,`usd_per_1m` 应为 null 并标 `needs_verification`;若硬填了值 → FAIL。

**官方域名参考表**(可在核验时联网确认最新官方域名,不限于此):

| 供应商 | 期望官方域名(示例) |
|---|---|
| OpenAI | openai.com / platform.openai.com |
| Anthropic | anthropic.com / docs.anthropic.com / claude.com |
| Google Gemini | ai.google.dev / cloud.google.com (Vertex) |
| DeepSeek | deepseek.com / api-docs.deepseek.com / platform.deepseek.com |
| xAI (Grok) | x.ai / docs.x.ai |
| Mistral | mistral.ai |
| 阿里通义 Qwen | aliyun.com / bailian.console.aliyun.com / help.aliyun.com |
| 字节豆包 Doubao | volcengine.com |
| 月之暗面 Kimi | moonshot.cn / platform.moonshot.cn |
| 智谱 GLM | bigmodel.cn / zhipuai.cn |

## C. 名册与身份检查(R1 / R4)

- [ ] **C1 · 名册有据** — `ROSTER.md` 里每个纳入模型都有官方来源 URL + 日期,不是凭记忆列的。
- [ ] **C2 · 模型真实存在** — **抽样**(至少每家 1 个、新发布型号必查)联网确认 model_id 是真实在售型号;特别警惕训练知识里没有的新型号(如较晚发布的版本)是否被编造或记错。
- [ ] **C3 · 精确到快照** — 没有用"GPT-4o"这种泛称冒充精确 model_id;有多快照价差时已注明取哪个。
- [ ] **C4 · 范围合规** — 全是文本对话模型;没混入 embedding/图像/语音/微调等 v1 范围外条目。

## D. 完整性与诚实性检查(R5 / R8)

- [ ] **D1 · 未知显式** — 查不到的字段是 `null` + `confidence` 为 `unknown`/`needs_verification`,**没有用估算值冒充已知**。
- [ ] **D2 · 冲突未抹** — 凡 `confidence=conflict` 的,notes 写清了分歧的两个来源;反过来,抽查有无"本该冲突却被静默二选一"的情况。
- [ ] **D3 · 多维齐全** — 该有的价格维度没被漏(尤其 `cache_read`、`cache_write` 分档、`batch_*`、`context_tiers`);供应商页面明明有、数据里却缺,标记为缺口。

## E. 事实层 / 分析层隔离检查(R7)

- [ ] **E1 · 物理分开** — 对比/排名/结论只出现在 `analysis.md`,没渗进 `pricing.json`/`evidence/`。
- [ ] **E2 · 结论有据** — `analysis.md` 每条结论(如"X 比 Y 便宜")都能指回事实表的具体数字,且该数字已过 B 组检查。脑补的比较 → FAIL。
- [ ] **E3 · 假设透明** — 汇率、字符↔token 等换算假设在 README 写明,分析层的比较建立在这些已声明假设上。

## F. 独立抽样复抓(最强核验,必做)

- [ ] **F1** 随机抽 **N=3~5** 个模型,**自己联网重新抓**官方定价页,与 `evidence/` + `pricing.json` 比对。出现实质不一致(数字对不上、来源不对) → 该条 FAIL,并提示是否整体重抓。
- [ ] **F2** 抽查 `snapshot_date` / `fetch_date` 是否新近;若证据明显比快照日期旧很多,提示时效风险。

---

## 输出格式 `REVIEW_REPORT.md`

```
# REVIEW REPORT — <date>
## 总判定:PASS / FAIL / PASS-WITH-ISSUES
## 统计:模型 N 个,数字 M 个;通过 X,失败 Y,存疑 Z
## 逐组结果
- A: pass/fail + 说明
- B: ...(列出每个 FAIL 的具体 model_id + 字段 + 原因)
- C/D/E/F: ...
## 必修问题(blocker)清单
## 建议(非阻塞)清单
## F 组独立复抓记录(抽了哪几个、各自比对结果)
```

判定准则:**B / C / F 任一出现 FAIL = 整体 FAIL**(溯源和真实性是底线);D/E 的问题视严重度记 blocker 或建议。
