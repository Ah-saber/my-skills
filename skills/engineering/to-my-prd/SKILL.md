---
name: to-my-prd
description: Synthesize a PRD from the current conversation context and publish it to the project issue tracker. Use when user wants to create a PRD from the current context, formalize a discussion into a spec, or when a grilled design is ready to be documented.
---

This skill takes the current conversation context and codebase understanding and produces a PRD. If the issue tracker and triage label setup have not been configured, point the user to the setup skill first.

## Process

1. Explore the repo to understand the current state of the codebase, if you haven't already. Use the project's domain glossary vocabulary from `CONTEXT.md` throughout the PRD, and respect any ADRs in `docs/adr/` in the area you're touching.

2. Extract from the conversation context all available information to fill each section of the PRD template. Where information is clear and sufficient, use it directly.

3. **Where information is missing, ambiguous, or contradictory**, ASK the user. Do not guess. Do not skip. Ask until each section has enough substance.

4. Sketch out the major modules you will need to build or modify. Actively look for opportunities to extract deep modules that can be tested in isolation. A deep module encapsulates a lot of functionality in a simple, testable interface which rarely changes.

   Check with the user that these modules match their expectations. Check with the user which modules they want tests written for.

5. Write the PRD using the template below, then save it to `.scratch/<plan-slug>/PRD.md`. Use a short kebab-case slug derived from the PRD title (e.g., `dataset-understanding`). Create the directory if it doesn't exist.

## PRD Template

```markdown
## Problem Statement

The problem that the user is facing, from the user's perspective.

## Current State

How the system currently behaves in relation to this problem.

## Proposed Solution

The solution to the problem, at a high level. Do not include implementation details, file paths, or code snippets.

## User Stories

A long, numbered list of user stories in the format:

1. As an <actor>, I want a <feature>, so that <benefit>

<user-story-example>
1. As a mobile bank customer, I want to see balance on my accounts, so that I can make better informed decisions about my spending
</user-story-example>

This list should be extremely extensive and cover all aspects of the feature.

## Integration Points

Which existing systems, modules, or services this feature interacts with. Describe the interaction surface, not the implementation.

## Acceptance Criteria

- [ ] Independently verifiable criterion 1
- [ ] Independently verifiable criterion 2

Each criterion must be testable or explicitly marked as non-testable with a reason.

## Out of Scope

Things explicitly NOT included in this PRD. Be specific — vague out of scope causes future confusion.

## Decisions

Implementation and testing decisions made during design:

- The modules that will be built/modified
- The interfaces of those modules that will be modified
- Architecture decisions (create ADR if all three conditions are met: hard to reverse, surprising without context, real tradeoff)
- Schema changes and API contracts
- Testing strategy and which modules will be tested
- What makes a good test in this context

Do NOT include specific file paths — they become outdated quickly.

Exception: if a prototype produced a snippet that encodes a decision more precisely than prose can (state machine, reducer, schema, type shape), inline it within the relevant decision and note briefly that it came from a prototype. Trim to the decision-rich parts — not a working demo, just the important bits.
```
