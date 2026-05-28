# Codebase Wiki — Schema

This is the schema file for a codebase wiki maintained by LLM agents. It describes the wiki's structure, conventions, and workflows. Any agent reading this file should be able to understand and operate the wiki without prior context.

## Purpose

This wiki is a persistent, structured knowledge base about a collection of software repositories. It captures what each application does, how the code is organized, what patterns and infrastructure are shared across repos, and why things are built the way they are. The wiki compounds over time — each repo ingested enriches the cross-references, surfaces shared patterns, and builds a richer picture of the whole codebase.

The wiki is **not** auto-generated API docs. It's a curated knowledge base that captures the things documentation usually misses: architectural decisions, runtime behavior, user-facing workflows, shared patterns, infrastructure dependencies, and the connections between services.

## Directory Structure

```
codebase-wiki/
├── AGENTS.md              # This file. The schema. Read this first.
├── index.md               # Content catalog — every wiki page listed with summary
├── log.md                 # Chronological record of all operations
├── repos/                 # One page per ingested repository
│   └── {repo-name}.md
├── apps/                  # User-facing application pages (what it does, who uses it)
│   └── {app-name}.md
├── patterns/              # Recurring technical patterns observed across repos
│   └── {pattern-name}.md
├── infrastructure/        # Shared services, platforms, and dependencies
│   └── {component-name}.md
├── decisions/             # Architectural decisions and their rationale
│   └── {decision-name}.md
├── queries/               # Saved query results worth keeping
│   └── {query-name}.md
├── observations/          # Cross-repo findings and issues spotted by the agent
│   └── {observation-name}.md
├── output/                # Generated deliverables (not wiki content — see below)
│   └── {YYYY-MM-DD}-{description}.md
└── raw/                   # Immutable source snapshots (optional)
    └── {repo-name}/       # Snapshot or notes taken during ingestion
```

## Page Types

### Repo Pages (`repos/`)

One page per repository. Created during ingestion. This is the primary factual record of what's in the repo.

```markdown
---
type: repo
name: {repo-name}
source: {git remote URL or local path}
last_ingested_commit: {short hash, e.g. a1b2c3d}
ingested: {date}
last_updated: {date}
tags: []
---

# {repo-name}

## Overview
One paragraph: what this repo is, what it produces (a web app, a library, a tool, a service), and its primary purpose.

## Tech Stack
Languages, frameworks, build tools, and runtime dependencies discovered in the repo. Be specific — include versions where visible (e.g. from pom.xml, package.json, Dockerfile).

## Project Structure
Key directories and files. Not an exhaustive tree — focus on what someone needs to know to navigate the repo. Explain non-obvious layout choices.

## Key Components
The important modules, services, controllers, or classes. What each one does, how they relate to each other.

## Configuration & Environment
Config files, environment variables, secrets references, profiles (dev/staging/prod). What needs to be set up to run this locally vs. in production.

## Data
Databases, schemas, migrations, stored procedures, seed data. What data does this repo own or access?

## Build & Deploy
How to build, test, and deploy. CI/CD pipeline if visible. Target environment(s).

## Dependencies & Integrations
External services, APIs, shared libraries, other repos this one depends on or is depended upon by. Link to relevant infrastructure/ and repo/ pages.

## Issues & Observations
Things the agent noticed during ingestion that look wrong, inconsistent, outdated, or improvable. These must be supportable with evidence from the code — not speculative feature ideas. Examples: outdated dependencies, inconsistent error handling, hardcoded values that should be config, missing tests for critical paths, patterns done better in other repos. Link to relevant observation pages in `observations/` if the issue spans multiple repos.

## Open Questions
Things the agent couldn't determine from the code alone. Gaps that the human should clarify.

## Dev Interview Notes
*(Optional. Add this section when a conversation with the developer or a domain expert is planned or has just occurred.)*

Questions and issues worth raising that go beyond what code alone can answer. Group by:
- **Issues to surface** — things found in the code the developer may not know are problems (security gaps, dead code, risky patterns).
- **Architectural decisions** — why certain choices were made; answers belong in `decisions/` pages.
- **Context gaps** — operational or domain knowledge only a human can provide (compliance requirements, deploy process, environment topology).

Remove entries from this section (or convert their answers to the appropriate wiki sections) after the conversation. The section can be removed entirely once everything is resolved or documented.
```

