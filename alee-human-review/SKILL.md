---
name: alee-human-review
description: >-
  Turns a branch, pull request, commit, or local diff into an ADHD-palatable
  human review guide with a prioritized reading order, complete file map, test
  inventory, and architecture-level changes. Use when the user asks for a
  human review, change summary, review guide, files to read first, or an
  architecture summary of code changes.
---

# alee-human-review

Create a durable guide that helps a human understand the change quickly. This is a summary and reading plan, not a code-quality review. Do not edit the product code.

## Workflow

1. Establish the scope.
   - Use the diff, PR, commits, or base branch the user named.
   - Otherwise include branch changes from the merge base with the default branch, plus staged, unstaged, and untracked work.
   - State the exact comparison in the artifact. Never silently omit local changes.
2. Inspect the entire change.
   - Read every changed file's diff. Read the full file when the diff lacks enough context.
   - Trace callers, readers, persisted data, shared types, and tests when needed to explain behavior or architecture.
   - Separate authored source from generated files, snapshots, lockfiles, and formatting-only changes.
3. Build the reading order around concepts, not alphabetical paths.
   - Put the source of truth or main entry point first.
   - Then follow the runtime flow: schema/types → backend mechanism → API boundary → client/UI → tests.
   - Recommend no more than five files in the first pass. Put everything else in the complete file map.
4. Identify architecture changes explicitly.
   - New or changed schema fields, persisted metadata, migrations, and source-of-truth decisions.
   - New abstractions, shared types, service boundaries, or ownership changes.
   - New mechanisms such as queues, retries, caching, synchronization, state machines, file layouts, protocols, or lifecycle transitions.
   - Changed API contracts, permissions, data flow, compatibility behavior, or failure handling.
   - State “No architecture-level changes found” when that is genuinely true.
5. Inventory tests.
   - Distinguish added, changed, deleted, and unchanged-but-relevant tests.
   - Say what behavior each added or changed test proves.
   - Name important behavior with no test coverage. Do not claim a test passed unless it was run.

## Deliverable

Create exactly one artifact:

- **In Cursor:** read and follow Cursor's `canvas` skill, then create a Canvas in the workspace canvas directory. Use `DiffView` for code changes and `useCanvasAction` with `openFile` for every file action. Link the Canvas in the response.
- **Without Canvas support:** write `human-review.md` in the repository root, unless the user named another path. Use absolute-path Markdown links for files and fenced `diff` blocks for code changes.

Do not dump the report only into chat. Chat should contain one sentence with the artifact link and a clickable link to the first file to read.

## Artifact structure

Optimize for scanning and resuming after interruption.

### Start here

Lead with:

1. A one-sentence description of what changed.
2. A button or link that opens the first file, with the relevant line range when known.
3. A concrete reading estimate, based on the size and complexity of the diff.

Show the first representative code diff immediately after this. Do not put more than two short sentences before the first diff.

### Read these files first

Recommend up to five files in numbered order. For each include:

- **Open file:** a clickable action, never a plain-text path
- **Why now:** its role in understanding the change
- **Look for:** the specific function, field, type, or transition that matters
- **Then read:** the next dependency, when useful
- **Code change:** the smallest representative changed hunk that explains why the file matters

In Canvas, render each priority file as a collapsible file card:

- The header shows the path and `DiffStats`.
- An **Open file** button dispatches `openFile` with the workspace-relative path and relevant line selection.
- The body uses `DiffView` with 6–20 lines: the changed lines plus only enough context to understand them.
- Open the first file card by default. Collapse the others.

Never substitute prose or inline backticks for the code diff. If a file has several important changes, show separate small hunks rather than one long block.

### Architecture changes with code

Group only the categories that exist:

- Data model and source of truth
- New abstractions and ownership
- Runtime mechanisms and lifecycle
- API and compatibility boundaries
- Risks or decisions worth thinking about

For each architecture change, explain:

1. Before
2. After
3. Why it matters
4. A clickable file action and a small diff that establish the change

Label inference as inference. Do not mistake a TypeScript type change for persisted schema behavior without verifying where the value is written.

### Tests

Use three short groups:

- **Added:** path → behavior proved
- **Changed:** path → behavior newly covered or expectation changed
- **Coverage gaps:** important behavior not directly tested

If tests were run, include the exact commands and results. Otherwise say “Not run; this artifact summarizes the diff.”

### Complete file map

Account for every changed file exactly once. Group files by feature or runtime flow, with one plain-language sentence per file. Collapse generated files, snapshots, and lockfiles into compact groups, but still list their paths.

Use checkboxes so the reader can track progress. Every path must also open the file:

```markdown
- [ ] [`path/to/file.ts`](/absolute/path/to/file.ts) — Owns the status transition from importing to ready.
```

### Questions to hold while reading

End with at most five high-value questions about architecture, correctness, migration, or ownership. Do not add style nitpicks.

## ADHD formatting

- Lead with action; no preamble.
- Prefer short sentences and concrete nouns.
- Keep lists to five items or split them into labeled groups.
- Make progress visible with numbered steps and checkboxes.
- Bold the decision or mechanism, not whole paragraphs.
- Avoid large prose blocks, repeated summaries, and exhaustive diff narration.
- Put details behind collapsible sections in Canvas when they would interrupt the main reading path.
- Prefer code over explanation: one short sentence, then the supporting diff.
- Use Canvas primitives for hierarchy and readable code; do not render the report as a wall of raw text.

## Accuracy rules

- Every changed file must appear in the complete file map.
- Every displayed file path must be clickable and open that file.
- Every priority file must include at least one representative code diff.
- Every important claim must point to one or more files.
- Distinguish facts from inferred intent.
- Distinguish added tests from modified tests.
- Do not call generated output an architecture change.
- Do not perform a bug review or propose fixes unless the user asks; surface risks as questions.
