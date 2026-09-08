# Writing PRD Summary Examples

## Reconstruct before classifying

Available evidence:

- A PRD introduces an explicit target-time feature.
- A training note says performance improved early and collapsed later.
- A diagnostic shows a repeated lattice artifact.
- A commit replaces the motion mapper.

Ask:

1. Was the mapper replacement intended to fix the artifact or test another hypothesis?
2. Did the version outperform its baseline before collapse, and was the comparison valid?
3. Why was the experiment stopped?
4. Did the replacement remove the artifact?

Do not classify work items until the answers establish the real sequence.

## Approved fragments entry

```markdown
**Plan: Explicit target-time feature.** The previous version relied on a time query and showed endpoint bias. V2 predicted endpoint-to-target flow, transported both endpoint encodings to a target grid, and decoded a target-time carrier `F_t`.

**Work item 1: V2 target feature design.** Time: July 31 to August 1. Module: Method. The design introduced a shared motion mapper, joint normalized forward transport, and a target-feature decoder.

**Work item 2: V2 training and collapse analysis.** Time: August 1 to August 2. Module: Experiment. Early results were slightly better than V1. Continued training produced a regular lattice artifact and loss of spatial and temporal variation. The experiment stopped because the artifact could not be understood or fixed in the available time.

**Unresolved problems.**

1. The first interface at which the lattice artifact forms remains unknown.
```

Write this only after the user approves the motivation, result, stop reason, and problem statement.

## Shape the shared version section

```markdown
## V2: Explicit target-time feature

V1 relied on a time-conditioned query and showed persistent endpoint bias. V2 therefore predicted endpoint-to-target motion, transported both endpoint encodings to a common target grid, and decoded a target-time feature `F_t` before Gaussian reconstruction.

Early training was slightly better than V1. Continued training produced a regular lattice artifact together with reduced spatial detail and target-time variation. The experiment ended because the failure could not be understood or corrected within the available time. The first interface at which the artifact formed remained unknown.
```

The shared version removes work-item labels, module tags, internal source paths, and low-value implementation detail.

## Claim strength

Weak:

> V2 is better than V1.

Supported:

> Before collapse, the researcher judged V2 slightly better than V1. The versions used different later training states, so this does not establish a stable final ranking.

Weak:

> The auxiliary loss conflicts with reconstruction.

Supported:

> At the measured checkpoint, the weighted auxiliary gradient was much larger and nearly orthogonal to the reconstruction gradient. This snapshot does not establish persistent training-wide conflict.

## Problem lineage

```markdown
### Solved

1. V3 resolved V2's repeated lattice and whole-output collapse; subsequent versions did not reproduce those failure modes.

### Still open

1. Stable training did not produce effective target motion; the student flow remained near zero in high-motion diagnostics.
```

## Publication boundary

If the branch is already four commits ahead and the document adds a fifth, report the five-commit push scope first. After approval, stage only the document, use a message such as `Add reviewed version history document`, push, and verify the remote revision.