### App Pages (`apps/`)

User-facing perspective. What the application **does** from the standpoint of someone using it. A repo page describes the code; an app page describes the product. Not every repo has an app page — libraries and tools may not need one.

```markdown
---
type: app
name: {app-name}
repo: "repos/{repo-name}.md"
last_updated: {date}
tags: []
---

# {app-name}

## What It Does
Plain-language description of the application's purpose. Who uses it and why.

## User Workflows
The main things a user does in this application. Described as workflows, not features. E.g. "A manager submits a budget request" not "Has a budget form."

## Access & Authentication
How users get access. Login flow. Roles and permissions if applicable.

## Connections to Other Apps
Does this app link to, feed data to, or depend on other applications in the codebase?

## Known Limitations & Pain Points
Things that are awkward, broken, or frequently complained about.

## Glossary
*(Optional. Add when the app page contains terms a developer couldn't be expected to know from context alone — domain-specific program names, internal org units, industry-specific process names, acronyms. If there are more than a couple of such terms, include this section. Skip it when the domain is self-evident.)*
| Term | Meaning |
|---|---|
| ... | ... |
```

### Pattern Pages (`patterns/`)

Recurring technical approaches observed across multiple repos. These emerge during ingestion — when the agent notices the same solution appearing in different places.

```markdown
---
type: pattern
name: {pattern-name}
repos: ["repos/{repo1}.md", "repos/{repo2}.md"]
last_updated: {date}
tags: []
---

# {pattern-name}

## What It Is
Describe the pattern in generic terms. What problem does it solve?

## How It's Implemented
The typical implementation as observed in the codebase. Include code snippets or config fragments where helpful. Note variations between repos.

## Where It Appears
List the repos that use this pattern, with links. Note any repos that solve the same problem differently.

## Gotchas
Non-obvious things about this pattern. Common mistakes. Things that broke in the past.
```

### Infrastructure Pages (`infrastructure/`)

Shared services, platforms, and external dependencies that multiple repos rely on.

```markdown
---
type: infrastructure
name: {component-name}
repos: ["repos/{repo1}.md", "repos/{repo2}.md"]
last_updated: {date}
tags: []
---

# {component-name}

## What It Is
The service/platform/tool and its role in the codebase.

## How Repos Use It
Which repos depend on this, and how. Link to repo pages.

## Configuration
How it's configured, where credentials live, relevant environment variables.

## Operational Notes
Anything important about running, monitoring, or troubleshooting this component.
```

### Decision Pages (`decisions/`)

Architectural decisions and their context. Why something is built a certain way. These are especially valuable because the "why" is the first thing lost over time.

```markdown
---
type: decision
name: {decision-name}
date: {date of decision, if known}
repos: ["repos/{repo1}.md"]
last_updated: {date}
tags: []
---

# {decision-name}

## Context
What situation or problem prompted this decision.

## Decision
What was decided.

## Rationale
Why this approach was chosen over alternatives.

## Consequences
What this decision means for the codebase. Trade-offs accepted.

## Status
Current | Superseded | Under review
```

### Query Pages (`queries/`)

Saved results from questions asked against the wiki. Not every query gets saved — only the ones that produce analysis worth keeping (comparisons, audits, investigations).

No fixed template. Use whatever structure fits the query result.

### Output Files (`output/`)

Point-in-time deliverables generated from wiki content on request. Examples: onboarding guides, issue summaries for a meeting, formatted checklists, status reports.

**Not wiki content.** Output files have no frontmatter, are not indexed, and are not cross-referenced. They go stale and the human decides whether to keep or delete them.

Naming convention: `{YYYY-MM-DD}-{description}.md` (e.g. `2026-04-10-leg-analysis-onboarding.md`). The date makes the freshness obvious at a glance.

When the human asks for something that is clearly a deliverable rather than a wiki page — "give me an onboarding doc", "summarize the open issues for a meeting", "create a checklist for deploying X" — write it to `output/` rather than the wiki proper.

