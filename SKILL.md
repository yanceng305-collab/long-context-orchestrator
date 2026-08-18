---
name: long-context-orchestrator
description: >
  Orchestrate long-context, research-heavy, multi-source, multi-repository,
  or complex analysis tasks that may exceed a single agent's effective context.
  Use when a task requires extensive code/document/web exploration, comparing
  multiple implementations or sources, preserving many findings for later
  synthesis, or performing several largely independent investigations before
  reaching a final conclusion. Use subagents and durable Markdown working files
  to avoid losing important information through context compaction. Do not use
  for small, localized tasks that can be completed reliably in one agent context.
---

# Long Context Orchestrator

## Purpose

Use this skill to prevent information loss on large tasks.

The goal is NOT to maximize the number of agents.

The goal is to keep the main agent's context focused on:

- the user's objective;
- important constraints;
- major decisions;
- unresolved questions;
- final synthesis.

Move large-scale exploration, evidence collection, intermediate analysis,
and detailed notes into subagents and durable files.

Treat files as persistent project memory.

Do not rely on conversation history alone to preserve important findings.


# 1. First understand the task

Before doing extensive exploration, identify:

- the user's actual objective;
- required final deliverable;
- hard constraints;
- important assumptions;
- sources/repositories/documents that may need investigation;
- whether implementation is requested or analysis only;
- whether current information must be verified externally.

Do not start modifying code merely because the task involves a repository.

Respect explicit user instructions such as:

- analyze first;
- do not modify code;
- produce a report;
- use specific repositories;
- verify against real implementation;
- save results to a particular file.


# 2. Decide whether orchestration is necessary

Do not use subagents mechanically.

Use the normal single-agent workflow when the task is sufficiently bounded.

Prefer orchestration when one or more of these are true:

- multiple repositories or large codebases must be investigated;
- many documents, papers, logs, traces, or web sources must be read;
- several independent technical questions must be answered;
- findings from early research must be preserved for later comparison;
- substantial source-code tracing is required;
- different implementations must be compared systematically;
- the investigation itself is likely to consume a large portion of the context;
- the task naturally contains exploration -> comparison -> synthesis stages;
- the task is likely to suffer significant information loss after compaction.

The decision should be based on task structure, not domain.

A large AI inference investigation, compiler investigation, literature review,
security audit, architecture comparison, performance analysis, or unrelated
research task may all qualify.


# 3. Create durable task memory

For an orchestrated task, create a working directory inside the current
workspace when appropriate.

Recommended layout:

.agent-work/
  <task-name>/
    INDEX.md
    WORKPLAN.md
    research/
    analysis/
    verification/

If the repository already defines another scratch/research location, prefer it.

Do not overwrite user files.

Do not commit these working files unless the user explicitly asks.

If creating files inside a repository would be inappropriate, choose another
safe writable workspace location and record it clearly.


## WORKPLAN.md

Record:

- objective;
- final deliverable;
- constraints;
- decomposition;
- dependencies between workstreams;
- current phase;
- completed work;
- remaining work;
- unresolved questions.

Update it after major phases.


## INDEX.md

INDEX.md is the main agent's compact memory.

Keep it concise.

Record:

- workstream name;
- status;
- result file;
- 3-10 important findings;
- important evidence locations;
- unresolved questions;
- conflicts requiring verification.

Do NOT turn INDEX.md into the full report.

It is an index into deeper information.


# 4. Decompose by questions, not by token count

Split the task into coherent, mostly independent research questions.

Good decomposition examples:

- current implementation investigation;
- reference implementation investigation;
- model/data-format investigation;
- runtime execution-path tracing;
- benchmark/profiling investigation;
- documentation/paper investigation;
- compatibility investigation.

Bad decomposition:

- "read first 30 files";
- "read next 30 files";
- arbitrarily divide a repository into equal pieces;
- create many agents only to increase parallelism.

Each workstream should have a clear question that can produce a useful artifact.


# 5. Use subagents for bounded exploration

Use subagents when research streams can progress independently.

The main agent should provide each subagent with:

1. the exact research question;
2. relevant user constraints;
3. source/repository scope;
4. what must be verified;
5. what should NOT be done;
6. output file path;
7. evidence requirements;
8. expected completion criteria.

Subagents should usually be read-heavy during investigation.

Avoid allowing several agents to modify overlapping production files
simultaneously unless the task explicitly requires parallel implementation
and the repository/worktree setup makes it safe.


# 6. Subagent output protocol

A research subagent MUST preserve its detailed findings in a Markdown file.

The file should contain, when applicable:

## Scope

What was investigated.

## Findings

Detailed findings.

## Evidence

Prefer concrete anchors:

- repository;
- branch / commit when relevant;
- file path;
- class;
- function;
- symbol;
- configuration key;
- command;
- relevant source location;
- document/page/section;
- URL for external research.

## Execution / call path

When relevant, explain the real path through the implementation rather than
only listing files.

## Confirmed facts

Facts directly supported by evidence.

## Inferences

Reasonable interpretations that are not directly proven.

## Unknowns

Questions that remain unresolved.

## Implications

What these findings may mean for later analysis.

