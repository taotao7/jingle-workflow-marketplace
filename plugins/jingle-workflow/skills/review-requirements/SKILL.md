---
name: review-requirements
description: >
  Multi-dimensional review of product requirements (PRD / spec / feature request).
  Grounds feasibility assessment in the current project's codebase and tech stack.
  Produces two reports: one for developers, one for the product team — covering
  completeness, clarity, feasibility, consistency, rationality, compliance, risk,
  testability, UX, data, priority, and dependencies. Mandatorily surfaces every
  unclear or semantically ambiguous phrase found in the requirement.
  Use when the user asks to review, evaluate, critique, or check a product requirement,
  PRD, spec, user story, or feature request.
---

# Review Requirements

You are a senior product & engineering consultant. Your job is to rigorously review product requirements and produce **two separate reports** — one for the development team, one for the product team — each tailored to what that audience cares about.

---

## Step 1: Parse input

Determine the input source from `$ARGUMENTS`:

| Pattern | Action |
|---------|--------|
| Ends with `.md`, `.txt`, `.pdf`, `.docx`, or resolves to an existing file | Read the file using the Read tool |
| Starts with `http://` or `https://` | Fetch the content. If the URL looks like a Feishu story/doc, try `mcp__jingle-feishu__get-story-detail` first. Otherwise use `WebFetch` |
| Looks like a Feishu story ID (pure number) | Use `mcp__jingle-feishu__get-story-detail` with that ID |
| Non-empty text that isn't a file or URL | Treat the text itself as the requirement content |
| Empty (no arguments) | Scan the project for likely requirement docs: `docs/`, `requirements/`, `prd/`, `specs/`, `*.md` files at root. List candidates and ask the user to confirm which to review |

If the content cannot be obtained, stop and tell the user what went wrong.

---

## Step 2: Establish project context

Before evaluating feasibility, quickly understand the current project:

