---
name: codebuddy-explain-repo
description: Use when a beginner wants to understand an unfamiliar codebase. Explores the project in parallel, then writes a plain-language onboarding guide to codebuddy_output/ONBOARDING_GUIDE.md. Trigger phrases: explain this repo, help me understand the code, what does this project do, onboard me, show me around the codebase.
---

# CodeBuddy: Explain the Repo

Produce a plain-language onboarding guide for a first-year student by reading the
codebase structure and its documentation in parallel, then combining the findings.

## Step 1 — Parallel exploration

Launch two subagents at the same time (do NOT wait for one before starting the other):

**Subagent A — code structure**
Explore `booking_system_backend/` only. Read:
- The top-level directory listing
- `server.py` (entry point)
- `models.py`, `schemas.py`, `db.py` (data layer)
- `services/` (business logic files)
- `tests/` directory listing

Return a structured summary with:
1. A list of every important file and one sentence on what it does.
2. The main data types/entities the app works with (e.g. User, Flight, Booking).
3. How a typical request flows through the code (entry point → service → database).

**Subagent B — documentation**
Read:
- `README.md` (root, if present)
- `AGENTS.md` (root)
- Any `.md` files inside `booking_system_backend/`

Return:
1. What the project does in one plain paragraph (no jargon).
2. The setup commands needed to run the backend locally.
3. Any warnings or gotchas mentioned for new contributors.

## Step 2 — Combine results

Using both subagent summaries, write `codebuddy_output/ONBOARDING_GUIDE.md`.
Create the `codebuddy_output/` directory if it does not exist.

The guide must contain exactly these sections, in order:

### What This Project Does
One paragraph, plain English, no jargon. Imagine explaining to a friend who has
never coded.

### Map of Important Files
A table with three columns: `File`, `What it does`, `Analogy`.
The analogy column gives a real-life comparison (e.g. "like the front desk of a
hotel -- it receives requests and sends them to the right department").
Cover every file Subagent A identified as important.

### Glossary
A two-column table: `Term` | `Plain English meaning`.
Include every technical term that appears in the guide or the code comments
(e.g. endpoint, schema, model, ORM, migration, dependency, route).
Keep definitions to one sentence each.

### Setup Instructions
Numbered steps to get the backend running locally. Copy the exact commands from
Subagent B's findings. Before each command, add a plain-English sentence saying
what that command does and why.

### Your First Beginner Task
One concrete task that:
- Touches a single, small file
- Can be completed in under 30 minutes
- Teaches one real concept (e.g. adding a field to a schema)
Include: what to do, which file to open, what to change, and how to check it worked.

## Step 3 — Report back

Tell the user:
- Where the guide was saved (`codebuddy_output/ONBOARDING_GUIDE.md`)
- How many files and sections it contains
- What their suggested first task is (one sentence)


## Beginner-first output (IMPORTANT: overrides the output format above)

Beginners get overwhelmed by long guides. Always produce TWO files:

1. codebuddy_output/START_HERE.md - the only file a beginner reads first.
   Hard limits: under 400 words, no tables, no code, no file paths except in section 3.
   Sections:
   1. The big picture - one real-life analogy for the whole project (3-4 sentences).
   2. What happens when someone uses it - a 5-step story of one real action, no technical words.
   3. Look at only these 3 files first - one sentence each on why.
   4. 5 words you'll hear - each explained in one short sentence with an analogy.
   5. Your next step - one tiny thing to do, then point to ONBOARDING_GUIDE.md for more.

2. codebuddy_output/ONBOARDING_GUIDE.md - the detailed reference, starting with the line:
   "New here? Read START_HERE.md first."

Tone: a friendly senior student. Short sentences. If in doubt, leave it out.