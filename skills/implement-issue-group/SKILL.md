---
name: implement-issue-group
description: Use when a parent spec and a dependency-linked set of GitHub issues need implementation.
---

# Implement Issue Group

Implement a supplied set of GitHub tickets as a sequence of independently reviewable changes. Use the parent spec for shared intent and each ticket's acceptance criteria for its concrete scope.

Prerequisite: Matt Pocock's `tdd` and `code-review` skills must both be installed and available before this skill starts; if either is missing, fail the entire skill immediately without inspecting issues or code, implementing, or using a substitute workflow.

This workflow runs without a human in the loop: do not ask questions or wait for approval, seam confirmation, or feedback; resolve decisions from the issues and repository evidence, and stop with a report if a critical ambiguity cannot be resolved safely.

## Inputs

Provide these two inputs:

- **Parent spec:** the parent GitHub issue URL or `owner/repo#number`, plus the authoritative spec file or document reference if it is separate from the issue body. If the issue body contains the spec, no separate file is needed.
- **Tickets:** the GitHub issue URLs or `owner/repo#number` identifiers for the tickets in this wave. A range is acceptable when its inclusive endpoints are unambiguous.

Treat only the supplied tickets as in scope. Use the repository context to identify the checkout; if issue references or the intended ticket set are ambiguous, stop and report the ambiguity without asking for clarification.

## Workflow

1. Read the parent spec issue, including comments, then read the full body and comments for every in-scope ticket. Review relevant project glossary, ADRs, implementation, and tests before changing code.
2. Build the live dependency picture from GitHub's native blocking graph and each ticket's `Blocked by` section. A ticket is ready only when every blocker is complete. If the sources disagree, or a native blocker is still open despite a comment saying it was completed, do not start that ticket. Report the conflict and work another independent ready ticket if one exists. Never infer that a blocker is stale or bypass it based on schedule pressure.
3. Select one ready ticket at a time, preserving the user's order among currently ready tickets. A blocked ticket does not prevent work on an independent ready ticket. If a blocker is outside the supplied set and remains open, leave its dependent ticket untouched and report the blocker.
4. Before editing a ticket, record the current commit as that ticket's review base. Use the `tdd` skill's test-first loop. Choose the highest existing public behavior seam that demonstrates the ticket's acceptance criteria; do not wait for seam approval. If no defensible seam can be selected from the issue and repository evidence, stop that ticket and report why. Complete a vertical slice for the ticket; do not defer its tests until the end of the batch.
5. Verify the ticket's acceptance criteria and run the strongest relevant project checks. Use the `code-review` skill against the recorded review base before considering the ticket complete. Resolve review findings and re-verify.
6. Commit only the completed ticket's changes. Then add an issue summary with the implementation, commit, and verification evidence, and close that ticket. The commit and successful verification must precede closure. Never close the parent spec or merge a pull request.
7. Repeat with the next ready ticket. Stop an affected ticket and report concrete conflicts with the parent spec, ticket, or ADR; do not silently change settled requirements.

## Completion report

List each target ticket as completed, blocked, or not started; include its commit and verification evidence when completed. Report unresolved dependency or requirement conflicts and any remaining repository-level risks. The wave is fully complete only when every in-scope ticket is implemented, verified, and closed.
