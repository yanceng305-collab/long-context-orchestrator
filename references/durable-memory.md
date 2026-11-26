# Durable Memory and Evidence

Read this reference when the work may cross agent, phase, turn, compaction, session, or review boundaries. The purpose is to preserve the user contract and decision-relevant evidence without turning every task into a research project.

## Choose the lightest sufficient persistence level

Persistence is independent of execution topology. A single-agent run can need durable evidence, and a parallel run can finish in one session. Start at the lightest level that safely carries the work across its actual boundaries, then upgrade when the task changes.

### Ephemeral

Use no task artifact only when all of these are true:

- the investigation and synthesis can finish in the current uninterrupted context;
- no worker or later phase will depend on intermediate findings;
- the material is small enough to retain accurately without likely compaction;
- no pause, handoff, audit, formal artifact, or cross-session recovery is required;
- claims can still be located directly in the sources used for the final response.

Ephemeral does not mean that evidence is optional. It means there is no intermediate state that must survive a boundary. If any condition stops being true, upgrade before that state becomes vulnerable.

### Checkpoint

Use a compact, parent-owned checkpoint when work crosses a turn or phase, may be compacted or interrupted, waits on external state, has downstream dependencies, or needs a handoff. Any run that actually dispatches workers and later relies on their results has crossed an agent boundary and therefore requires at least a parent-owned checkpoint.

The checkpoint is hot state, not a report. Keep at least these semantic fields, using the project's existing format and adding task-specific fields only when they materially improve recovery:

1. **Objective and hard constraints** — the requested outcome, deliverable, scope, permissions, and prohibited actions.
2. **Current phase and exact next action** — where the work is and what should happen next.
3. **Completed work, artifacts, and verification** — what was actually done and what evidence confirms it.
4. **Key findings and decisions** — including rejected routes when their rationale matters downstream.
5. **Conflicts, unknowns, and blockers** — especially unresolved items that can change the result.
6. **Evidence pointers and freshness needs** — locations, versions, dates, revisions, and anything that must be rechecked before reuse.

Update the checkpoint at meaningful boundaries: after a decision that downstream work will rely on, before a handoff or likely compaction, when pausing, and when the next action changes materially. Do not update it mechanically after a fixed number of actions.

### Evidence bundle

Add cold evidence when findings are too large for the hot checkpoint, later phases depend on their details, multiple workers or sources must be reconciled, or a high-impact claim needs reviewable provenance.

Cold evidence should be partitioned by question, lane, phase, or claim so later readers can load only what they need. A worker may return a short, self-contained result directly, but should write an owned evidence artifact when the result is large, likely to be compacted, needed by a downstream consumer, disputed, or subject to review. Record, as applicable:

- scope investigated and sources actually accessed;
- concrete source anchors and relevant version or date;
- confirmed facts, inferences, unknowns, and implications;
- conflicts, caveats, falsifiers, and failed or rejected routes;
- commands, measurements, or verification results needed to reproduce a claim.

The parent is the sole writer of shared hot state and any promoted shared ledger. Workers write only their assigned artifacts or return results. The parent reviews worker output, spot-checks material claims against original sources, and promotes only the minimum decision-relevant, non-secret information. A worker status such as `DONE` reports lane state; it does not validate the lane's claims.

Use a separate shared claim ledger only when conflicts, formal handoff, audit, version-sensitive facts, or a small set of high-impact claims justify it. Do not require one for routine work.

### Durable program

Use a durable program when the work must survive sessions or a new agent, has multiple phases and downstream consumers, supports a formal review or reproducible decision, preserves the history of high-impact conflicts, or the user explicitly requests a persistent research workspace.

Regulatory, safety, audit-grade, or explicitly reproducible deliverables use
this level when another reviewer must reconstruct the decision from evidence.
When resuming an existing durable workspace or cross-session handoff, keep it at
this level until no remaining task, reviewer, or deliverable depends on its
history and recovery state; do not downgrade merely because the resumed work
may finish in the current session.

