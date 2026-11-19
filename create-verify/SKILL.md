---
name: create-verify
description: >-
  Reads a codebase and writes its /verify scaffolding: account recipes, a
  local-only seed helper, and the browser report format. Use when the user
  says "create-verify", "/create-verify", "set up verify", or "scaffold
  verify recipes". Does not open a browser or seed accounts.
disable-model-invocation: true
---

# create-verify

Build the project's `/verify` skill by reading the code. Write the recipes, the seed helper, and the report format. Stop there. Do not open a browser, sign anyone up, or run a seed.

Match `.cursor/skills/verify/` if it already exists: update it from the code. If it does not exist, create it. Do not add a second verify skill anywhere else.

## Read before writing

A recipe that patches a field you have not seen read is wrong. Do these in order.

1. **Sign-up.** Find the path that creates a user and every record it writes with that user (workspace, membership, profile). Recipes start from a real sign-up through that path. Never insert those records by hand.
2. **Dev login.** Find the bypass a local server accepts (fixed email code, skipped magic link) and the condition that turns it on. If there is no bypass, the verify skill asks the user for codes. Do not invent a bypass.
3. **States the product branches on.** Search for plan, role, credits, quota, trial, billing status, invite status, and permission checks. For each state, record the store and the field the UI or API reads. That field is what the recipe patches.
4. **Local database.** How this repo connects locally, and which client it already uses (`mongosh`, `psql`, a script). The helper refuses every host that is not local.
5. **External calls.** Pages that call a payment provider or other live API with the account's ids. A state that only works with fake provider ids is "can't be seeded."

Skip a state nothing reads. Skip a state behind a hardcoded allowlist (operator, staff). Say so in the recipes file.

## Write the seed helper

One file at `.cursor/skills/verify/seed-helpers`, in the client this repo uses. Contract:

- Refuse to run unless the connection is local (`localhost`, `127.0.0.1`, `::1`).
- `account(email)` loads the user sign-up created and the records sign-up attached. Throw if sign-up did not happen. Do not insert a user.
- One function per mutation the recipes need. Each function sets only fields a reader uses. Name the function after the state (`subscribe`, `joinWorkspace`), not after the table.
- Payment-provider ids are fake. Comment that any page which calls the provider with them will fail.
- `cleanupVerifyAccounts()` deletes only `verify-…@<domain>` users and records that belong to no one else.

Email domain: `example.com`, unless sign-up rejects it. Then use a domain the app accepts and name it in the recipes.

## Write `recipes.md`

Adapt the slots. `free` is the unpatched sign-up. Every other row names a field you saw read, and the file that reads it. A row you cannot justify does not go in. Omit team recipes when the app has no shared workspace.

````markdown
# Account recipes

Each recipe starts from a real sign-up, then patches the local database.
Sign-up already writes <the records you found>, so never insert a user by hand.

## Running a recipe

1. Sign up in the browser as `verify-<recipe>-<unix-ts>@<domain>`. Enter
   `<dev code>` (<the condition that accepts it>).
2. Apply the patch:

   ```bash
   <local command, email filled in>
   ```

3. Sign out and back in so the app reads the new state.
4. Confirm the UI shows the expected state before the test case. If it does
   not, read the code that reads the field, patch what it actually reads, and
   note the extra patch in the report.

The helper refuses to run against a non-local database.

## Single account recipes

| Recipe | State | Patch |
|---|---|---|
| `free` | <what a fresh sign-up is> | None |

## Team recipes

Sign up both accounts first. `<ADMIN>` is usually a paid account.

| Recipe | State | Patch |
|---|---|---|
| `team-member` | <role> in someone else's workspace | <patch> |

## Can't be seeded

- **<name>.** <why>. <Ask the user to sign in, or mark the case BLOCKED.>

## Cleanup

```bash
<cleanup command>
```

Run it only when the user asks. Seeded accounts are useful for re-checking a
failure.
````

## Write `SKILL.md`

Use this file as `.cursor/skills/verify/SKILL.md`. Fill the angle-bracket slots from what you read. If the repo already has a doctor or launch helper for the dev environment, step 2 of Setup uses that helper: run it, use a passing environment, launch only the way that helper documents, and stop only what this run started. Otherwise ask the user to start the app and do not start or stop their server.

