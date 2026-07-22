---
name: archaeologist
description: Inspect relevant repository history before changing old or unusual code, removing defensive logic, replacing dependencies, evolving public APIs, making architecture changes, or estimating substantial work. Use when prior fixes, reverts, compatibility constraints, analogous changes, or the origin of a code path could materially affect the decision.
---

# Archaeologist

> Your codebase has a past. Read the part that matters.

Use history to answer a current decision, not to produce a chronology. Treat history as evidence about constraints and prior attempts, not as authority that overrides current requirements.

## Frame the investigation

Before proposing the change:

1. State the decision history could change, such as whether to remove a guard, how wide an API migration is, or how large an estimate should be.
2. Identify the smallest relevant surface: symbols, lines, files, tests, dependencies, and analogous implementations.
3. Write one or two concrete history questions. Prefer “What failure caused this retry guard?” over “What is the history of this file?”
4. Check that repository history is available. If it is shallow, missing, or inaccessible, report that limitation and continue from current evidence without inventing a past.

Do not invoke a broad history review for a trivial edit whose behavior and intent are directly established.

## Follow the evidence trail

Start narrowly and expand only when findings affect the decision.

1. **Find the current and analogous code.** Search for the symbol, behavior, test name, error text, or old and new dependency names. Include similar implementations elsewhere in the repository.
2. **Locate origin and changes.** Use path history, pickaxe searches, and line attribution as appropriate:

   ```sh
   git log --oneline --decorate -- path/to/file
   git log -S'ExactToken' --oneline --all -- path/to/area
   git log -G'RelevantPattern' --oneline --all -- path/to/area
   git blame -L START,END -- path/to/file
   ```

   Add `--follow` for a likely single-file rename. Use blame as a pointer to commits, not as proof of intent.
3. **Read the decisive commits.** Inspect the patch, its parent when needed, commit message, nearby tests, and changed-file list. Check whether tests were added with the behavior and what invariant they encode.

   ```sh
   git show --stat COMMIT
   git show COMMIT -- path/to/code path/to/tests
   git show COMMIT^:path/to/file
   ```

4. **Look for failed or reversed approaches.** Search focused terms from the code, incident, issue, dependency, or API. Inspect reverts and the commits they revert. Do not infer failure merely because code later changed.
5. **Calibrate analogous work.** For estimates or migrations, inspect several genuinely similar commits. Note code, tests, migrations, rollout, compatibility work, and follow-up fixes. Treat diff size and elapsed commit dates as weak proxies, not effort measurements.
6. **Stop when history no longer changes the decision.** Avoid exhaustive logs, author profiles, and unrelated chronology.

Stay read-only during the investigation. Do not rewrite history, restore files, or discard working-tree changes.

## Separate evidence from inference

Label conclusions mentally or explicitly as:

- **Evidence:** directly observed in a diff, test, revert relationship, release note, or linked issue available to inspect.
- **Inference:** a plausible explanation derived from that evidence.
- **Unknown:** a question the available history cannot answer.

Prefer behavior encoded by a regression test over an unsupported commit-message claim. Prefer a commit message over guessing from blame. Verify that a historical constraint still applies to the current environment before preserving it.

Never claim that history proves author intent, production impact, or effort unless the repository contains direct support. Do not confuse repeated patterns with mandatory conventions.

## Convert findings into a decision

Report only evidence that materially affects the current work. Use this compact structure:

```text
History question: Why does the client suppress a second callback?
Evidence: Commit abc123 added the guard and a regression test for duplicate
callbacks from SDK v2; commit def456 later upgraded to v4 but retained the test.
Inference: The original SDK defect may be obsolete, but duplicate-callback behavior
remains an asserted compatibility invariant.
Decision impact: Keep the invariant while replacing the SDK-specific guard; run the
existing regression test and add a v4-path case.
Unknown: No history establishes whether downstream callers still depend on it.
```

Let history change the proposal when it reveals a live invariant, a previously failed approach, hidden rollout work, or a broader precedent. Say “No material historical evidence found” when that is the honest result; do not pad the report.

For estimates, present a range and name the historically observed scope drivers. Do not convert a prior commit’s line count into a precise schedule.
