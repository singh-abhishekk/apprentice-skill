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
| Support URL | **TODO, see Blockers** |
| Privacy policy URL | **TODO, see Blockers** |
| Terms of service URL | **TODO, see Blockers** |
| Logo | `assets/logo-512.png` (512x512 PNG, transparent corners) |

**Short description** (as in the manifest):

> Spot repeatable frontier-LLM calls in code and suggest an eval-gated small-model replacement.

**Long description** (as in the manifest):

> Apprentice reads your code for places that send the same shape of request to a frontier
> model over and over: classification, extraction, routing, moderation, triage, labeling,
> inside a loop, a script, a cron job or a production endpoint. When it finds one, it says so
> once and explains the path: capture real input and output pairs from the calls you already
> make, verify them, then either optimize the prompt or fine-tune a small open model on them.
> Nothing moves traffic until the replacement passes your own held-out evaluation, and
> rollback is immediate. The skill only reads and advises. It never edits your code, never
> calls a model on your behalf, and never sends your code anywhere.

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

**P4. User has no dataset yet**
- Prompt: "I want to try this but I have no training data."
- Expected: fires, and explicitly refuses to invent training rows. Offers the two real
  routes: capture from production traffic, or generate a starter set where every generated
  row lands as raw and earns gold only after human review.
- Result shape: prose. Must not produce fabricated example rows.

**P5. Drift question from an existing user**
- Prompt: "I have been capturing traffic for a month. How do I know the model is still good?"
- Expected: fires on the drift branch, explains that capture records the call and feedback
  records whether it worked, and that the feedback score is what the Drift view charts.
- Result shape: prose plus the `capture` / `feedback` snippet.

### Negative cases

**N1. One-off exploratory prompt**
- Prompt: "Write me a haiku about compilers."
- Expected: skill does **not** fire. No mention of Apprentice.
- Why: no repeatable structure and no code. The frontmatter excludes creative writing
  explicitly. Suggesting a product here would be the "overly broad triggering" the plugin
  guidelines prohibit.

**N2. Single ad hoc script run once**
- Prompt: "This script calls the API once to summarise a README. Anything to improve?"
- Expected: does **not** fire on cost grounds. Ordinary code review is fine.
- Why: volume is not real. A single call is not a repeatable task, and flagging it would
  waste the user's attention on a migration that cannot pay for itself.

**N3. Chat UX feature**
- Prompt: "I am building a chatbot. Users can ask anything about their data."
- Expected: does **not** fire.
- Why: open-ended conversation has no repeating prompt shape to learn, so the premise of a
  small-model replacement does not hold. Claiming otherwise would be a promise the product
  cannot keep.

---

## Global tab

No restriction. The skill sends nothing anywhere and reads only what the user has already
opened, so there is no data-residency reason to limit regions.

---

## Blockers before this can be submitted

These are not optional. The form requires them and a submission without them is rejected.

1. **Identity verification.** Platform settings, Organization, General, Verifications:
   Individual or Business. Every submission must come from a verified identity in the same
   org, and the submitter needs Apps Management write permission. Owner action.
2. **Privacy policy URL, public.** `https://runapprentice.com/privacy` currently redirects
   to `/login`, so it does not exist. It has to describe what the SDK captures, since capture
   records real prompts and outputs from the customer's own traffic.
3. **Terms of service URL, public.** `https://runapprentice.com/terms`, same, currently
   redirects to `/login`.
4. **Support URL.** Any public route that reaches a human. Not yet chosen.

Items 2 to 4 are pages on runapprentice.com, not changes to this repo.