### Observation Pages (`observations/`)

Cross-cutting findings that span multiple repos or don't belong to any single repo page. These are generated by the agent during ingestion (when it notices something across repos) or during lint passes (when it can compare the full wiki).

Every observation must be supportable with evidence from the code. "This dependency is two major versions behind in three repos" is an observation. "This app should add a search feature" is a feature suggestion — those come from the human, not the agent.

```markdown
---
type: observation
name: {observation-name}
repos: ["repos/{repo1}.md", "repos/{repo2}.md"]
severity: info | warning | issue
last_updated: {date}
status: open | acknowledged | resolved
tags: []
---

# {observation-name}

## Finding
What the agent observed, stated factually.

## Evidence
Specific files, configs, or patterns in the affected repos that support the finding. Link to repo pages.

## Impact
Why this matters. What could go wrong, what's being missed, what's inconsistent.

## Suggestion
If the agent can suggest a concrete fix or improvement based on how other repos in the wiki handle the same thing, include it here. Otherwise omit this section.

## Resolution
*(Only present when status is `resolved`.)* What changed, which commit or action fixed it, and when.
```

### Observation status definitions

- **open** — Identified. No action taken. The human should read the Finding, Impact, and Suggestion and decide what to do.
- **acknowledged** — Known and either accepted as a deliberate trade-off, or actively being addressed (e.g. a rewrite is in progress). The observation stays visible because the situation hasn't changed yet. Add a note under Suggestion explaining why it's acknowledged rather than open.
- **resolved** — Fixed. The `## Resolution` section is required and must state what changed and when. Resolved observations are kept (not deleted) as a record.

### Severity definitions

- **issue** — Something that is broken or poses a concrete risk (e.g. wrong JDK in CI, credentials committed, CSRF disabled on a write-capable app).
- **warning** — Something that could cause problems or inconsistency but isn't broken today (e.g. hardcoded values, dead code requiring action, config that could mislead).
- **info** — A meaningful difference or pattern worth tracking, but not a problem (e.g. two repos solving the same problem differently, an architectural choice that has implications).

## Operations

### Ingest a Repo

Trigger: The human points the agent at a repository (local path, git URL, or a set of files).

Workflow:

1. **Explore the repo.** Read the top-level structure first: README, build files (pom.xml, package.json, Cargo.toml, etc.), config files, directory layout. Then drill into key source directories. Don't try to read every file — focus on understanding the architecture, not memorizing the code.

2. **Draft the repo page.** Create `repos/{repo-name}.md` following the template. Fill in what you can determine from the code. Be honest about what you can't determine — use the "Open Questions" section.

3. **Draft an app page (if applicable).** If this repo produces a user-facing application, create `apps/{app-name}.md`. If you can't determine user workflows from the code alone, note that and ask the human.

4. **Identify patterns.** Check if this repo uses patterns already documented in `patterns/`. If so, add this repo to those pages. If you see a new recurring pattern (check other repo pages), create a new pattern page.

5. **Identify infrastructure.** Check if this repo depends on infrastructure already documented in `infrastructure/`. If so, update those pages. If it introduces a new shared dependency, create a new infrastructure page.

6. **Identify decisions.** If you can infer architectural decisions from the code (especially non-obvious ones), create or update decision pages. Flag these for human review — decisions are the page type most likely to need human input.

7. **Note issues and observations.** Record anything that looks wrong, inconsistent, outdated, or improvable in the repo page's "Issues & Observations" section. If the finding spans multiple repos (e.g. inconsistent approaches to the same problem), create or update an observation page in `observations/`. Only include findings you can support with evidence from the code.

   **Actionable vs. informational:** If an issue requires someone to take action (delete a file, fix a bug, update a config, clean up dead code), it gets a dedicated observation page — even if it only affects one repo. Leave a brief summary and a markdown link to the observation page in the repo page's Issues section. If the finding is purely informational context (e.g. explaining why a non-obvious choice was made), keep it inline on the repo page only.

   Also **actively compare the new repo against existing repo pages** for meaningful differences in shared infrastructure, patterns, or technology choices — even when neither approach is wrong. Examples: two apps connect to the same database via different endpoint types; one app has auth and another doesn't; two apps deploy to different hosting environments. These differences have architectural implications and belong in observation pages, not just in the post-ingestion discussion summary.

