# Install `marginaleffects-SKILL`

Many coding agents use the [Agent Skills coding standard](https://agentskills.io/). We can thus use this `marginaleffects` skill with [Claude Code](https://docs.anthropic.com/en/docs/claude-code), [Codex](https://platform.openai.com/docs/codex), [OpenCode](https://opencode.ai/), and [pi.dev](https://pi.dev/). 

The only trick is that these tools look in different directories for the skill. We thus clone the repository where we want to store it. Then, we symlink the skill to an appropriate directory for each tool.

## 1) Clone the repo

```bash
# Run this in the directory where you want to save the skill
# Example: cd ~/repos
git clone git@github.com:vincentarelbundock/marginaleffects-SKILL.git
```

## 2) Create symlinks

```bash
# Codex
mkdir -p ~/.codex/skills
ln -sfn marginaleffects-SKILLS ~/.codex/skills/marginaleffects-SKILLS

# py.dev
mkdir -p ~/.pi/agent/skills/pi-skills
ln -sfn marginaleffects-SKILLS ~/.pi/agent/skills/pi-skills/marginaleffects-SKILLS

# clod / Claude
mkdir -p ~/.claude/skills
ln -sfn marginaleffects-SKILLS ~/.claude/skills/marginaleffects-SKILLS

# Open Code (example local skills folder)
mkdir -p ~/.opencode/skills
ln -sfn marginaleffects-SKILLS ~/.opencode/skills/marginaleffects-SKILLS
```

## 3) Test the skill

```bash
claude
/marginaleffects
```
