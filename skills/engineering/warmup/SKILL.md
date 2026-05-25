---
name: warmup
description: Scaffold per-project configuration for the iteration workflow protocol, or warm up a new session by loading existing config and showing current plan progress. Use when starting work in a project for the first time, at the start of a new session, or when the agent appears to lack context about the issue tracker or triage labels.
---

# Warmup

Two modes, detected automatically:

- **Setup mode** (first run) — ask 4 questions, generate 5 config files + `CONTEXT.md` skeleton
- **Warmup mode** (subsequent runs) — read existing config, load `CONTEXT.md` and ADRs, display current Plan progress

Both modes are idempotent. Re-running updates changed parts without overwriting user edits.

## Process

### 1. Detect mode

Check whether `docs/agents/issue-tracker.md` exists.

**If it does NOT exist** → Setup mode (section 2).

**If it DOES exist** → Warmup mode (section 3).

### 2. Setup mode (first run)

#### 2.1 Explore

Look at the current repo to understand its starting state:

- `git remote -v` and `.git/config` — is this a GitHub repo? GitLab? No remote?
- `CLAUDE.md` and `AGENTS.md` at the repo root
- `CONTEXT.md` and `CONTEXT-MAP.md` at the repo root
- `docs/adr/` and `docs/agents/` directories
- `.scratch/` — sign that local-markdown issue tracker convention is already in use
- `.gitignore` — check if one exists; if not, note it for creation
- Sibling projects — check for nearby repos (e.g. same parent directory) that may serve as `.gitignore` reference

#### 2.2 Present findings and ask (one at a time)

Summarise what's present and what's missing. Walk the user through **four decisions**, ONE at a time. Explain each term before asking.

---

**Section A — Issue tracker.**

> Where issues live for this project. Skills like to-issues and triage need to know whether to call `gh issue create`, write a markdown file, or follow some other workflow.

If a `git remote` points at GitHub, propose GitHub. If GitLab, propose GitLab. Otherwise offer:

- **GitHub** — uses the `gh` CLI
- **GitLab** — uses the `glab` CLI
- **Local markdown** — issues as files under `.scratch/<plan-name>/issues/`
- **Other** — ask the user to describe the workflow in one paragraph

---

**Section B — Triage label vocabulary.**

> When the triage skill processes an issue, it applies labels to move it through the state machine. Map canonical role names to your platform's actual label strings.

The five canonical roles:

- `needs-triage` — needs evaluation
- `needs-info` — information needed
- `ready-for-agent` — fully specified, agent-ready
- `ready-for-human` — needs human implementation
- `wontfix` — will not be actioned

Default: each role's string equals its name.

---

**Section C — Domain docs layout.**

> Skills read `CONTEXT.md` for domain language and `docs/adr/` for past decisions. They need to know where to look.

- **Single-context** — one `CONTEXT.md` + `docs/adr/` at the repo root
- **Multi-context** — `CONTEXT-MAP.md` at root pointing to per-context `CONTEXT.md` files

---

**Section D — Project type.**

> Determines the semantic interpretation of triage states and Agent Brief fields.

- **Research** — `needs-info` = needs further investigation/experiment
- **OSS** — `needs-info` = waiting for reporter to provide more information
- **Dev** — `needs-info` = needs investigation of code/logs/data

---

#### 2.3 Confirm and write

Show the user a draft of all files to be written. Let them edit before writing.

**Write the `## Agent skills` block** to `CLAUDE.md` (preferred) or `AGENTS.md`. If an existing block is present, update it in-place.

```markdown
## Agent skills

### Issue tracker

[one-line summary]. See `docs/agents/issue-tracker.md`.

### Triage labels

[one-line summary]. See `docs/agents/triage-labels.md`.

### Domain docs

[one-line summary — "single-context" or "multi-context"]. See `docs/agents/domain.md`.

### Project type

[Research | OSS | Dev]. See `docs/agents/triage-context.md`.
```

**Write the five config files:**

1. `docs/agents/issue-tracker.md` — backend declaration
2. `docs/agents/triage-labels.md` — canonical role → platform label mapping
3. `docs/agents/domain.md` — domain doc layout + consumer rules
4. `docs/agents/triage-context.md` — state semantics table + Agent Brief field semantics (based on project type selected in Section D)
5. `CONTEXT.md` — initial domain glossary skeleton (if not already present)

**Write `.gitignore`:**

If `.gitignore` does not already exist, create one. Use a sibling project's `.gitignore` as reference if available (ask the user for the path). Include at minimum:

```gitignore
# Agent / tool directories
**/.claude/
**/.agents/
**/.cursor/
**/.vscode/

# Project data (large / generated)
/data/
/results/
/logs/
/checkpoints/

# Scratch / working files
.scratch/

# Documentation (generated / internal)
docs/
```

Adapt the content to the project's language and framework (e.g., add `__pycache__/` for Python, `node_modules/` for Node).

**Create `.scratch/` directory:**

Create the `.scratch/` directory at the repo root. Do **not** pre-create subdirectories like `issues/` — subdirectories are created per-plan as `.scratch/<plan-name>/issues/` when plans are initialized.

For `triage-context.md`, use the state semantics table for the selected project type:

| Canonical Role | Meaning (this project) |
|---------------|----------------------|
| `unlabeled` | ... |
| `needs-triage` | ... |
| `needs-info` | ... |
| `ready-for-agent` | ... |
| `ready-for-human` | ... |
| `in-progress` | ... |
| `blocked` | ... |
| `in-review` | ... |
| `changes-requested` | ... |
| `wontfix` | ... |
| `done` | ... |

Plus Agent Brief field semantics:

| Field | Meaning (this project) |
|-------|----------------------|
| Context | ... |
| Contract | ... |
| Expected Behavior | ... |
| Acceptance Criteria | ... |
| Out of Scope | ... |

For `CONTEXT.md`:

```markdown
# CONTEXT.md — [Project Name] Domain Glossary

## Core Concepts

### [Concept A]
- **Definition**: ...
- **Boundaries**: what's included / what's not
- **Code mapping**: which module/package (no file paths)

## Term Mapping

| Domain Term | English | Code Naming |
|-------------|---------|-------------|
| ... | ... | ... |
```

#### 2.4 Done

Tell the user setup is complete. Mention they can edit `docs/agents/*.md` directly.

### 3. Warmup mode (subsequent runs)

When `docs/agents/issue-tracker.md` already exists:

#### 3.1 Load configuration

Read the config files:
- `docs/agents/issue-tracker.md`
- `docs/agents/triage-labels.md`
- `docs/agents/domain.md`
- `docs/agents/triage-context.md`

Announce: "Warmed up — [project type] project, issues tracked via [backend]."

#### 3.2 Load domain context

Read `CONTEXT.md` (or the relevant one if multi-context). If `docs/adr/` exists, note the count.

Summarise key domain concepts in one sentence.

#### 3.3 Show current plan status

Scan `archive/` for plan directories. For each plan:

```
Plan NNNN — [goal summary]
  Issues: N/N done  [blocked: X]  [synthesis: pending/done]
```

If no plans exist: "No active plans."

#### 3.4 Check for stale items

Flag:
- Issues in `in-progress` or `in-review` for > 2 weeks
- Plans with all issues done but no synthesis.md
- `.out-of-scope/` entries referencing deprecated concepts
