---
name: codebuddy-fix-error
description: Use when a beginner shares an error message and wants it explained and fixed. Finds the cause, explains it simply with an analogy, applies a minimal fix, adds a test, runs the tests, and writes a report to codebuddy_output/error_reports/. Trigger phrases: I got an error, fix this error, why is this failing, help me debug, what does this error mean.
---

# CodeBuddy: Fix an Error

Walk the student through understanding and fixing one error, step by step.
Work only inside `booking_system_backend/` unless explicitly told otherwise.

## Step 1 — Understand the error

Read the error message the student provided. Extract:
- The **exception type** (e.g. `KeyError`, `AttributeError`, `TypeError`)
- The **file path and line number** from the traceback (if present)
- The **key phrase** that describes what went wrong

If the student did not paste a traceback, ask them to run the failing command
again with this snippet appended: `2>&1 | head -60` to capture the full output.

## Step 2 — Find the root cause

Open the file and line number from the traceback using `read_file`.
If no file/line was given, use `grep` to search `booking_system_backend/` for
the exception message or the function name mentioned in the error.

Read the surrounding 10-15 lines to understand the context.

## Step 3 — Explain the cause (beginner-friendly)

Write a short explanation in this exact structure:

**What happened (one sentence):**
Plain English, no jargon. E.g. "The code tried to look up a key that does not
exist in a dictionary."

**Analogy:**
A real-life comparison that a first-year student will instantly understand.
E.g. "Imagine asking a hotel receptionist for room 999, but the hotel only has
100 rooms. That is the same error -- the code asked for something that was not
there."

**The exact line causing the problem:**
Show the line with its file path and line number.

**Why that line fails:**
One or two sentences connecting the analogy to the actual code.

## Step 4 — Apply the minimal fix

Make the smallest possible change that fixes the error. Do not refactor, rename,
or improve anything else. Use `apply_diff` or `search_and_replace`.

Before applying, show the student a before/after diff in plain terms:
- "Before: [old line]"
- "After: [new line]"
- "Why this works: [one sentence]"

## Step 5 — Add a regression test

Add a new test function to the most relevant existing test file inside
`booking_system_backend/tests/`. Follow the naming convention already used
in that file.

The test must:
- Reproduce the exact scenario that caused the error
- Pass after the fix is applied
- Have a docstring (comment) that explains in plain English what it is testing

Use `insert_content` to add the test at the end of the test file.

## Step 6 — Run the tests

Run the backend test suite:
```bash
cd booking_system_backend && .venv/bin/python -m pytest -v --tb=short
```

If the venv does not exist, tell the student to run:
```
cd booking_system_backend
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```
Then re-run the test command.

Report: how many tests passed, how many failed, and whether the new test is green.

## Step 7 — Write the report

Create the directory `codebuddy_output/error_reports/` if it does not exist.
Write a report file named `codebuddy_output/error_reports/<YYYYMMDD_HHMMSS>_fix.md`
using the current date and time.

The report must contain:

```
# Error Fix Report

**Date:** <date>
**Error type:** <exception class>
**File fixed:** <path:line>

## What went wrong
<plain-English cause, 2-3 sentences>

## The analogy
<the analogy from Step 3>

## Fix applied
Before:
  <old line>

After:
  <new line>

## Test added
File: <test file path>
Function: <test function name>

## Test results
<pass/fail counts and confirmation the new test is green>
```

## Step 8 — Summarise for the student

Tell the student in 3-4 short sentences:
1. What caused the error (plain English)
2. What you changed and why
3. What the new test does
4. Where they can read the full report
