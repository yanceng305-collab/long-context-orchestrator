# Recovery and Completion

Read this reference when continuing interrupted or existing work, when another agent may take over, or before declaring an orchestrated task complete. Recovery must work for a reader with no reliable inherited context.

## Zero-context recovery

Recover in this order:

1. **Re-establish the user contract.** Read the original request or its authoritative task record. A checkpoint may serve as that task record only when it faithfully preserves the objective, requested deliverable, scope, hard constraints, permission boundaries, and explicit stopping condition. If no trustworthy contract source is available, do not guess it; report the recovery blocker.
2. **Read the latest hot state.** If the host exposes resumable session state, use it as a continuity aid. Then read the current checkpoint or handoff and identify the current phase, exact next action, completed lanes, blockers, and evidence pointers. Do not treat conversational or generated summaries as verified facts.
3. **Inspect real current state.** Check the relevant workspace, changed files, diffs, generated artifacts, tests, commands, logs, document revisions, datasets, and external state. Real state is authoritative and can invalidate the checkpoint.
4. **Check freshness.** Compare source revisions, versions, timestamps, branches, inputs, environment assumptions, and external status with those recorded in the evidence. Mark only affected claims or lanes stale; do not invalidate unrelated evidence.
5. **Load cold evidence selectively.** Read only the lane reports, decision records, or verification artifacts needed for the next action. Reopen original sources for high-impact, disputed, stale, or weakly supported claims.
6. **Recompute remaining work.** Derive the plan from the user contract, verified real state, valid evidence, unresolved conflicts, and current constraints. Do not merely copy an old plan.

Recovery is successful only when the agent can state the objective, current phase, verified completed work, material unknowns or blockers, and exact next action consistently with real state.

## Reuse valid work; do not restart by habit

Do not repeat a completed lane merely because the agent or session changed. Reuse it when its scope matches the current task, its evidence is accessible, its material claims remain fresh, and its verification is sufficient for the downstream decision.

Reopen or rerun only the affected portion when:

- the underlying source, revision, input, or environment changed;
- the earlier scope does not cover the current question;
- the evidence is missing, ambiguous, or too weak for its current impact;
- a conflict or failed verification calls the conclusion into question;
- the user explicitly requests revalidation.

Prefer narrow revalidation over wholesale repetition. Preserve rejected routes and prior failures when they prevent the same dead end from being retried.

## Resume interrupted lanes

For each interrupted lane, inspect its assigned question, partial artifact or return, source scope, last verified result, outstanding work, and dependencies.

- If its output is complete and still valid, mark the lane complete only after the parent has inspected the evidence needed downstream.
- If usable partial work exists, resume from its next unresolved question. Do not discard it solely because the original worker is unavailable.
- If the original worker can safely continue, provide the current contract and changed facts rather than relying on its hidden context.
- If work must be reassigned, give the replacement the lane contract, partial evidence, open issues, and ownership boundary.
- If a lane is blocked, continue independent lanes and record the blocker, attempted paths, impact, and the concrete condition needed to proceed.

Do not dispatch the same lane twice unless independent duplication is intentional and valuable. Keep shared state parent-owned throughout recovery.

## Continue through the requested outcome

Research, code reading, document reading, and worker synthesis are intermediate work unless they are themselves the user's requested deliverable. In the same session, continue from validated findings to the requested outcome:

- for analysis, answer the actual decision or explanatory question;
- for implementation, apply the evidence to the authorized change and verify the integrated result;
- for a report or artifact, produce it in the requested format and destination;
- for review, return evidence-backed findings at the requested scope.

Do not stop at a research summary when the user asked for a change, decision, report, or other completed deliverable. Conversely, research does not grant permission to modify production files when the user asked only for analysis.

## Completion gates

Judge completion against the user contract and real evidence, not the presence of planning files, a checked box, or a worker's status message.

Before declaring complete, confirm:

- the requested deliverable exists, is readable, and addresses the actual objective;
- hard constraints, scope, authorization boundaries, and requested format or destination were respected;
- required analysis or implementation work is integrated rather than left as disconnected lane outputs;
- material claims have appropriate source support, and important inferences are not presented as facts;
- relevant tests, checks, commands, benchmarks, source audits, or artifact inspections were actually run or read, with results evaluated;
- material conflicts and unknowns are resolved, or are explicitly disclosed with their impact;
- blockers have been removed or honestly reported;
- the final result reflects current source and workspace state.
- the output language and level of detail match the user request and intended
  audience recorded in the task contract.

For high-impact, auditable, or formally contracted work, also map each required outcome to its artifact or verification evidence and confirm that retained evidence can trace consequential conclusions back to original sources.

A passed test does not compensate for a missing user deliverable. A complete-looking artifact does not compensate for failed verification. File presence alone never proves completion.

## Budgets, blockers, and incomplete outcomes

Budget or time exhaustion is a stopping condition, not evidence of completion. Preserve the latest hot state and report what remains, its impact, and the safest next action.

When meaningful progress cannot continue because required input, authority, access, a dependency, or external state is unavailable:

- distinguish completed, partially completed, stale, and unstarted work;
- identify the exact blocking condition and evidence for it;
- record safe attempts already made and avoid repeating them blindly;
- state what user input or state change would unblock the task;
- do not label the overall task complete or conceal high-impact gaps.

Use any host-specific paused, blocked, budget-limited, or completion status only according to that host's rules. The semantic distinction above remains valid even when no such status mechanism exists.

## Final handoff

Leave a concise handoff that states:

- the user objective and what was delivered;
- the authoritative artifact or changed state;
- verification performed and its result;
- material unresolved issues, conflicts, or limitations;
- whether the task is complete, incomplete, or blocked and why;
- the exact next action when work remains;
- pointers to the hot checkpoint and only the cold evidence most relevant to continuation.

The handoff should let a new reader resume without loading the full history. It supplements, and never overrides, the current workspace, current sources, and actual verification results.
