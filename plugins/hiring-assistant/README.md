# hiring-assistant (Claude Code plugin)

Packages [mtgibbs/claude-hiring-assistant](https://github.com/mtgibbs/claude-hiring-assistant) (MIT) as an installable Claude Code plugin, so the framework can be used in any project instead of only inside a clone of that repo.

## Install

```
/plugin marketplace add <path-or-git-url-of-this-marketplace>
/plugin install hiring-assistant@hiring-assistant-marketplace
```

## Use

1. `/hiring-assistant:hiring-setup` - installs the org template into `.org/example/`, creates intake folders, offers privacy `.gitignore` rules, and runs the setup wizard.
2. Drop resumes into `resumes/developers/new/<Level>/` or `resumes/managers/new/<Level>/`.
3. `/hiring-assistant:evaluate-resumes` - evaluate, summarize, prep interviews, fact-check, report.
4. Fill in `INTERVIEW_NOTES.md` per candidate, then `/hiring-assistant:assess-interviews`.

Ad hoc requests ("compare these three candidates", "prepare interview questions for Jane") route to the bundled agents automatically.

## Contents

| Component | Source in original repo |
|---|---|
| `agents/` (12 agents) | `.claude/agents/` |
| `commands/evaluate-resumes`, `assess-interviews` | `.claude/commands/` |
| `commands/hiring-setup` | new |
| `skills/hiring-methodology` | `AI.md`, `CLAUDE.md`, `config/schema.md`, `examples/` |
| `skills/hiring-setup` | `.org/example/`, `.gitignore` |

Not carried over: the GitHub Copilot instructions and VS Code tasks (not applicable to a Claude plugin).

## Notes

Organization config and all candidate data stay in the user's project; the plugin itself is read-only. Automated screening of job applicants is regulated in several jurisdictions (for example, the EU AI Act treats it as high-risk), so check your obligations before using this on real candidates and keep a human making the decisions.