1. **Read README.md** (if present) for project overview
2. **Detect tech stack** by checking for: `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, `pom.xml`, `build.gradle`, `Gemfile`, `composer.json`, `pubspec.yaml`
3. **Glance at top-level directory structure** using `ls`
4. **Note any CLAUDE.md** for existing project conventions

Capture a brief summary (3-5 bullets) of the project context. This informs the feasibility dimension below.

---

## Step 3: Mandatory ambiguity scan (不清晰 / 语意不详)

**This step is required. Do not skip it even if the document looks polished.**

Go through the requirement line by line and flag **every** description that is unclear, ambiguous, or open to multiple interpretations. Product requirement docs' most common failure mode is not "missing content" — it's "content that reads fluently but means different things to different readers". These must be surfaced explicitly, not buried inside other dimensions.

For each flagged phrase, record:

1. **原文引用** — the exact sentence/phrase from the document (copy it verbatim)
2. **位置** — section / paragraph / line if available
3. **问题类型** — one of:
   - 模糊修饰词 (e.g., "快速", "高性能", "用户友好" without concrete numbers)
   - 主语/对象缺失 ("需要处理" — 谁处理？系统自动还是用户手动？)
   - 条件/边界不完整 ("某些情况下" — 哪些情况？如何判断？)
   - 因果/目的模糊 ("优化体验" — 改善哪个具体指标？)
   - 逻辑歧义 ("A 或 B 完成后" — 任意一个还是两个都要？)
   - 指代不明 ("将其同步" — "其"是什么？同步到哪里？)
   - 隐含假设 ("按正常流程处理" — 哪里定义了"正常流程"？)
   - 术语不一致 (同一概念用了多个名字，或同一名字指多个概念)
4. **可能的多种理解** — at least 2 interpretations a developer could reasonably choose
5. **需要产品回答什么** — the concrete clarification you need

**Rule of thumb**: If after reading a sentence you can ask "who? when? which case? what exactly?" and the answer isn't spelled out within 1-2 paragraphs, it's ambiguous — flag it. Err on the side of flagging more, not fewer. Better to ask a question that turns out obvious than to let developers guess.

This scan feeds directly into Step 6's 语意不清与模糊表达对照表 (which is **mandatory** — include it even if the list ends up empty, explicitly stating "未发现语意不清问题").

---

## Step 4: Multi-dimensional evaluation

Evaluate the requirements on all 12 dimensions below. For each dimension, assign one of three ratings:

- **PASS** — requirement adequately addresses this dimension
- **NEEDS WORK** — partially addressed but has gaps
- **MISSING** — not addressed at all

Provide a one-line justification for each rating.

### Dimensions

| # | Dimension | Key questions |
|---|-----------|---------------|
| 1 | **完整性 (Completeness)** | Are functional scope, non-functional requirements (performance, availability, security), acceptance criteria, and success metrics all present? |
| 2 | **清晰性 (Clarity)** | Is the language unambiguous? Are vague modifiers avoided (no "快速"/"用户友好"/"高性能" without concrete numbers)? Is terminology consistent? **This dimension's rating must align with Step 3's ambiguity scan — if any items were flagged in Step 3, this dimension cannot be PASS.** |
| 3 | **可行性 (Feasibility)** | Is this achievable with the current tech stack and architecture? What are the technical unknowns? (Use project context from Step 2) |
| 4 | **一致性 (Consistency)** | Are there internal contradictions? Does the requirement align with existing product behavior and design patterns? |
| 5 | **合理性 (Rationality)** | Does this solve a real user problem? Is the effort proportional to the expected value? Is the scope bounded and not over-engineered? |
| 6 | **合规性 (Compliance)** | Are data privacy regulations considered (GDPR, PIPL, CCPA)? Security requirements? Accessibility (WCAG)? Industry-specific regulations? Legal review needed? |
| 7 | **可测试性 (Testability)** | Are acceptance criteria concrete enough to write test cases? Can edge cases be identified from the requirements alone? |
| 8 | **优先级 (Priority)** | Is MVP vs. nice-to-have clearly distinguished? Is there a phased rollout plan? Are dependencies between phases identified? |
| 9 | **依赖 (Dependencies)** | Are third-party APIs, other teams, infrastructure changes, or external services identified? Are timelines for dependencies clear? |
| 10 | **风险 (Risk)** | Are edge cases, failure modes, degradation strategies, and rollback plans considered? What could go wrong? |
| 11 | **UX/交互 (User Experience)** | Are user flows complete? Are empty states, error states, loading states, and transition animations specified? Is the interaction model clear? |
| 12 | **数据 (Data)** | Is the data model defined? Are migrations, data retention policies, PII handling, and backward compatibility addressed? |

---

## Step 5: Write the developer report — 开发实施注意事项

**Purpose: this report is a list of things developers must pay attention to when implementing this requirement.** It is NOT a spec rewrite, NOT a feasibility essay. It's a practical watchlist of landmines, edge cases, architectural impacts, and technical decisions to keep in mind during coding, testing, and rollout.

Create the file `./review-reports/dev-review-YYYY-MM-DD.md` (use today's date; if the file already exists, append `-2`, `-3`, etc.).

### Report structure

```markdown
# 开发注意事项：[需求标题]

> 评审日期: YYYY-MM-DD
> 项目: [project name from context]
> 技术栈: [detected stack]
> 整体判断: [可以开工 / 谨慎进行 / 暂不建议开工]

## 总览

[2-3 句话概括整体情况，例如"需求基本可行，但鉴权模型与现有系统冲突，且涉及 PII 数据，建议重点关注第 2、5、7 项"]

## 注意事项清单

> 每条独立成项，按严重程度从高到低排序。严重程度：🔴 必须处理 / 🟡 建议处理 / 🟢 提醒知悉

### 🔴 1. [一句话标题，例如"用户表需要新增 phone_verified 字段，涉及历史数据回填"]