Leave the per-case rules and the report template as written.

````markdown
---
name: verify
description: >-
  Verifies features end to end in a real browser: opens the app, creates test
  accounts, signs in, performs actions, and writes a markdown report of each
  test case with evidence. Use only when the user says "verify", "/verify", or
  passes a list of test cases to check.
disable-model-invocation: true
---

# verify

Drive the app in the browser to check each test case, then write a report. The user asked for browser verification, so this overrides any "no browser testing" rule.

## Setup

1. Target: use the URL the user gives. Default `<local url>`. Never create accounts or change data on production unless the user names prod.
2. If the app is not running, ask the user to start it (`<dev command>`). Do not start or stop their server.
3. If the user passed no test cases, derive them from the request or the current diff and list them before starting.
4. On a non-local target, ask the user for logins and for any email codes. Never write secrets into the report or the repo.

## Seed accounts

Local only. Before running the cases, decide which account state each case needs and pick a recipe from [recipes.md](recipes.md). Cases that need the same state share one account.

For each recipe: sign up in the browser as `verify-<recipe>-<unix-ts>@<domain>` with `<how to pass dev login>`, apply the recipe's patch, sign out and back in, then confirm the UI shows that state. If no recipe fits, write the patch the same way, and read the code that reads each field before patching it.

## Per test case

1. Restate the case as: preconditions, steps, expected result.
2. Do it in the browser. Snapshot before each interaction. Snapshot again to observe the result.
3. Screenshot the result to `~/Documents/verify-reports/<YYYY-MM-DD>-<slug>/<NN>-<step>.png` (absolute path). One after each key step and always one of the final state, including FAIL and BLOCKED. If the tool saves somewhere else, use the path it returns.
4. Judge:
   - ✅ **PASS** only if what you observed matches the expected result.
   - 🔴 **FAIL** if it differs. Quote what you saw instead.
   - 🟡 **BLOCKED** if you could not reach the state (login wall, missing data, captcha). Say what blocked it.
5. After 4 failed attempts at the same step with no new evidence, mark BLOCKED and move on.
6. When every case is done, release the browser.

## Report

Write `~/Documents/verify-reports/<YYYY-MM-DD>-<slug>.md` (create the folder if needed), then reply with the summary table and the file path.

Evidence: screenshots go in the report itself as inline images, never as links or a list of paths. Put each one directly under the step it proves, using a path relative to the report (`<YYYY-MM-DD>-<slug>/<NN>-<step>.png`) so the report renders wherever it's opened. Add the exact text or state from the snapshot next to the final one. In your reply, embed the final screenshot of each case with its absolute path. A case with no screenshot is not done.

```markdown
# Verify: <feature>

Target: <url> · Date: <date>

| # | Test case | Account | Result |
|---|-----------|---------|--------|
| 1 | ... | `free` | ✅ PASS |
| 2 | ... | `<recipe>` | 🔴 FAIL |
| 3 | ... | `<recipe>` | 🟡 BLOCKED |

## Accounts

| Recipe | Email | Patches applied |
|---|---|---|
| `free` | verify-free-<unix-ts>@<domain> | None |

## 1. <test case>

**Account:** `free` (verify-free-<unix-ts>@<domain>)

**Expected:** ...

**Steps taken:**
1. Went to <signup path>

   ![Signup page](<YYYY-MM-DD>-<slug>/01-signup.png)

2. Typed ... 
3. Clicked ...

   ![After the action](<YYYY-MM-DD>-<slug>/02-action.png)

**Observed:** <exact text or state seen>

![Final state](<YYYY-MM-DD>-<slug>/03-final.png)

<quoted snapshot text>

**Result:** ✅ PASS
```
````

## Finish

Reply with these four things and nothing else:

1. The files written.
2. Each recipe as `name` — the field it patches — the file that reads that field.
3. The states you refused to seed, and why.
4. The local connection command, so the user can confirm the database. Not a browser run.

Do not claim a recipe works. You only set up the scaffolding. Someone still has to sign up and confirm the UI.
