---
name: alee-code-review
description: >-
  Grills a diff the way Alex Lee reviews code: no avoidable useEffects, no new
  schema fields when the value can be derived, explicit state enums over loose
  booleans, DRY small abstractions but WET flows, scalable architecture over
  bandaid fixes, and no backwards-compat shims for unlaunched code. Use when the
  user says "grill code", "grill system design", "alee review", "check for
  bandaids", or asks to review/audit/clean up a diff, branch, or PR.
---

# alee-code-review

Review the change like a skeptical senior engineer who assumes the AI took a shortcut. Find the bandaid and apply the real fix in the same pass.

## Workflow

1. Get the diff: `git status`, `git diff`, and `git diff main...HEAD` for branch work. Read every changed file in full, plus the callers/readers of anything changed — a field or hook is only "unused" or "derivable" once you've seen who reads it.
2. Walk the six rules below against every hunk. Fix each finding as you go — don't stop to report first and wait for approval. The only exceptions: a breaking change to something that's public, or an architecture rewrite clearly beyond the diff's scope. Leave those unfixed and flag them.
3. Re-grill your own fixes. Don't introduce a new bandaid while removing one.
4. Run only the tests colocated with what you touched and type-check the app (follow the repo's testing rules).
5. Report what you found and what you changed (format at the bottom).

## The rules

### 1. No useEffect unless it's real external sync

Allowed: event listeners, subscriptions, timers, DOM APIs, analytics — anything with a cleanup function or a genuine external system. Rule of thumb: **no cleanup, probably no useEffect.**

Flag and replace:
- Derived state computed in an effect → compute during render (or `useMemo`).
- Syncing state from props/other state → store the previous value and compare during render (`if (prev !== current) { setPrev(current); ... }`), or use functional `setState(prev => ...)`.
- Responding to a user action in an effect → do it in the event handler.
- Fetch-on-mount / refetch-on-change → the data layer (SWR/query hooks, `mutate`), not an effect.
- Effects that reset state when an id changes → `key` the component.
- `if (process.env.NODE_ENV === 'production') return` guards inside effects/hooks — flag as bad practice.

### 2. Don't add schema fields — derive from the source of truth

Every new field (Mongoose schema, Liveblocks storage, TS type persisted anywhere, metadata.json) must justify itself. Before accepting one ask:
- Can it be derived from existing data? (status from run state, render type from file extension/mimeType, permissions from the owner object, version from the version model, file list from an `ls`, git info from `.git`.)
- Is it a constant pretending to be data? Hard-code it (e.g. an onboarding example id does not belong in a schema).
- Is it client-only UI state? Keep it on the client, not in the DB.
- Does it create a second source of truth that can drift? If so, reject it and name the existing source of truth.

Prefer a `deriveX()` function at read time over persisting X. Cleanly *removing* a field that's now derivable is a win — call it out.

### 3. Explicit states over loose booleans

`isLoading`, `isPending`, `isStuck`, `isComplete`, `isPreviewLoading` stacked together allow impossible combinations. Replace with one state field:
- A string-literal union / string enum: `status: 'idle' | 'copying' | 'ready' | 'failed'`.
- A discriminated union when states carry data: `{ type: 'chat' | 'plugin'; name: ... }`.
- A small state machine when transitions matter (new → initializing → streaming → done).

Also flag: numeric/Hungarian-style constants where a string enum reads better, open-ended strings that should be a closed enum (and "unknown" defaults that should hide UI instead), and status enums duplicated across features that should share one type.

A single boolean is fine when it is genuinely binary and can't conflict with anything.

### 4. DRY small abstractions, WET flows

- **DRY**: small, stable building blocks — owner/permission objects, constants, status enums, icons, helpers, shared types. If the repo already has it (`shared` package, EditorV5, design-system import, chat pattern/editor room logic), reuse it. "Don't reinvent the wheel" — search before writing a new helper.
- **WET**: larger flows (onboarding, import pipelines, agent harness steps, multi-step UI). Don't force two flows through one generic function with flags like `ownsRetryPreamble: boolean` or `mode === 'a' ? ... : ...` branches everywhere. Duplication beats a leaky abstraction.
- Flag vague names that hide intent (`claimFirstAsk`, `isDsArtifact` → `isDesignSystemArtifact`).

### 5. Grill the architecture — no bandaids

Assume the diff took the shortcut. Look for:
- Special-casing / string manipulation / regex patching symptoms instead of fixing the cause.
- New locks, flags, retries, timeouts, or guards layered on to hide a race.
- Prompt hacks (e.g. "language lock" instructions) covering a system problem.
- Logic in one code path when the same concept lives in a sibling path (e.g. retry state that should also live in the storage document; design-system build agent vs. another agent that should mirror it).
- Fixes that work for this case but break the next feature added.

For each, state the root cause and the scalable fix, even if it's the harder change. If the right fix is out of scope, say so explicitly rather than quietly shipping the bandaid.

### 6. No backwards compat for unlaunched code

Before keeping a fallback, legacy branch, migration shim, or optional "old" field, check whether the code is actually public:
- Is it behind a feature flag or internal-only gate?
- Is it on `main` / deployed (`git log origin/main -- <path>`), or only on this branch?
- Is there real persisted user data in the old shape?

If it's not public, delete the fallback and go straight to the new shape (no vestigial fields, no "fallback to `defaultFiles`", throw on the legacy path). Breaking changes are fine — **but list every breaking change for the user up front** before or alongside making it. If it IS public, say what data/users would break and propose a migration.

## Report format

Lead with a one-line verdict, then findings ordered by severity. Each finding says what was wrong and what you changed.

Label a finding with a short name for the rule it broke. Never write "Rule" or a rule number.

- `[Effect]` — a useEffect that is not external sync
- `[Source of truth]` — a new field that should have been derived
- `[State]` — loose booleans where one enum would do
- `[Abstraction]` — a DRY helper missing, or a flow forced through one function
- `[Bandaid]` — a guard hiding the real cause
- `[Compat]` — a fallback for code that has not shipped

```markdown
**Verdict:** <fixed / fixed with open items / needs rethink> — <one sentence why>

**Breaking changes:** <list, or "none">

1. **[Bandaid] path/to/file.ts:L12-L30** — <what was wrong, in one or two sentences>
   Fixed: <what you changed, naming the source of truth / helper / enum used>

**Not fixed (needs your call):**
- **[Compat] path/to/file.ts:L40** — <issue, why it wasn't auto-fixed, proposed fix>

**Verified:** <tests run + type-check result, or what couldn't be verified>
```

Skip praise and nitpicks the rules don't cover. If a finding depends on something you couldn't verify (e.g. whether a feature is public), say so instead of guessing.