**类别**: 数据 / 架构 / 安全 / 性能 / 依赖 / 边界情况 / 测试 / 部署

**要点**: 具体说明这个点为什么需要注意。

**影响面**: 涉及哪些模块、接口、服务、团队。

**建议处理方式**: 推荐怎么做，或者需要怎么和谁对齐。

---

### 🔴 2. ...

### 🟡 3. ...

### 🟢 N. ...

## 维度评分（内部参考）

| 维度 | 评级 | 一句话 |
|------|------|--------|
| 完整性 | PASS/NEEDS WORK/MISSING | ... |
| 清晰性 | ... | ... |
| 可行性 | ... | ... |
| 一致性 | ... | ... |
| 合理性 | ... | ... |
| 合规性 | ... | ... |
| 可测试性 | ... | ... |
| 优先级 | ... | ... |
| 依赖 | ... | ... |
| 风险 | ... | ... |
| UX/交互 | ... | ... |
| 数据 | ... | ... |

## 工作量粗估

**S / M / L / XL** — [简短理由，可按模块拆分]
```

### Guidance for generating the notice list

- Every item must be **actionable and concrete** — not "注意性能" but "N+1 查询风险：列表页每行调用 `getUser()`，需改为批量拉取"
- Ground each item in the **actual project context** detected in Step 2 (real file paths, real services, real frameworks)
- Typical categories to cover (skip any that don't apply):
  - 数据模型变更 / 迁移 / 回填 / 历史兼容
  - 接口改动 / 向后兼容 / 版本
  - 鉴权 / 权限 / 会话
  - 安全 / PII / 加密 / 审计日志
  - 性能 / 并发 / 缓存 / 限流
  - 第三方依赖 / 跨团队协作
  - 灰度 / 开关 / 回滚预案
  - 测试策略 / 可观测性
  - 边界情况与错误处理

---

## Step 6: Write the product report — 产品需求问题清单

**Purpose: this report is a list of questions/issues the product team needs to answer or address before the requirement can be implemented.** It is NOT a rewritten spec, NOT a technical assessment. Every item should be a clear question or concrete problem that product needs to fix in the PRD.

Create the file `./review-reports/product-review-YYYY-MM-DD.md` (same date/collision rules).

### Report structure

```markdown
# 产品需求问题清单：[需求标题]

> 评审日期: YYYY-MM-DD
> 整体判断: [可以进入开发 / 需要补充后再评审 / 需要重大调整]

## 总览

[2-3 句话概括需求文档的主要问题。例如"核心流程清晰，但缺少异常场景定义、未明确 MVP 范围、且涉及用户隐私数据但未说明合规要求，共提出 14 个问题，语意不清 6 处"]

## 🚨 语意不清与模糊表达对照表（必填）

> **这是产品需求文档最常犯的错误，也是这份报告最重要的部分之一。** 此表必须存在 —— 即使扫描后没有发现任何问题，也要显式写明"未发现语意不清问题"，不能直接省略此小节。
>
> 本表来源于 Step 3 的强制扫描结果。收录范围包括：
> - **模糊修饰词**（如"快速"、"高性能"、"用户友好"，缺少具体指标）
> - **主语/对象缺失**（"需要处理该数据" → 谁处理？系统自动还是用户手动？）
> - **条件/边界不完整**（"某些情况下跳过验证" → 哪些情况？如何判断？）
> - **因果/目的模糊**（"优化用户体验" → 具体哪个环节？改善什么指标？）
> - **逻辑歧义**（"A 或 B 完成后触发" → 任意一个完成就触发，还是两个都完成？）
> - **指代不明**（"将其同步到系统" → "其"是什么？同步到哪个系统？）
> - **隐含假设**（"按照正常流程处理" → 什么是"正常流程"？文档没有定义）
> - **术语不一致**（同一概念用了多个名字，或同一名字指多个概念）
>
> **扫描原则**：读完一句话后只要产生"谁？什么时候？哪种情况？具体指什么？"等疑问，且答案没有在相邻上下文中给出，就必须收录。宁可多记，不可漏记。

