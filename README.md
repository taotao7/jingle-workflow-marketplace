# Jingle Workflow Marketplace

Shared Claude Code plugins for the Jingle team — monorepo layout.

Repository: **[taotao7/jingle-workflow-marketplace](https://github.com/taotao7/jingle-workflow-marketplace)**

## Installation

In Claude Code:

```bash
# Add this marketplace (GitHub shorthand)
/plugin marketplace add taotao7/jingle-workflow-marketplace

# Install the plugin
/plugin install jingle-workflow@jingle-workflow-marketplace
```

Or for local development:

```bash
/plugin marketplace add /path/to/jingle-workflow-marketplace
```

Updating to the latest version:

```bash
/plugin marketplace update jingle-workflow-marketplace
```

## Available plugins

### jingle-workflow

Common product & engineering workflows.

#### Skills

| Skill | Command | Description |
|-------|---------|-------------|
| `review-requirements` | `/jingle-workflow:review-requirements` | Multi-dimensional PRD review, outputs **developer 注意事项清单** + **product 问题清单** |

## Usage examples

```bash
# Review a local requirements doc
/jingle-workflow:review-requirements docs/prd/login-v2.md

# Review from a URL (Feishu doc, Confluence, etc.)
/jingle-workflow:review-requirements https://example.feishu.cn/docx/xxx

# Review inline text
/jingle-workflow:review-requirements 用户可以通过手机号登录，支持找回密码...

# Auto-detect requirements in current project
/jingle-workflow:review-requirements
```

### Output

The skill writes two reports to `./review-reports/` in your current project, each serving a different purpose:

| File | 面向 | 内容定位 |
|------|------|----------|
| `dev-review-YYYY-MM-DD.md` | 开发 | **实施注意事项清单** — 具体到每一点工程上要小心的地方（数据迁移、接口兼容、安全、性能、边界情况、回滚预案…），按 🔴🟡🟢 分级 |
| `product-review-YYYY-MM-DD.md` | 产品 | **需求问题清单** — 每一项是产品需要回答或补充的具体问题（缺失的流程、模糊表述、缺失的验收标准…），附"为什么重要"和"期望回答格式" |

### Evaluation dimensions

Both reports are grounded in a 12-dimension evaluation: 完整性 · 清晰性 · 可行性 · 一致性 · 合理性 · 合规性 · 可测试性 · 优先级 · 依赖 · 风险 · UX/交互 · 数据。Feasibility is assessed against the **actual current project's codebase and tech stack**.

## Contributing

See [CLAUDE.md](./CLAUDE.md) for monorepo conventions and how to add new plugins or skills.

### Adding a new plugin

1. Create `plugins/<plugin-name>/.claude-plugin/plugin.json`
2. Add skills under `plugins/<plugin-name>/skills/<skill-name>/SKILL.md`
3. Register the plugin in `.claude-plugin/marketplace.json`
4. Validate: `claude plugin validate .`
5. Open a PR

### Adding a skill to an existing plugin

1. Create `plugins/<plugin>/skills/<skill-name>/SKILL.md`
2. Reload: `/reload-plugins`
3. Open a PR