8. **Update the index.** Add entries for all new and updated pages.

9. **Update the log.** Append an entry:
   ```
   ## [{date}] ingest | {repo-name}
   Source: {path or URL}
   Pages created: {list}
   Pages updated: {list}
   Open questions: {count}
   ```

10. **Commit (if git is available).** Commit all wiki changes with a message following the format: `ingest: {repo-name}`. This creates an atomic snapshot of the ingestion that can be reviewed or rolled back.

11. **Discuss with the human.** Summarize what you found, what's interesting, what connects to existing wiki content, and what you couldn't determine. Ask about the open questions. Any cross-repo comparisons mentioned in this summary must already be backed by an observation page — if you're describing a difference in the summary, it should be in the wiki, not just in the chat.

### Query the Wiki

Trigger: The human asks a question about the codebase.

Workflow:

1. Read `index.md` to find relevant pages.
2. Read those pages.
3. Synthesize an answer with [markdown links](relative/path.md) to wiki pages as citations.
4. If the answer is substantial and worth preserving, offer to save it as a query page in `queries/`.
5. If a query page was saved, update the index and commit (if git is available): `query: {short description}`.

### Lint the Wiki

Trigger: The human asks for a health check, or the agent notices issues during other operations.

Check for:
- **Stale pages**: Repos that have been re-ingested but whose pattern/infrastructure pages weren't updated.
- **Orphan pages**: Pages with no inbound links from other pages.
- **Missing pages**: Concepts, tools, or services referenced in text but lacking their own page.
- **Broken links**: Markdown links that point to paths that don't exist.
- **Contradictions**: Pages that make conflicting claims (e.g. two repo pages describing the same dependency differently).
- **Sparse pages**: Pages with mostly empty sections.
- **Open questions**: Unresolved questions across all repo pages — consolidate and present to the human.
- **Cross-repo observations**: Compare repos for inconsistent approaches to the same problem, outdated dependencies in some repos but not others, patterns done well in one place and poorly in another. Create or update observation pages in `observations/` for anything found. Mark resolved observations as `status: resolved`.
- **Human edits**: If git is available, check `git diff` for uncommitted changes the human made directly. Reconcile these — update the index, fix cross-references, and log the changes. Then commit: `lint: reconcile + health check`.

### Close Open Questions

Trigger: The human provides answers to open questions on a repo page, or has spoken with a developer and returns with new information.

Workflow:

1. For each answer provided:
   - Update the relevant section of the repo page (Tech Stack, Data, Key Components, etc.) with the new information.
   - Remove the resolved question from `## Open Questions`.
   - If the answer reveals an architectural decision, create a `decisions/` page and link to it from the repo page.
   - If the answer resolves or changes an observation, update that observation's status and add a `## Resolution` section if resolved.
2. If a `## Dev Interview Notes` section exists, update or remove items that were addressed. Remove the section entirely once empty.
3. Update `last_updated` in the repo page frontmatter.
4. Update `index.md` if any new pages were created.
5. Append to the log: `## [{date}] reconcile | {repo-name} open questions`.
6. Commit if git is available: `update: close open questions — {repo-name}`.

### Reconcile Human Edits

Trigger: The human says they've made manual edits, or the agent detects uncommitted changes during lint.

The human is free to edit any wiki page directly. Small edits (typos, rewording, adding a sentence) are always safe. Structural changes (new pages, reorganized sections, merged content) benefit from agent reconciliation afterward.

Workflow:

1. If git is available, run `git diff` to see what changed. Otherwise, ask the human what they modified.
2. For each changed page: verify frontmatter is intact, update `last_updated`, check that markdown links still resolve.
3. Update `index.md` summaries for any changed pages.
4. Update cross-references on other pages if the edits affect them.
5. Append to the log: `## [{date}] reconcile | Human edits` with a summary of what was reconciled.
6. Commit if git is available: `reconcile: human edits`.

### Proactive Reminders

The human may not always know when to trigger a wiki operation. Look for natural moments to suggest one — don't wait to be asked.