| # | 位置 | 原文 | 问题类型 | 可能的多种理解 | 需要产品回答 | 建议改写示例 |
|---|------|------|----------|----------------|--------------|--------------|
| 1 | 2.1 节 | "响应要快" | 模糊修饰词 | <300ms? <1s? <3s? | 明确 P95 响应时间上限 | "接口 P95 响应 < 300ms" |
| 2 | 3.2 节 | "系统需要处理异常" | 主语/对象缺失 | 前端 try/catch? 后端重试? 运维告警? | 明确哪个模块在什么层面处理 | "支付模块捕获超时异常后，重试 1 次，仍失败则记录日志并通知用户" |
| 3 | 4.1 节 | "A 或 B 完成后触发通知" | 逻辑歧义 | (a) 任意一个完成即触发 (b) 两个都完成才触发 | 明确是 OR 还是 AND | "A 和 B 都完成后触发一次通知" |
| 4 | 5.3 节 | "完成后通知相关人员" | 指代不明 | "完成"=下单? 支付? 发货? "相关人员"=客服? 运营? 下单用户? | 明确触发点和通知对象 | "订单发货后，通过站内信通知下单用户和对应客服" |

> 如果扫描后确认文档表达完全清晰，将整张表替换为一行："✅ 未发现语意不清或表达模糊的描述。"

## 问题清单

> 每个问题独立成项，包含所属维度、问题描述、为什么重要、期望的回答格式。
> 优先级：🔴 阻塞（不解答无法开发）/ 🟡 重要（影响实施质量）/ 🟢 建议（锦上添花）

### 🔴 Q1. [一句话问题，例如"用户登录失败 5 次后应该如何处理？"]

**维度**: 完整性 / 清晰性 / 一致性 / 合理性 / 合规性 / 可测试性 / 优先级 / 依赖 / 风险 / UX / 数据

**问题描述**: 具体是哪一段、哪一点没讲清楚或缺失。可引用原文。

**项目依据**: 从当前项目中找到的客观证据，说明为什么这个问题必须由产品明确。例如：
- 现有代码行为："`src/auth/login.ts:42` 当前实现是失败 3 次锁定 30 分钟，需求未说明是沿用还是修改"
- 已有数据模型："`users` 表无 `login_attempts` 字段，若要实现需新增字段并回填"
- 现有产品逻辑冲突："当前注册流程不要求手机号，但需求中'短信验证解锁'依赖手机号"
- 线上数据/规模："当前日活 10 万用户，锁定策略直接影响客服工单量"

**为什么重要**: 这个问题如果不解决，会导致什么后果（开发无法推进 / 体验不一致 / 合规风险等）。

**期望回答**: 你希望产品以什么形式回答，例如：
- 明确数字：多少次之后锁定？锁定多久？
- 具体流程：锁定后用户如何解锁？
- 文案：锁定提示的文案是什么？

---

### 🔴 Q2. ...

### 🟡 Q3. ...

### 🟢 Q_n. ...

## 合规与法务提醒

> 涉及数据隐私、安全、无障碍、法律等方面需要产品/法务确认的点（如果有）

- [ ] 是否涉及个人信息收集？是否符合 PIPL/GDPR？
- [ ] ...

## 维度评分

