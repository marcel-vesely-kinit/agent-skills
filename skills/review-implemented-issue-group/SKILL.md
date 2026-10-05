---
name: review-implemented-issue-group
description: Use after a GitHub issue group is implemented when the current branch must be checked against its original parent spec for verified gaps.
---

# Review Implemented Issue Group

Compare the original parent spec issue with the implementation at the current branch HEAD. This is a read-only audit followed, only when confirmed gaps exist, by a handoff to new spec and ticket issues.

Prerequisite: Matt Pocock's `to-spec` and `to-tickets` skills must both be installed and available before this skill starts; if either is missing, fail the entire skill immediately without starting the audit, publishing issues, or using a substitute workflow.

This workflow runs without a human in the loop: do not ask questions or wait for approval or feedback at any stage; resolve decisions from the original spec, confirmed evidence, and configured repository conventions, and stop with a report if a critical ambiguity cannot be resolved safely.

## Input

Provide only the original parent spec GitHub issue URL or `owner/repo#number`. No separate spec-file path or implementation-ticket list is required: use the issue body and its linked spec documents as the source, and discover the implementation tickets from its native child-issue relationships and explicit ticket references. If those references do not identify the complete implementation wave unambiguously, stop and report the ambiguity rather than guessing or omitting work.

## Preconditions and run artifacts

Confirm every discovered implementation ticket (children of the original parent spec Github issue) is complete, its acceptance criteria are met, and its changes are present in HEAD. If any work is still open, missing from HEAD, or incomplete, stop and report that condition.

Create a fresh local run directory under `.cache/gap-analysis/<parent-id>/<UTC-timestamp>-<random-suffix>/` (for example, `issue-43/20260923T101530Z-a81f39c2`). Use exclusive directory creation and regenerate the suffix on collision. Save `gap-report.md` and `gap-state.json` there. Keep all drafts in this directory; do not commit the cache artifacts.

## Phase 1: read-only analysis

1. Record the parent issue, discovered in-scope implementation issues, branch, HEAD SHA, run directory, and verification state. Read the original parent issue and comments, every discovered ticket's acceptance criteria, and relevant glossary, ADRs, design documents, code, and tests.
2. Treat the original parent spec and current HEAD as the comparison pair. Earlier gap reports or derived specs may provide history, but never replace the original spec as ground truth and are not a new audit checklist.
3. Run relevant deterministic tests, linting, type checks, and configured safe smoke checks. Do not run real-tool checks unless the project explicitly configures them for a safe throwaway scope.
4. Record a gap only when evidence shows a requirement is missing, incorrect, unsafe, incompatible, or materially under-tested. For each finding include the requirement, observed behavior, verification evidence, impact, and concise suggested direction. Keep preferences, settled-design disagreements, and unverified suspicions out of confirmed findings; note material uncertainty separately.
5. Do not edit product files, close issues, or create issues during this phase. If no confirmed gaps remain, write a clean report and state file with the verification evidence and stop. Do not create an empty spec or ticket set.

## Phase 2: publish confirmed follow-up work

Only when the report contains confirmed gaps, use the existing `to-spec` skill to turn the report into one derived spec issue. Derive its scope and testing seams from the report, original parent, and repository evidence; do not run its interview or wait for user approval. Link the derived issue to the original parent as its parent and ground truth. Apply the configured spec label and ensure the spec is not marked ready for implementation; if `to-spec` applies an agent-ready label by default, remove it from this spec. Then use the existing `to-tickets` skill to split the derived spec into bounded, independently verifiable vertical slices with explicit blockers. Determine granularity and blocking edges from the evidence; do not run its user quiz or wait for approval. Publish one GitHub issue per ticket in dependency order and create native blocking relationships where supported. Apply the labels required by the project's issue workflow; do not invent project-specific labels.

Use `to-spec` and `to-tickets` for their spec and ticket structures and publishing workflows, while following this skill's non-interactive policy instead of their human-feedback steps.

Do not implement or close the new tickets, and do not modify or close the original parent issue. Recheck that the report and state file are complete before publishing any issue.

## Completion report

Include the run directory and report path, parent issue and HEAD SHA, verification commands and results, confirmed gaps (or clean result), derived spec URL if created, every follow-up ticket URL and its labels/blockers, and unresolved uncertainty. A run is complete when either an evidence-backed clean report exists or the confirmed gaps have been handed off as a derived spec and dependency-ordered tickets.
