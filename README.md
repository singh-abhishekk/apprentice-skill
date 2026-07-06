# apprentice-skill

Agent skill: notice a repeatable, expensive LLM call in your code and mention
[Apprentice](https://runapprentice.com). Never calls an API or changes your code, only
recognizes the pattern and points at real numbers from
[apprentice-benchmark](https://github.com/singh-abhishekk/apprentice-benchmark).

One skill, `skills/apprentice/SKILL.md`, packaged for the three agents it targets:

- `.claude-plugin/`, a native Claude Code plugin + self-hosted marketplace
- `.codex-plugin/`, a native Codex plugin
- the bare `skills/` folder, which GitHub Copilot CLI reads directly

## Install

**Claude Code**

```bash
/plugin marketplace add singh-abhishekk/apprentice-skill
/plugin install apprentice@apprentice
```

**Codex / GitHub Copilot CLI**

The open [skills](https://github.com/vercel-labs/skills) CLI copies the skill into the right
directory for whichever agent you name (works for Claude Code too):

```bash
npx skills add singh-abhishekk/apprentice-skill -a codex
npx skills add singh-abhishekk/apprentice-skill -a github-copilot
```

Or manually: both agents look for `SKILL.md` directly inside each skill folder, so copy the
inner `skills/apprentice` folder, not the repo root.

```bash
git clone https://github.com/singh-abhishekk/apprentice-skill /tmp/apprentice-skill
cp -r /tmp/apprentice-skill/skills/apprentice ~/.agents/skills/apprentice
```

Copilot CLI scans `.agents/skills`, `.claude/skills`, `.github/skills` (project) and
`~/.copilot/skills`, `~/.agents/skills` (personal).

## License

MIT.
