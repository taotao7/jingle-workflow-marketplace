---
name: review-requirements
description: >
  Multi-dimensional review of product requirements (PRD / spec / feature request).
  Grounds feasibility assessment in the current project's codebase and tech stack.
  Produces two reports: one for developers, one for the product team — covering
  completeness, clarity, feasibility, consistency, rationality, compliance, risk,
  testability, UX, data, priority, and dependencies.
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

## Step 3: Multi-dimensional evaluation

Evaluate the requirements on all 12 dimensions below. For each dimension, assign one of three ratings:

- **PASS** — requirement adequately addresses this dimension
- **NEEDS WORK** — partially addressed but has gaps
- **MISSING** — not addressed at all

Provide a one-line justification for each rating.

### Dimensions

| # | Dimension | Key questions |
|---|-----------|---------------|
| 1 | **完整性 (Completeness)** | Are functional scope, non-functional requirements (performance, availability, security), acceptance criteria, and success metrics all present? |
| 2 | **清晰性 (Clarity)** | Is the language unambiguous? Are there vague modifiers like "快速", "用户友好", "高性能" without concrete numbers? Is terminology consistent throughout? |
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

## Step 4: Write the developer report — 开发实施注意事项

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

## Step 5: Write the product report — 产品需求问题清单

**Purpose: this report is a list of questions/issues the product team needs to answer or address before the requirement can be implemented.** It is NOT a rewritten spec, NOT a technical assessment. Every item should be a clear question or concrete problem that product needs to fix in the PRD.

Create the file `./review-reports/product-review-YYYY-MM-DD.md` (same date/collision rules).

### Report structure

```markdown
# 产品需求问题清单：[需求标题]

> 评审日期: YYYY-MM-DD
> 整体判断: [可以进入开发 / 需要补充后再评审 / 需要重大调整]

## 总览

[2-3 句话概括需求文档的主要问题。例如"核心流程清晰，但缺少异常场景定义、未明确 MVP 范围、且涉及用户隐私数据但未说明合规要求，共提出 14 个问题"]

## 问题清单

> 每个问题独立成项，包含所属维度、问题描述、为什么重要、期望的回答格式。
> 优先级：🔴 阻塞（不解答无法开发）/ 🟡 重要（影响实施质量）/ 🟢 建议（锦上添花）

### 🔴 Q1. [一句话问题，例如"用户登录失败 5 次后应该如何处理？"]

**维度**: 完整性 / 清晰性 / 一致性 / 合理性 / 合规性 / 可测试性 / 优先级 / 依赖 / 风险 / UX / 数据

**问题描述**: 具体是哪一段、哪一点没讲清楚或缺失。可引用原文。

**为什么重要**: 这个问题如果不解决，会导致什么后果（开发无法推进 / 体验不一致 / 合规风险等）。

**期望回答**: 你希望产品以什么形式回答，例如：
- 明确数字：多少次之后锁定？锁定多久？
- 具体流程：锁定后用户如何解锁？
- 文案：锁定提示的文案是什么？

---

### 🔴 Q2. ...

### 🟡 Q3. ...

### 🟢 Q_n. ...

## 模糊表达对照表

> 需求中出现的、必须改写成可度量/可验证的模糊表述

| # | 原文 | 问题 | 建议改写 |
|---|------|------|----------|
| 1 | "响应要快" | 没有具体指标 | "接口 P95 响应 < 300ms" |
| 2 | "用户友好" | 无法验收 | "所有错误提示包含 [原因 + 解决建议]，全流程无死胡同" |

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
- **Expected answer format** helps product team give concrete answers (number, flow, copy, decision)
- Don't over-question — don't ask things that are clearly implied or that are engineering-side decisions (those go in the dev report)
- If a question is really a technical concern, don't put it here — put it in the dev report instead

---

## Step 6: Output

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
- When in doubt about a dimension, raise it as a question (product report) or a caveat (dev report) rather than silently passing it
