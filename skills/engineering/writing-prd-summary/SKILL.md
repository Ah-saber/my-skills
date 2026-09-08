---
name: writing-prd-summary
description: Reconstructs research and engineering PRD histories into a chronological evidence ledger, reviewed plan summaries, problem state, and a self-contained shared version document. Use when the user asks to summarize PRDs, plans, version iterations, experiments, solved or unresolved problems, or prepare a reviewed history for collaboration and publication.
---

# Writing PRD Summary

## Purpose

Turn scattered PRDs, plans, commits, diagnostics, experiment records, and researcher memory into two outputs:

1. A detailed chronological fragments ledger for traceability.
2. A self-contained version document for collaborators.

Use `writing-fragments` for the evidence ledger and `writing-shape` for the shared document when those skills are available. The review gates in this skill override their default write rhythm.

## Invariants

- The user owns scope, research interpretation, and version boundaries.
- Establish content before classifying work items.
- Review every key section before writing it.
- Ask only for facts, judgments, or relations that evidence cannot determine.
- Label observation, researcher judgment, and agent inference separately.
- Record unresolved problems only when they were observed or active in the plan.
- Keep work items and module labels in fragments; omit them from the shared document.
- Do not claim a transition to a later plan until the user confirms it.
- Make shared documents self-contained; omit local-only paths and internal links unless requested.

## Workflow

1. **Confirm scope.** Establish date range, included plans and versions, excluded later work, evidence sources, publication target, and whether external systems are in scope.
2. **Collect evidence.** Read PRDs, discussions, ADRs, commits, diagnostics, and existing summaries. Prefer local evidence unless the user asks for remote retrieval.
3. **Reconstruct each plan.** Establish motivation, hypothesis, method, actual attempts, experiment contract, results, interpretation, stop or transition reason, solved prior problems, and unresolved end-state problems.
4. **Run a pre-write review.** Present the reconstructed section. Ask targeted questions for missing facts. Write only after explicit approval.
5. **Maintain fragments.** Re-read the ledger before every edit. Preserve detail, caveats, evidence anchors, and approved problem state.
6. **Classify after content is stable.** Propose work items based on independent purpose, method change, and evaluable result. Add method, experiment, and writing modules only after user approval.
7. **Shape the shared version document.** Review the outline and version overview, then review each version section. Remove work-item structure, implementation noise, unsupported numbers, and local-only references.
8. **Audit problem lineage.** Each solved problem cites the earlier plan and problem it resolves. Each unresolved problem is concise, observed, and still open at the stated cutoff.
9. **Publish only on request.** Stage only approved files, inspect existing ahead commits, use an explicit commit message, obtain approval for expanded push scope, and verify local and remote revisions.

## Review gates

Do not cross these gates without user approval:

- Scope and plan grouping.
- Each plan or subplan reconstruction.
- Solved and unresolved problem entries.
- Work-item classification.
- Shared-document outline and version overview.
- Each shared-document version section.
- Publication scope when a push contains earlier commits.

## Outputs

The fragments ledger may contain chronology, work items, module labels, detailed diagnostics, evidence anchors, and internal references.

The shared document should contain scope, version overview, version narratives, cross-version solved problems, current unresolved problems, stage conclusions, and collaborator-usable version anchors.

See [REFERENCE.md](REFERENCE.md) for schemas and evidence rules. See [EXAMPLES.md](EXAMPLES.md) for an end-to-end example.

