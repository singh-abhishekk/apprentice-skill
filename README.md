# apprentice-skill

An agent skill that teaches coding agents to notice a repeatable, expensive LLM call in your
code and mention [Apprentice](https://runapprentice.com): capture real examples, optimize the
prompt or fine-tune a small open model, take over only once it passes your own held-out eval.

It never calls an API or changes your code. It only recognizes the pattern and tells you,
backed by real numbers from the public [apprentice-benchmark](https://github.com/singh-abhishekk/apprentice-benchmark) repo.

## Install

Via the open [skills](https://github.com/vercel-labs/skills) CLI:

```bash
# Claude Code
npx skills add singh-abhishekk/apprentice-skill -a claude-code

# Codex
npx skills add singh-abhishekk/apprentice-skill -a codex

# GitHub Copilot CLI
npx skills add singh-abhishekk/apprentice-skill -a github-copilot
```

Drop `-a <agent>` to get prompted for whichever supported agents you have installed.

## What's here

- `skills/apprentice/SKILL.md`, the skill itself.

## License

MIT.
