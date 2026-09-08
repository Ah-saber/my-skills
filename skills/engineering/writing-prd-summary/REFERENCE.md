# Writing PRD Summary Reference

## Output model

Maintain two distinct artifacts. The fragments ledger is the factual source of truth and may preserve chronology, work-item classification, diagnostics, evidence limits, problem lineage, commit anchors, and internal references. The shared version document is a shaped narrative for collaborators and must be understandable without access to the agent's workspace.

## Scope contract

Confirm before reconstruction:

- Historical start and cutoff.
- Included plans and version grouping.
- Later work explicitly excluded.
- Evidence locations and retrieval boundaries.
- Whether project-management systems are included.
- Fragments and shared-document destinations.
- Whether commit and push are authorized.

Visible excluded versions may clarify chronology. Do not write them into the current record or claim them as the next direction.

## Plan reconstruction schema

Establish each plan chronologically:

1. Range, including later diagnostic backfill.
2. Motivation: the observed limitation that triggered the plan.
3. Hypothesis: what the researcher expected.
4. Method, including deliberate omissions.
5. Actual variants, ablations, reversions, and abandoned approaches.
6. Experiment contract needed to interpret results.
7. Measured results and visual observations.
8. Researcher interpretation.
9. Stop or transition reason.
10. Solved prior problems.
11. Unresolved end-state problems.

## Questioning rules

Ask to determine why a choice was deliberate, which comparison was valid, how the researcher ranked results, why an experiment stopped, which problem remained open, and whether a later version actually solved it.

Avoid inviting mechanisms that were not active during the plan. Present reconstructed evidence before asking detailed questions so the user can correct the reading.

## Evidence and claim strength

Use the narrowest claim supported by evidence.

| Evidence | Allowed claim |
|---|---|
| Same protocol, repeated comparison | Comparative result within that protocol |
| Fixed multi-window diagnostic | Limited behavior across those windows and checkpoints |
| Single fixed window | Mechanism observation for that case |
| Training monitor aggregate | Training trajectory, not general interpolation quality |
| Visual review | Researcher visual judgment |
| Different data or contracts | Qualitative relation with explicit incomparability |
| Gradient snapshot | Local gradient relation at the measured state |

Keep a number only when it supports a decision, bounds a claim, or documents a failure mode.

Distinguish direct observation, researcher judgment, and agent inference. Keep agent inference out of the factual record unless the user adopts it.

## Work-item classification

Classify after plan content is approved. A separate work item needs an independent purpose, method change, evaluable experiment, or clear transition event. Do not force one work item per version. Combine compact method-and-experiment hypotheses; split substantial independent content.

Module semantics:

- **Method:** theory, architecture, contracts, reusable technical design.
- **Experiment:** training, evaluation, diagnosis, comparison, and result.
- **Writing:** documentation or paper-writing performed during the historical plan.

One item may contain multiple modules. Later document preparation must not be backdated into historical plans.

## Problem ledger

Write one problem per entry. An unresolved problem must be an observed failure, an experiment-raised question, a researcher-confirmed concern, or an unresolved attribution or comparability limit.

A solved entry states the earlier plan and problem, solving version, observable resolution, and remaining limitation. Keep implementation incidents out unless they affect experimental validity or remain research-relevant.

## Shared-document transformation

Recommended structure:

1. Scope and evidence principles.
2. Version evolution overview.
3. One narrative section per plan or version group.
4. Cross-version solved problems.
5. Current unresolved problems.
6. Stage conclusion.
7. Collaborator-usable version anchors, if requested.

Within each version section, use motivation, method, experiment, and stage judgment headings. Remove work-item numbering, module labels, irrelevant implementation tests, unsupported causal statements, numbers that support no decision, absolute filesystem paths, scratch paths, inaccessible internal links, and unconfirmed future transitions.

## Review and write discipline

Before every fragments edit, re-read the fragments file. Before every shared-document edit, re-read the shared document. Preserve concurrent user edits.

Review in this order: umbrella scope; each subplan; plan-level problems; work-item classification; shared-document outline and overview; each version section; cross-version problem lineage; final cutoff and conclusion.

User approval applies to the reviewed block. Materially new claims require another review.

## Publication checklist

Publication requires explicit authorization.

- Inspect dirty and untracked files.
- Stage only the approved artifact.
- Check ignore rules before force-add.
- Verify the staged diff and file count.
- Use a message that names the document update.
- Inspect commits already ahead of the remote.
- Explain that pushing also pushes unpushed ancestors.
- Ask before expanding push scope.
- Push the approved branch.
- Verify local HEAD equals the remote-tracking revision.

External project-management publication is optional. Enable it only when the user names the system and confirms its mapping.

