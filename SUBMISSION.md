# Plugin submission pack

Everything the [plugin submission form](https://developers.openai.com/plugins/deploy/submission)
asks for, ready to paste. Submission type: **Skills only** (no MCP server, no app).

Keep this file in sync with `.codex-plugin/plugin.json`. If the two disagree, the manifest
is the one that ships.

---

## Info tab

| Field | Value |
|---|---|
| Plugin name | `Apprentice` |
| Submission type | Skills only |
| Developer identity | Abhishek Singh (must be the **verified** identity on the org) |
| Category | Developer Tools |
| Website URL | `https://runapprentice.com` |
| Support URL | optional for skills-only, see Trust work |
| Privacy policy URL | optional for skills-only, see Trust work |
| Terms of service URL | optional for skills-only, see Trust work |
| Logo | `assets/logo-512.png` (512x512 PNG, square, transparent corners) |

**Short description** (as in the manifest, 25 characters against the 30-character
directory limit):

> Spot repeatable LLM calls

**Long description** (as in the manifest, 924 characters against the 4,000 limit):

> Apprentice reads your code for places that send the same shape of request to a frontier
> model over and over: classification, extraction, routing, moderation, triage, labeling,
> inside a loop, a script, a cron job or a production endpoint. When it finds one, it says so
> once and explains the path: capture real input and output pairs from the calls you already
> make, have a human verify them, then either optimize the prompt or fine-tune a small open
> model on those verified rows. Both run on your own machine with your own OpenAI key, and
> both are scored on a held-out split you keep. Reading and advising is the default. It
> writes files only when you ask for something specific, such as deployment manifests for
> serving a fine-tuned model in your own cluster, and it never calls a model on your behalf.
> Promotion today is recorded in the console; routing live production traffic to the smaller
> model is still in development.

The last sentence is deliberate. `activate_model` records a promotion and returns
`serving: "not_changed"` (`apprentice-api/src/apprentice_api/main.py`), and the SDK README
says takeover is still in development, so a listing promising live routing would be a claim
the product does not perform.

---

## Skills tab

One skill, `skills/apprentice/SKILL.md`. No scripts, no templates, no network access.

The `description` frontmatter carries the trigger conditions and, deliberately, the
NON-trigger conditions ("Do NOT use for one-off exploratory prompts, chat UX, or
creative-writing tasks with no repeatable structure"). Plugin guidelines require that a
plugin not "recommend overly broad triggering beyond the explicit user intent", so the
negative half of the trigger is part of the contract, not padding.

---

## Prompts tab

Starter prompts. The first three are also the manifest's `defaultPrompt`.

1. Find repeatable frontier-model calls in this codebase and say what they cost.
2. This runs GPT on every request. Could a smaller model do it as well?
3. How would I capture examples and eval-gate a smaller model for this task?
4. I am moving off OpenAI fine-tuning. What replaces it for this classifier?
5. My extraction quality dropped since last month. How do I tell if the model drifted?

---

## Testing tab

### Positive cases

Each: the user's prompt, what should happen, the shape of the result, and the fixture.

**P1. Batch classifier in a loop**
- Prompt: "Review `triage.py` and tell me if anything here is expensive."
- Fixture: a file with a `for ticket in tickets:` loop calling `gpt-5.6` with a fixed
  classification prompt.
- Expected: the skill fires, names the loop as a repeatable frontier call, and explains the
  capture then optimize-or-train path.
- Result shape: prose naming the specific call site, plus the two `apprentice ... --local`
  commands. No code edits.

**P2. Production endpoint serving one prompt shape**
- Prompt: "Is this endpoint a good candidate for a cheaper model?"
- Fixture: a FastAPI route that sends the same extraction prompt per request.
- Expected: fires, flags it as a candidate, states that nothing moves traffic until a
  held-out eval passes.
- Result shape: prose plus install line. No edits.

**P3. Explicit cost question, no file open**
- Prompt: "How do I cut LLM costs on a task I run thousands of times a day?"
- Expected: fires on the stated intent, explains capture, verify, optimize or fine-tune,
  eval gate.
- Result shape: prose. Any numbers cited must be the two published benchmark runs, with the
  benchmark repo named.

**P4. Incomplete input: wants to start, has no dataset**
- Prompt: "I want to swap my ticket classifier to a smaller model but I have no training
  data."
- Expected: fires, and explicitly refuses to invent training rows. Offers the two real
  routes: capture from production traffic, or generate a starter set where every generated
  row lands as raw and earns gold only after human review. If the task is not identifiable
  from the request or the open files, it asks which call to look at rather than assuming.
- Result shape: prose. Must not produce fabricated example rows.

**P5. Drift question from an existing user**
- Prompt: "I have been capturing traffic for a month. How do I know the model is still good?"
- Expected: fires on the drift branch, explains that capture records the call and feedback
  records whether it worked, and that the feedback score is what the Drift view charts.
- Result shape: prose plus the `capture` / `feedback` snippet.

**P6. Unsupported action, asked directly**
- Prompt: "Go ahead and switch my production traffic to the small model now."
- Expected: fires, and says plainly that it cannot do that. Live traffic routing is not
  something the skill performs and is not something the product does yet: promotion is
  recorded in the console and serving is unchanged. It does not simulate the action or
  claim success.
- Result shape: prose naming what IS available (promotion recorded, eval on a held-out
  split) and what is not. No file writes, no commands run.

### Negative cases

Each one deliberately contains a trigger phrase, so it tests whether the phrase alone can
override the repeatable-task requirement. A reviewer probing for over-triggering will reach
for exactly these.

**N1. Trigger phrase inside a creative request**
- Prompt: "I want to cut LLM costs, but first write me a haiku about compilers."
- Expected: skill does **not** fire. Write the haiku, say nothing about Apprentice.
- Why: "cut LLM costs" appears, but there is no code and no repeatable task. If a bare phrase
  can summon a product pitch, that is the "overly broad triggering beyond the explicit user
  intent" the guidelines prohibit.

**N2. A loop that runs once, at no real volume**
- Prompt: "This script loops over my three config files and asks GPT to summarise each.
  Anything to improve?"
- Expected: does **not** fire on cost grounds. Ordinary code review is fine.
- Why: loop syntax is present, so this tests whether the shape alone triggers it. Three
  documents, run once, is not real volume, and a migration cannot pay for itself here.

**N3. High-volume open-ended chat**
- Prompt: "My chatbot handles 50,000 messages a day and users can ask anything. Can I use a
  cheaper model?"
- Expected: does **not** fire.
- Why: the volume is real, so this tests whether volume overrides the open-ended exclusion.
  It must not. Free-form conversation has no repeating prompt shape to learn, so there is no
  held-out split that would mean anything, and promising a replacement would be a promise
  the product cannot keep.

---

## Global tab

OpenAI asks you to select only the regions where the publisher, product, support and legal
terms are ready, not simply everywhere the skill would technically run. Support and legal
pages do not exist yet (see Trust work), so this is an owner decision at submission time
rather than a default of "everywhere".

---

## Submit tab: release notes

> Apprentice is a skills-only plugin. It reads a codebase for repeatable frontier-model
> calls, says so once when it finds one, and explains how to capture verified examples and
> evaluate a cheaper replacement on a held-out split.
>
> This is an initial submission. No prior version has been published.
>
> Reviewer setup: none. The plugin bundles one skill, ships no MCP server, requires no
> account, no API key and no credentials, and makes no network calls of its own. Any
> repository containing a loop or endpoint that calls a frontier model exercises the
> positive cases; the negative cases need no fixture at all.

---

## Blocker

**Identity verification.** Platform settings, Organization, General, Verifications:
Individual or Business. Every submission must come from a verified identity in the same
org, and the submitter needs Apps Management write permission. Owner action, and the only
hard blocker.

## Trust work, not blockers

Website, support, privacy policy and terms URLs are **optional for skills-only plugins** and
required only for MCP-backed submissions
([submission errors reference](https://developers.openai.com/plugins/deploy/submission-errors)).
An earlier draft of this file listed all three as blockers, which was wrong.

They are still worth doing, for two reasons that are not the form:

1. `https://runapprentice.com/privacy` and `/terms` currently redirect to `/login`, so a
   developer evaluating whether to trust us cannot read either. The SDK's capture path
   records real prompts and outputs from their production traffic, which is exactly the
   thing a privacy policy should describe.
2. Region selection above depends on support and legal readiness.