A task continued from a prior session or transferred to a new agent uses this
level by default. An equivalent host-managed durable workspace may satisfy the
same contract, but the parent must map where hot state, cold evidence, history,
verification, and final artifacts live; a conversation resume token or lone
checkpoint is not by itself equivalent.

A durable program needs these roles, not prescribed filenames or directories:

- bounded hot state for recovery;
- cold evidence with provenance;
- current synthesis or decision records;
- verification evidence;
- the requested final artifact;
- enough historical trace to explain consequential changed or rejected conclusions.

Reuse established project conventions. Do not create a parallel state system when an existing artifact already satisfies the contract.

## Upgrade triggers

Upgrade from Ephemeral to Checkpoint before dispatching workers, crossing a meaningful phase, pausing, handing off, waiting, or risking compaction.

Upgrade from Checkpoint to an Evidence bundle when summaries would discard evidence needed for later reasoning, when sources or workers conflict, when claims need source-level review, or when the parent cannot safely retain the worker results in context.

Upgrade to a Durable program when recovery across sessions or agents, formal audit, reproducibility, historical decisions, or long-lived delivery becomes part of the task.

An upgrade preserves existing useful state; it does not require rewriting all prior work. Do not downgrade while downstream work still depends on evidence that would be lost.

## Storage and ownership

- Honor a user-specified destination and the target project's artifact or scratch conventions.
- Otherwise choose a safe writable location that does not overwrite user content or pollute the product tree. Record the location in hot state.
- Determine whether task artifacts are tracked, ignored, or outside version control; do not assume. Do not commit or publish working artifacts unless the user requested it.
- Keep parallel workers away from shared mutable files. Assign each worker an exclusive artifact path or accept a direct return, then let the parent merge reviewed findings.
- Never copy secrets, credentials, private keys, tokens, or unrelated sensitive content into checkpoints, evidence, logs, or handoffs. Prefer precise pointers and redacted summaries over raw dumps.
- Preserve only information needed for recovery, reasoning, verification, or the requested deliverable. Large raw source material remains at its authoritative location when it can be referenced safely.
- If no safe writable task-specific location exists, do not claim durable
  recovery. Keep the task in an ordinary bounded workflow when possible, or
  report the storage limitation and request an appropriate location before
  crossing an agent, phase, compaction, or handoff boundary that requires a
  checkpoint.

## Boundaries with host-provided state

Do not assume that any host offers sessions, compaction, goals, snapshots, long-term memory, or artifact services. Use them when available and appropriate, but keep the workflow correct without them.

- **Session or thread state** can maintain conversational continuity. It is not a reviewable source of truth.
- **Compaction or summaries** can keep a run moving. They do not replace source anchors, conflict records, or validated artifacts.
- **Goals or durable run contracts**, when the host supports them, should be used only when the user explicitly requests such a goal and completion is objectively verifiable. A goal stores the completion contract, not the detailed evidence.
- **Long-term memory**, when available and appropriate, is for stable cross-task preferences, corrections, and reusable process knowledge. Do not store case-specific findings, project state, or current evidence there, and never treat recalled memory as authority or permission.
- **Task artifacts** preserve reviewed, case-specific state, evidence, decisions, and deliverables. Link them to original sources rather than duplicating all source material.

Native state and task artifacts are complementary: native state supports continuity; artifacts support review, handoff, recovery, and evidence integrity.

## Progressive retrieval

Recover and reason through this hierarchy:

```text
hot checkpoint or handoff
  -> relevant cold evidence or synthesis artifact
    -> original file, diff, test, log, document, dataset, or authoritative source
```

Load only the cold material needed for the current decision. Reopen the original source for high-impact, disputed, stale, or surprising claims. Original and current real-world state can overturn every summary, checkpoint, ledger, or memory entry.
