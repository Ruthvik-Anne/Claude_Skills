# Claude Skills — Mini Documentation

> **TL;DR:** A Skill is a folder with a `SKILL.md` file that teaches Claude a repeatable workflow. Claude only reads the full file when the task matches, so keeping many skills installed is cheap.

---

## What are Claude Skills?

Claude Skills (also called Agent Skills) are a feature by Anthropic that lets users package repeatable workflows, instructions, reference materials, and optional executable scripts into discoverable folders that Claude can load on demand.

The central design idea is **progressive disclosure**: Claude is told only the name and one-line description of every installed Skill at the start of a session, then reads the full `SKILL.md` only when the user's request matches, and only opens additional bundled files (templates, references, scripts) when a step actually needs them.

Skills were released as an **open standard** in December 2025 — portable across tools and platforms. The same skill can work whether you're using Claude or other AI platforms.

---

## Anatomy of a Skill

```
my-skill/
├── SKILL.md        ← Required. Instructions + YAML frontmatter.
├── scripts/        ← Optional. Executable scripts.
├── examples/       ← Optional. Sample outputs.
└── resources/      ← Optional. Reference files.
```

**Minimal `SKILL.md`:**

```yaml
---
name: my-skill
description: Brief one-line description for skill discovery (keep concise)
---

# Instructions

Detailed instructions Claude follows when this skill activates.
```

The `description` field is critical — it's what Claude uses to decide whether to auto-load the skill.

---

## How to Use Skills

### Claude Code (Terminal)

Where you store a skill determines who can use it:

| Scope      | Path                                       |
|------------|--------------------------------------------|
| Personal   | `~/.claude/skills/<skill-name>/SKILL.md`   |
| Project    | `.claude/skills/<skill-name>/SKILL.md`     |
| Enterprise | Managed via org settings                   |

**Install a skill:**
```bash
mkdir -p ~/.claude/skills/my-skill
# create SKILL.md inside it
```

**Use a skill:**
```bash
# Direct invocation
/my-skill

# Or just describe your task — Claude auto-invokes if description matches
```

**Bundled skills** (available in every session): `/code-review`, `/batch`, `/debug`, `/loop`, `/claude-api`, `/run`, `/verify`.

Claude Code **watches skill directories live** — adding or editing a skill takes effect in the current session without restarting.

---

### Claude.ai (Web/App)

Upload skills via **Settings → Capabilities → Skills**.

Skills you upload are available across your conversations. Claude auto-activates them when your request matches the skill's description, or you can reference them directly.

---

### Claude API

Use `client.beta.messages.create()` with a `container.skills` parameter:

```python
response = client.beta.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=1000,
    container={
        "skills": [
            {"type": "anthropic", "skill_id": "xlsx", "version": "latest"}
        ]
    },
    tools=[{"type": "code_execution_20250825", "name": "code_execution"}],
    messages=[{"role": "user", "content": "Create an Excel report..."}],
    betas=["code-execution-2025-08-25", "files-api-2025-04-14", "skills-2025-10-02"]
)
```

Anthropic provides first-party skill IDs for common tasks: `xlsx`, `pdf`, `pptx`, `docx`, etc.

---

### Cowork (Desktop)

Skills placed in the project directory (`.claude/skills/`) are picked up automatically when Cowork operates on that folder. No extra configuration needed — it follows the same path resolution as Claude Code.

---

## Key Frontmatter Fields

```yaml
---
name: deploy                         # Skill name / slash command
description: Deploy app to prod      # One-liner for auto-discovery
context: fork                        # "inline" (default) or "fork" (subagent)
disable-model-invocation: true       # Prevent Claude from auto-triggering
---
```

| Field | Purpose |
|-------|---------|
| `description` | Tells Claude when to auto-load this skill |
| `context: fork` | Run in a subagent (isolated context) |
| `disable-model-invocation` | Only invocable by you via `/skill-name` |

---

## Dynamic Context Injection

Inject live shell output into skill instructions:

```markdown
## Current git diff

!`git diff HEAD`

## Instructions
Summarize the changes above...
```

Claude Code runs the backtick command and inlines the output before Claude reads the skill.

---

## Skill Priority (when names conflict)

```
Enterprise > Personal (~/.claude) > Project (.claude/) > Plugin
```

---

## Quick Reference

| Tool | Install path | Invoke |
|------|-------------|--------|
| Claude Code | `~/.claude/skills/<name>/SKILL.md` | `/name` or auto |
| Claude.ai | Settings → Capabilities → Skills | Auto |
| Claude API | `container.skills` param | Per request |
| Cowork | `.claude/skills/<name>/SKILL.md` | Auto |

---

**Official docs:** [code.claude.com/docs/en/skills](https://code.claude.com/docs/en/skills) · **Open standard:** [agentskills.io](https://agentskills.io) · **Community skills:** [github.com/travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills)
