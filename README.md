# Dovetail for Codex

Dovetail is a native Codex plugin containing eight composable skills for prompt
design, delegated work, evaluation, self-play, skill authoring, steering, and
session handoff.

This repository is the Codex port of
[OpenCnid/dovetail](https://github.com/OpenCnid/dovetail). It replaces the old
copied-skill bridge with Codex plugin discovery and rewrites the platform-aware
instructions around observed Codex primitives.

## Install

Add the repository marketplace and install the plugin:

```bash
codex plugin marketplace add OpenCnid/dovetail-codex
codex plugin add dovetail-codex@opencnid
```

Start a new Codex session after installing or updating so the skill catalog is
rebuilt from the installed plugin.

## Skills

| Skill | Purpose | Invocation |
|---|---|---|
| `prompt-engineering` | Structure precise prompts and agent instructions | automatic |
| `hypershot-protocol` | Prime output form without content-heavy examples | automatic |
| `subagent-composition` | Partition and coordinate bounded delegated work | automatic |
| `self-play` | Test artifacts with clean-room adversarial evaluation | automatic |
| `judge-composition` | Build evidence-backed evaluation rubrics and judges | automatic |
| `better-skill-creator` | Create or revise Codex-native skills | automatic |
| [`spark-steering`](#spark-steering) | Find out why a workflow keeps going wrong and what to change | explicit only |
| `upsum` | Close a session with a durable record and compact summary | explicit only |

Invoke an explicit-only skill by name, for example:

```text
Use $spark-steering to diagnose why this workflow keeps drifting.
Use $upsum to close this work session.
```

### Spark Steering

Use `$spark-steering` to debug **how Codex does the work**, rather than the code
itself. When a mistake keeps happening, it helps you find the smallest change
that would prevent it. Sometimes Codex is missing a fact or can’t see what’s
already been done. Other times no one has decided what “done” means or who
should check the result. A new tool won’t settle either question.

**Example: “Did you run the tests?”** Codex fixes a bug and says it’s done.
Only when you ask does it run `npm test`, the command this project uses to check
that the code still works. Now you know Codex *can* run the tests without new
tools or permissions. Check the project instructions: do they say *when* to run
them? If not, decide which rule you mean:

- **If every code change needs that check**, put one sentence in `AGENTS.md`,
  the project’s instructions for Codex: “After changing code, run `npm test`
  and report the result.” Start a fresh session and ask for a small code
  change without mentioning tests. Does Codex run them and tell you what happened?
- **If the check is only needed before publishing a new version**, put that
  sentence in the release checklist instead: “Before publishing, run `npm test`
  and report the result.” Try the release procedure and an ordinary edit. The
  instruction should apply to the first, not the second.

A reminder fixes this run. **Put the rule where it’s needed, then try one task
where it should apply and one where it shouldn’t.** That checks whether you
fixed the workflow or just wrote a more convincing reminder.

## Native Codex design

- `.codex-plugin/plugin.json` points directly at `./skills/`.
- Skill routing stays concise because names and descriptions occupy the
  model-visible catalog on every turn.
- Full procedures live in `SKILL.md`; branch-specific depth lives in
  `references/` and loads only when needed.
- `agents/openai.yaml` provides UI metadata, starter prompts, and explicit-only
  policy.
- Delegated workflows choose `fork_turns` deliberately and account for the
  shared filesystem.
- Repository guidance stays below the default combined `AGENTS.md` context
  budget.

The evidence and decisions behind the port are in
[`docs/codex-surface-map.md`](docs/codex-surface-map.md).

## Validate

```bash
python scripts/test-codex-package.py
```

The check verifies the native manifest, marketplace entry, skill inventory,
frontmatter, UI metadata, invocation policy, and prompt-budget constraints.

## License

[CC BY 4.0](LICENSE.md) — OpenCnid Labs. The original lineage and third-party
attributions remain recorded in each skill and in the release notes.