- **When a repo is mentioned:** check its page for open questions and a Dev Interview Notes section. If either has unresolved items, surface them briefly at the start of the conversation.
- **After several ingestions:** suggest a lint pass to catch cross-repo inconsistencies and stale pages.
- **After a reingest that closes observations:** note what's resolved and whether any open questions remain.
- **When the human seems to be searching for something:** offer a query instead of asking them to read pages themselves.
- **When the human makes manual edits:** remind them to ask for a reconcile so cross-references and the index stay in sync.

Keep suggestions brief — one sentence is enough. The goal is to make the wiki feel like a maintained asset, not a static dump.

The four operations the human can request at any time:
1. **Lint** — health check on the whole wiki (stale pages, broken links, contradictions, open questions).
2. **Reingest** — update wiki pages for a repo that has changed. Best at meaningful checkpoints, not mid-rewrite.
3. **Reconcile** — clean up after manual edits (cross-references, index, commit).
4. **Query** — answer a question about the codebase using wiki content; offer to save substantial answers as query pages.

### Help Command

If the human says `help`, `what can I do`, or similar, respond with the full capability list:

- **Ingest** — add a new repo to the wiki
- **Reingest** — update after meaningful changes to an existing repo
- **Reconcile** — clean up after manual wiki edits
- **Lint** — periodic health check across the whole wiki
- **Query** — ask anything about the codebase; offer to save substantial answers as query pages
- **Close open questions** — answer what the agent couldn't determine from code alone
- **Resolve/acknowledge observations** — track fixes and accepted trade-offs over time
- **Add a decision page** — record the *why* behind a non-obvious architectural choice
- **Add a query page** — save an analysis worth keeping for future reference
- **Onboard a new developer** — query the wiki on their behalf before they touch the code
- **Pre-refactor audit** — assess blast radius before changing shared infrastructure or patterns
- **Dependency audit** — find repos that are behind on a library or still using a deprecated pattern
- **Incident support** — query config, dependencies, and integration points faster than reading code
- **Cross-repo consistency check** — surface places where the same problem is solved differently
- **Generate a deliverable** — onboarding docs, issue summaries, deployment checklists, status reports; written to `output/` with a datestamped filename

## Token Efficiency

LLM context windows are finite. The biggest waste during ingestion is reading files that carry no architectural or behavioral information. Follow these rules to keep ingestion focused.

### Always skip these directories

`node_modules/`, `target/`, `build/`, `dist/`, `out/`, `.gradle/`, `.mvn/wrapper/`, `bin/`, `obj/`, `vendor/`, `__pycache__/`, `.git/`, `.idea/`, `.vscode/`, `.settings/`, `coverage/`, `.nyc_output/`, `logs/`, `tmp/`, `temp/`

### Skip these files unless specifically investigating them

`package-lock.json`, `yarn.lock`, `pnpm-lock.yaml` (lock files — `package.json` captures what matters), `*.min.js`, `*.min.css`, `*.map`, `*.jar`, `*.war`, `*.ear`, `*.woff`, `*.woff2`, `*.ttf`, `*.eot`, image/media files (`*.png`, `*.jpg`, `*.svg`, `*.gif`, `*.mp4`), database dumps that are data-only (not schema or migrations), `swagger-ui/`, `webjars/`

### Reading priority

Read in this order. Stop when you can write the repo page — you can always drill deeper later.

**First — project identity:** `README.md`, build manifests (`pom.xml`, `build.gradle`, `package.json`, `Cargo.toml`, `go.mod`, `requirements.txt`), `Dockerfile`, `docker-compose.yml`, CI/CD config (`.github/workflows/`, `Jenkinsfile`, `azure-pipelines.yml`)

**Second — configuration and structure:** Application config (`application.yml`, `application.properties`, `angular.json`, `tsconfig.json`, `.env.example`), database migrations, resource directories

**Third — architecture:** Entry points (`main` classes, `index.ts`, `app.module.ts`), routing/controller layer, security/auth configuration, service layer (selectively), data layer (entity definitions, repository interfaces)