| 维度 | 评级 | 一句话 |
|------|------|--------|
| 完整性 | PASS/NEEDS WORK/MISSING | ... |
| ...（12 项全部列出） |
```

### Guidance for generating the question list

- Each item must be **a real question or problem**, not a generic complaint. "需求不完整" ❌ / "用户注销账户后，订单历史是否保留？保留多久？" ✅
- **Quote the original text** when pointing out ambiguity or contradiction
- **🚨 重点关注语意表达不明确的地方** — 这是产品最容易犯的错误。凡是读完后让你产生"这里到底是什么意思？"、"谁来做？"、"什么时候？"、"哪种情况？"等疑问的描述，都必须作为问题提出来。宁可把潜在的理解分歧都问清楚，也不要让开发凭猜测实现。对于每一处语意不清的地方，引用原文 → 指出可能的多种理解 → 要求产品明确选哪一种。
- **🚨 每个问题必须有项目依据** — 不能凭空提问。每个问题的「项目依据」字段必须引用 Step 2 中获取的项目上下文作为客观证据：现有代码行为、数据模型、API 接口、已有产品逻辑、技术栈限制等。让产品看到"不是开发在挑刺，而是项目现状确实需要你明确这个点"。如果某个问题无法从项目中找到直接依据（如全新功能），则说明"当前项目无相关实现，需产品定义从零开始的规则"，这本身也是依据。
- **Expected answer format** helps product team give concrete answers (number, flow, copy, decision)
- Don't over-question — don't ask things that are clearly implied or that are engineering-side decisions (those go in the dev report)
- If a question is really a technical concern, don't put it here — put it in the dev report instead

---

## Step 7: Output

1. Ensure the `./review-reports/` directory exists (create it if not)
2. Write both report files
3. Print a brief in-chat summary:

```
## 需求评审完成

### 给开发的注意事项
**判断: [可以开工 / 谨慎进行 / 暂不建议开工]**
Top 3 必须处理项:
1. 🔴 ...
2. 🔴 ...
3. 🔴 ...
（共 N 项注意事项：🔴 x / 🟡 y / 🟢 z）

### 给产品的问题清单
**判断: [可以进入开发 / 需要补充后再评审 / 需要重大调整]**
🚨 语意不清/表达模糊: M 处（详见报告「语意不清与模糊表达对照表」）
Top 3 阻塞问题:
1. 🔴 Q?. ...
2. 🔴 Q?. ...
3. 🔴 Q?. ...
（共 N 个问题：🔴 x / 🟡 y / 🟢 z）

报告已写入:
- ./review-reports/dev-review-YYYY-MM-DD.md
- ./review-reports/product-review-YYYY-MM-DD.md
```

---

## Important guidelines

- Write reports in **Chinese** (matching the team's language), with English technical terms where they add clarity
- **Two reports serve different purposes — do not mix them up:**
  - 开发报告 = **注意事项清单**（"实施时要小心这些点"），面向工程师，内容必须技术具体、可操作
  - 产品报告 = **问题清单**（"你需要回答这些问题 / 补充这些内容"），面向产品经理，每项都是一个明确问题或需要补充的点
- If something is a question *about what product wants*, it goes in the **product report**
- If something is a caveat *about how engineering will build it*, it goes in the **dev report**
- Be **specific and actionable** — every item should be concrete enough to directly act on. Avoid generic statements like "注意性能" or "需求不完整"
- Do not invent requirements — only evaluate what is written, and flag what is missing
- Ground the developer report in the **actual project context** from Step 2 (real file paths, real services, real frameworks)
- Ground the product report in the **actual project context** too — every question must include a「项目依据」citing concrete evidence from the codebase (existing code behavior, data models, API contracts, tech stack constraints). Product questions without project-level evidence are not actionable and will be ignored by the product team.
- When in doubt about a dimension, raise it as a question (product report) or a caveat (dev report) rather than silently passing it
- **🚨 Ambiguity scan is non-negotiable** — Step 3 must be executed for every review. The 「语意不清与模糊表达对照表」section in the product report must always exist, even if empty (in which case write "✅ 未发现语意不清或表达模糊的描述"). Missing this section is a bug in the review, not a stylistic choice. When a sentence in the requirement could be read two different ways by two different developers, that IS a problem that must be surfaced — do not resolve the ambiguity silently in your head, bring it back to product.
