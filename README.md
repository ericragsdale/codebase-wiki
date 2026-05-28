# Codebase Wiki

A living wiki for your codebase that your AI agent builds and keeps current. Point it at a repo and it maps what's there: what the app does, how the code fits together, decisions made and why. You ask it questions, it asks you questions.

This repo contains two files:

- **`AGENTS.md`** - The instructions. Your agent reads this file and knows what to do.
- **`README.md`** - 👋

## Quick Start

### 1. Set up your wiki directory

> IMO, there is no benefit to cloning since you only need AGENTS.md and you should work with your agent to evolve this system to fit your needs.

- Create a directory for the wiki (e.g. `codebase-wiki`) and put `AGENTS.md` inside it.

    ```bash
    mkdir codebase-wiki
    mv AGENTS.md codebase-wiki/
    ```

- Enter the wiki directory and initialize it as a git repo (optional but highly recommended)

    ```bash
    cd codebase-wiki
    git init
    ```

- Open your agentic coding tool (Claude Code, Codex, GitHub Copilot, etc.) inside the wiki directory. Most will automatically read `AGENTS.md`. If yours does not, tell it to add a reference so it always reads it at the start of a session.

    example agent prompt:

    ```agent
    Read AGENTS.md and, if applicable, add a reference to it in the project's instructions file so you always read it when starting a session in this directory.
    ```

### 2. Ingest your first repo

Tell the agent where the repo is and ask it to ingest:

```bash
Ingest the repo at ~/projects/my-app
```

This process is collaborative. The agent will explore the repo, create wiki pages, and ask you questions about things it can't determine from the code alone.

### 3. Browse the results

Open the wiki directory in a markdown viewer and review what the agent produced. If something is wrong, missing, or confusing, tell the agent.

## Choosing a Model

Use a mid-tier model (e.g. Sonnet, GPT-4o) for ingestion, re-ingestion, lint, and reconciliation. These are structured tasks with explicit instructions. Save your strongest model for open-ended queries and cross-repo analysis where synthesis matters.

## Day-to-Day Workflows

### Adding a new repo

```text
Ingest the repo at ~/projects/another-app
```

As the wiki grows the agent starts finding connections: shared patterns, common infrastructure, decisions made the same way (or differently) across repos. Each new repo enhances the overall knowledge base.

### Asking questions

```text
How does authentication work across the codebase?
Which repos use stored procedures?
What are the differences between how app-A and app-B handle deployment?
```

The agent reads the index, pulls the relevant pages, and puts together an answer. If the answer is useful, ask the agent to save it as a query page.

### Updating after code changes

When a repo changes significantly (new dependency, refactored auth, new deployment target), re-ingest it:

```text
Re-ingest ~/projects/my-app, the auth system was refactored last week.
```

The agent will update the repo page and carry those changes through to any related pattern, infrastructure, and decision pages. The log captures what changed and when.

### Running a health check

```text
Lint the wiki.
```

The agent will report orphan pages, missing cross-references, stale content, unresolved open questions, and suggest improvements. Good to do periodically, or after a batch of ingests.

### Adding context the code can't provide

Some of the most valuable wiki content doesn't live in the code: why a decision was made, what a workaround was for, which team owns what, what the pain points are. You can tell the agent directly:

```text
Add a decision page: we chose session-based auth over stateless JWT because the app needs to revoke sessions immediately when users are deactivated. This was decided in early 2024 after a security audit.
```

```text
Update the app page for Inventory Manager. The main pain point is that search is slow when filtering by category because it hits a view with no index.
```

The agent integrates this into the wiki the same way it integrates code, updating cross-references, linking to related pages, keeping everything consistent.

## Editing the Wiki Yourself

The wiki is mostly read-only from your perspective. The agent is the one who should be making changes. The files are just markdown though, and small edits are fine. The only cost is inconsistency: cross-references and the index can drift if you make structural changes directly.

**Fine:** typos, rewording a sentence, deleting something wrong.

**Tell the agent instead:** anything structural (new pages, reorganizing, renaming) or changes that should ripple across multiple pages. If you do edit directly, say "I made some manual edits, lint the wiki and reconcile" next session and the agent will clean up after you.

## Tips

**Start small.** Ingest one or two repos, browse the wiki, see what's useful and what's noise. The first few ingests set the tone. If you correct the agent on what matters and what doesn't, subsequent ingests will be better.

**File good answers back.** When you ask the wiki a question and get a useful comparison or analysis, save it as a query page. Your explorations compound.

**The system evolves.** As you use the wiki, you'll discover page types, conventions, or workflows that don't fit the initial instructions. Update `AGENTS.md`. You and your agents co-evolve it over time.

**One repo at a time.** Batch ingestion is possible, but one-at-a-time ingestion with human involvement produces much higher quality, especially at the start.

**The agent can help you refine the instructions.** After a few ingests, ask: "Based on the wiki so far, what's working well in the instructions and what should we change?" The agent has read every page and can spot structural issues you might miss.