**Selectively — implementation detail:** Individual components, utility classes, test files, templates. Only drill in when investigating a specific area.

### Large repos

For repos with hundreds of files, aim to read 30-50 files total: manifests and config (5-10), directory tree listing (1), entry points and routing (5-10), security and auth (2-5), data layer (5-10). Write the repo page with what you know. Flag unknowns as open questions. Drill deeper only when asked.

### Re-ingestion

When re-ingesting a repo already in the wiki, don't start from scratch. Read the existing repo page first. If the repo is a git repository, use the `last_ingested_commit` from the frontmatter to see exactly what changed: `git log {last_ingested_commit}..HEAD --stat` shows which files were modified, and `git diff {last_ingested_commit}..HEAD -- {file}` shows the details. Focus on changed areas only. Update the repo page (including `last_ingested_commit`), and cascade to related pages. If git is unavailable in the source repo, compare the directory listing to what's documented.

## Version Control

The wiki is a directory of markdown files. If it's a git repository, every operation benefits.

### Commit conventions

Commit after each operation with a structured message:

- `ingest: {repo-name}` — after ingesting a repo
- `reingest: {repo-name}` — after re-ingesting a changed repo
- `query: {short description}` — after saving a query page
- `lint: health check` — after a lint pass
- `lint: reconcile + health check` — after reconciling human edits
- `reconcile: human edits` — after reconciling only (no lint)
- `update: {description}` — after any other wiki change

Each commit is an atomic snapshot. If an ingestion produces bad results, `git revert` or `git checkout` undoes it cleanly.

### Using git during operations

If git is available, agents should use it:

- **Re-ingestion**: `git diff` and `git log --since` reveal what changed in the wiki since the last ingestion, and help focus on deltas rather than re-reading everything.
- **Lint**: `git diff` (unstaged changes) reveals human edits that need reconciliation.
- **Rollback**: If the human is unhappy with an ingestion or edit, `git diff HEAD~1` shows exactly what changed and `git revert HEAD` undoes it.

### Not required

Git is recommended but not required. All workflows function without it — the log file provides a fallback timeline, and re-ingestion can work by comparing directory listings to existing repo pages. If git is unavailable, the agent should not error or refuse to operate.

## Conventions

- **Links**: Use standard markdown links to reference other wiki pages: `[display-text](relative/path/to/page.md)`. Paths are relative to the linking file's location — files one level deep (e.g. `observations/`, `repos/`) link upward with `../` (e.g. `[sb-fund-list](../repos/sb-fund-list.md)`). Root-level files (`index.md`, `log.md`) link without the `../` prefix. Frontmatter `repo` and `repos` fields use plain path strings (e.g. `"repos/sb-fund-list.md"`) — not markdown link syntax.
- **Frontmatter**: Every page has YAML frontmatter with at least `type`, `name`, and `last_updated`.
- **Tags**: Use frontmatter `tags` for cross-cutting concerns that don't fit into the page type hierarchy. Tags emerge from the codebase — don't predefine them.
- **Tone**: Factual, concise, present tense. Write for a developer who has context on the tech stack but not this specific codebase.
- **Honesty**: If something is unclear, say so. "Unknown" and "needs clarification" are better than guesses.
- **Immutability of sources**: Never modify anything in `raw/`. The wiki layer is the agent's workspace; the raw layer is the human's source of truth.
- **Agent-agnostic**: This schema does not assume a specific LLM or tool. Workflows reference generic capabilities: read files, write markdown, search text. If your agent has specific tools (MCP servers, shell access, etc.), use them — but the workflows don't require them.

## Getting Started

If this is a fresh wiki (only this file exists), the first step is:

1. The human provides a repository to ingest.
2. The agent runs the ingest workflow above.
3. The agent creates `index.md` and `log.md` during the first ingest.
4. Repeat. The wiki grows with each repo.

If the wiki already has content, start by reading `index.md` and `log.md` to understand what's already here before doing anything else.

**`repos/` vs `apps/`**: These cover the same subject from different angles. A repo page describes the *code* — tech stack, architecture, how it's built. An app page describes the *product* — what it does, who uses it, user workflows. They always go together for user-facing applications. Don't merge them or confuse their purpose.
