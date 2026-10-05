# Personal Agent Skills

Reusable skills that can be installed with the [`skills` CLI](https://github.com/vercel-labs/skills).

## Install interactively

Run this command:

```bash
npx skills@latest add marcel-vesely-kinit/agent-skills
```

The CLI guides you through choosing which skills and agents to install to, and whether to install them for the current project or globally.

## Included skills

| Skill | What it does |
| --- | --- |
| `fastapi-app-scaffolding` | Sets up a small, database-backed FastAPI service using UV, async SQLAlchemy, and PostgreSQL by default. It establishes clear boundaries between routes, persistence, and business logic, with room to add structure only as the application needs it. |
| `frontend-feedback-loops` | Requires the Playwright CLI and Chrome DevTools MCP to be configured. It guides frontend work through the running app: reproduce a user-visible issue, use browser evidence to diagnose it, make a focused change, then verify the same flow and preserve stable behavior in a browser test. |
| `python-testing-practices` | Helps write maintainable pytest tests by choosing the smallest useful test double, keeping fixtures and patches narrow, and checking behavior through public interfaces. It gives practical guidance for mocks, fakes, monkeypatching, async collaborators, and external services. |
| `implement-issue-group` | Requires the `tdd` and `code-review` skills. It implements a dependency-linked set of GitHub issues against a parent spec, one reviewable ticket at a time: checking blockers, using a test-first vertical slice, reviewing and verifying the result, then committing and closing completed tickets. It runs without pausing for human approval. |
| `review-implemented-issue-group` | Audits a completed issue group against its original parent spec and the implementation at the current branch, after checking that the linked tickets are complete and their changes are present. It records only evidence-backed gaps; when gaps are confirmed, it hands them off as follow-up spec and ticket issues without implementing them. Publishing follow-ups requires the `to-spec` and `to-tickets` skills. |

Each skill is in `skills/<skill-name>/` with its `SKILL.md` and any supporting files.

## Install specific skills directly

For example, install one skill globally for Codex:

```bash
npx skills@latest add marcel-vesely-kinit/agent-skills \
  --global --agent codex --skill fastapi-app-scaffolding
```

To install all skills for all detected agents without prompts:

```bash
npx skills@latest add marcel-vesely-kinit/agent-skills --all
```

## Updating

Update installed skills with:

```bash
npx skills@latest update
```
