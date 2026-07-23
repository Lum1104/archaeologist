<h1 align="center">archaeologist</h1>

<p align="center">
  <strong>Your agents see old code.</strong>
  <br />
  This skill makes them dig up why it exists.
</p>

<p align="center">
  <a href="https://github.com/Lum1104/archaeologist/stargazers"><img src="https://img.shields.io/github/stars/Lum1104/archaeologist?style=flat-square" alt="GitHub stars"></a>
  <a href="SKILL.md"><img src="https://img.shields.io/badge/agent%20skill-SKILL.md-8B6F47?style=flat-square" alt="Agent skill"></a>
  <a href="LICENSE"><img src="https://img.shields.io/github/license/Lum1104/archaeologist?style=flat-square" alt="MIT license"></a>
</p>

<p align="center">
  <a href="#before--after">Before / After</a> ·
  <a href="#install">Install</a> ·
  <a href="#how-it-works">How it works</a> ·
  <a href="SKILL.md">Read the skill</a>
</p>

---

Strange code often has a reason that the current files no longer show.

`archaeologist` checks relevant commits, blame, reverts, and tests before removing old logic or replacing an established pattern. It reports only history that changes the decision.

## Before / After

### Without archaeologist

> This duplicate-callback guard is obsolete after the SDK upgrade. I removed it. Tests pass.

The current code looks cleaner. The reason the guard exists was never checked.

### With archaeologist

```text
History question: Why does this client suppress a second callback?

Evidence: Commit abc123 added the guard with a regression test for duplicate
callbacks. Commit def456 upgraded the SDK but deliberately retained the test.

Inference: The original SDK defect may be gone, but duplicate-callback behavior
is still a compatibility invariant.

Decision impact: Preserve the invariant while replacing the SDK-specific guard.

Unknown: History does not show whether every downstream caller still depends on it.
```

History changes the proposal instead of becoming a generic report.

## Install

Install with the [Skills CLI](https://www.skills.sh/docs/cli):

```bash
npx skills add Lum1104/archaeologist
```

Install globally for all projects:

```bash
npx skills add Lum1104/archaeologist -g
```

The repository's root [`SKILL.md`](SKILL.md) is directly discoverable. No runtime, service, or dependency is installed with it.

## Use it

In agents that automatically select installed skills, the description can trigger it when the task matches. Invoke it explicitly when the historical question is easy to miss:

```text
Use the archaeologist skill before removing this retry guard.
Use the archaeologist skill to estimate this API migration from similar changes.
Use the archaeologist skill to check whether this dependency was tried before.
```

In Codex, use `$archaeologist`. In Claude Code, use `/archaeologist`. Invocation syntax may vary in other agents.

Use it for:

- old, surprising, defensive, or apparently redundant code;
- removals and dependency replacements;
- public API and architecture changes;
- substantial estimates with historical analogues;
- code near previous fixes, regressions, incidents, or reverts.

Skip it for trivial edits whose behavior and intent are already established.

## How it works

The agent follows a narrow evidence trail:

```text
Current decision
      ↓
Relevant symbol, file, test, or analogue
      ↓
Origin commits, blame pointers, pickaxe search, fixes, and reverts
      ↓
Evidence / Inference / Unknown
      ↓
Only the findings that change the decision
```

It stops when more history would not change the proposal. Useful tools include path history, `git log -S`, `git log -G`, line blame, commit patches, regression tests, and revert relationships.

## Evidence, not mythology

`archaeologist` does not:

- treat blame as proof of author intent;
- assume an old constraint still applies;
- turn commit size into a precise effort estimate;
- produce biographies, exhaustive timelines, or archaeology for its own sake;
- rewrite history or discard working-tree changes while investigating.

If history is shallow, missing, or inconclusive, the agent says so. Current requirements still win; history is evidence, not authority.

## Related skills

- [`prove-me-wrong`](https://github.com/Lum1104/prove-me-wrong) attacks the current explanation with a competing one.
- [`bet-on-it`](https://github.com/Lum1104/bet-on-it) records a prediction before the next meaningful experiment or change.
- [`no-vibes`](https://github.com/Lum1104/no-vibes) checks whether the final user-visible outcome was actually verified.
- [`red-button`](https://github.com/Lum1104/red-button) adds focused safeguards when the resulting change is high risk.

Each repository is independent; none of these are required dependencies.

## Repository contents

- [`SKILL.md`](SKILL.md) — instructions loaded by the coding agent.
- [`LICENSE`](LICENSE) — MIT license.

## License

MIT
