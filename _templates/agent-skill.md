---
# AI AGENT SKILL TEMPLATE
# Copy to _resources/<skill-name>.md. The page will live at /resources/<skill-name>/.
# Put the skill files in assets/skills/<skill-name>/ and a zipped copy at
# assets/skills/<skill-name>.zip so visitors can download it in one click.

title: "Skill name"
resource_type: agent-skill
excerpt: "What the skill does, in one sentence."
order: 10
last_updated: 2026-01-01

# Summary box (top of the page)
audience: "Teachers and developers"
works_with: [Claude Code]     # agents or apps the skill has been tested with
license: "MIT"
downloads:
  - label: "Download skill (.zip)"
    file: /assets/skills/skill-name.zip
links:
  - label: "View SKILL.md"
    url: /assets/skills/skill-name/SKILL.md

toc: true
toc_label: "Contents"
---

## What it does

Describe the task the skill handles and when the agent should use it.

## Install

1. Download and unzip the skill.
2. Copy the `skill-name` folder into your agent's skills folder (for Claude Code: `~/.claude/skills/`).
3. Restart the agent.

## Example

A prompt that triggers the skill, and a short sample of the result.

```text
Example prompt here
```

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Instructions the agent reads |

## Limitations

What the skill does not do, and anything to check before using its output with students.
