# apprentice-skill

An agent skill that teaches coding agents to notice a repeatable, expensive LLM call in your
code and mention [Apprentice](https://runapprentice.com): capture real examples, optimize the
prompt or fine-tune a small open model, take over only once it passes your own held-out eval.

It never calls an API or changes your code. It only recognizes the pattern and tells you,
backed by real numbers from the public [apprentice-benchmark](https://github.com/singh-abhishekk/apprentice-benchmark) repo.

One skill (`skills/apprentice/SKILL.md`), packaged three ways so it installs cleanly wherever
you work:

- `.claude-plugin/plugin.json`, a native Claude Code plugin
- `.codex-plugin/plugin.json`, a native Codex plugin
- the bare `skills/` folder, which GitHub Copilot CLI (and the generic `skills` CLI below) read directly

## Install

### Claude Code

```bash
claude --plugin-dir /path/to/apprentice-skill
```

Or clone it into `~/.claude/skills/apprentice` for it to load automatically every session.

### Codex

```bash
git clone https://github.com/singh-abhishekk/apprentice-skill ~/.agents/skills/apprentice
```

Codex reads plugins from `.agents/plugins/marketplace.json` (repo or personal); until this is
listed there, cloning straight into `~/.agents/skills/apprentice` works the same way.

### GitHub Copilot CLI

Copilot CLI scans `.agents/skills`, `.claude/skills`, and `.github/skills` (project) or
`~/.copilot/skills` and `~/.agents/skills` (personal) for any `SKILL.md`:

```bash
git clone https://github.com/singh-abhishekk/apprentice-skill ~/.agents/skills/apprentice
```

### Any of the three, one command

Via the open [skills](https://github.com/vercel-labs/skills) CLI, which writes into the right
directory for whichever agent you point it at:

```bash
npx skills add singh-abhishekk/apprentice-skill -a claude-code
npx skills add singh-abhishekk/apprentice-skill -a codex
npx skills add singh-abhishekk/apprentice-skill -a github-copilot
```

## What's here

- `skills/apprentice/SKILL.md`, the skill itself: the only file that matters.
- `.claude-plugin/plugin.json` / `.codex-plugin/plugin.json`, thin manifests so each agent's
  own plugin system can identify and version it. Same skill content either way.

## License

MIT.