Do not silently turn assumptions into facts.


# 7. Keep subagent returns small

The detailed report belongs in the file.

When returning control to the parent agent, the subagent should provide only:

- work completed;
- result file path;
- approximately 5-15 important findings;
- unresolved issues;
- anything that blocks downstream work.

Do NOT paste the entire research report into the parent conversation.

Do NOT return large source-code excerpts unless essential.

The parent agent should use file paths as references to durable memory.


# 8. Avoid repeatedly loading everything into the main context

The parent agent should normally:

1. read INDEX.md;
2. determine which findings matter for the current reasoning step;
3. open only the relevant report or section;
4. inspect original evidence when a high-impact claim needs verification.

Do not routinely load every research report in full into the main thread.

Use progressive retrieval:

INDEX
  -> research report
      -> original source

This is the preferred memory hierarchy.


# 9. Separate exploration from synthesis

Do not make the same overloaded research agent responsible for discovering
everything and producing the final strategic conclusion.

After major research streams complete, start a synthesis phase.

For complex tasks, use a fresh analysis subagent to read the relevant research
artifacts and produce a cross-source analysis.

Examples:

- implementation mapping;
- gap analysis;
- compatibility matrix;
- competing hypothesis analysis;
- prioritized optimization plan;
- architectural comparison.

Write synthesis results into `analysis/`.


# 10. Resolve contradictions explicitly

If different agents or sources disagree:

Do not let the main agent simply pick one.

Record the conflict in INDEX.md.

If the disagreement materially affects the result:

- inspect the original evidence;
- or assign a focused verification subagent.

Verification should answer the narrow disputed question.

Store the result under:

verification/

Update INDEX.md after resolution.


# 11. Use staged dependency chains

Parallelize only work that is actually independent.

Example:

          Research A
             |
Research B --+--> Gap analysis --> Strategy
             |
          Research C

A later analysis should wait for required upstream evidence.

Do not start strategic optimization analysis before basic implementation facts
are sufficiently understood if those facts materially affect the strategy.


# 12. Protect important information from compaction

Assume conversation compaction may happen.

Therefore, after each major phase, persist important state to files.

At minimum preserve:

- user objective;
- hard constraints;
- important discoveries;
- rejected hypotheses and why;
- unresolved questions;
- current plan;
- file locations;
- decisions that downstream work relies on.

Never assume that because something appeared earlier in the conversation it
will remain available later.


# 13. Context-budget behavior

Prefer externalized memory over carrying raw research in the conversation.

Avoid:

- dumping thousands of lines of source code into the parent context;
- copying whole reports between agents;
- repeatedly re-reading unchanged large files;
- preserving verbose command output without analytical value;
- making the main agent perform every search itself.

Prefer:

- exact source anchors;
- structured notes;
- selective reading;
- concise indexes;
- focused verification;
- fresh synthesis contexts.


# 14. Agent count should be dynamic

There is no fixed required number of subagents.

Use as few as necessary.

A task may require:

- no subagents;
- 2 parallel investigators;
- several research agents;
- research agents followed by one synthesis agent;
- a verifier for a disputed question.

Do not spawn agents whose responsibilities overlap heavily.

Do not recursively spawn large agent trees unless a workstream is itself too
large to investigate reliably in one context.


# 15. Nested delegation

A subagent may delegate further only when:

- its assigned workstream is independently large;
- there are clearly separable subquestions;
- doing so materially reduces context pressure.

Nested delegation is not the default.

Avoid uncontrolled agent trees.


# 16. Evidence quality over summary quantity

The purpose of research files is not simply to produce long documents.

Preserve information that enables later reasoning:

- what exists;
- where it exists;
- how it works;
- why the conclusion follows;
- what remains uncertain.

A short finding with a precise source anchor is often more valuable than a
long generic explanation.


# 17. Final synthesis

Before producing the final answer:

1. reread the original user objective and constraints;
2. read the latest INDEX.md;
3. read relevant synthesis artifacts;
4. selectively verify high-impact claims against detailed research/original sources;
5. ensure unresolved uncertainty is represented honestly.

Do not synthesize solely from memory of earlier conversations.


# 18. Final deliverable

Respect the user's requested output format and destination.

If the user asks for a Markdown report, write the complete result to that file.

If the user asks for code changes, use the research artifacts to guide the
implementation.

If the user asks only for analysis, do not modify production code.

The final conversational response should normally be concise when a full
artifact has already been written.

State:

- what was completed;
- major conclusion;
- final artifact path;
- any material unresolved issue.


# 19. Continue existing work

If this skill is invoked again for an existing task:

First inspect the existing:

- WORKPLAN.md;
- INDEX.md;
- relevant research artifacts.

Do not repeat completed research unless:

- sources changed;
- previous evidence is insufficient;
- the user explicitly requests revalidation.

Reuse durable project memory.


# 20. Core principle

The main context is for reasoning.

Subagent contexts are for bounded investigation.

Files are for durable memory.

Fresh agents are for new reasoning stages.

Original sources are the final authority.

Use this architecture to scale the task instead of trying to keep the entire
project inside one conversation context.
