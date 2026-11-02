---
name: better-copy
description: >-
  Rewrites Magic Patterns UI copy so it is short, specific, and matches what
  the product actually does. Use when writing or editing dialog, button,
  empty-state, error, toast, or onboarding copy, or when the user says
  "better copy" or "/better-copy".
---

# better-copy

Write the shortest copy that still tells the user what will happen. One recommended version. If a file is in scope, edit the strings. If they only pasted copy, return the rewrite.

Read the component (and the mutation it calls) before writing. Copy must match the real behavior: who is affected, whether it can be undone, and what keeps working.

## Voice

The model is the newer OS dialogs, not the older dashboard ones.

```tsx
// DsRemoveFromOsDialog
<AlertDialog.Title>Remove "{name}" from this project?</AlertDialog.Title>
<AlertDialog.Description>You can always re-add it later.</AlertDialog.Description>
// Cancel · Remove

// DsDeleteDialog
<AlertDialog.Title>Delete "{name}" for everyone?</AlertDialog.Title>
<AlertDialog.Description>
  This design system will no longer be available to anyone creating new
  designs. Existing designs using it won't be affected.
</AlertDialog.Description>
// Cancel · Delete for everyone
```

Avoid the older shape: title case (`Remove Design System`), "Are you sure?", and a body that repeats the title (`This action is permanent and cannot be undone.`).

## Rules

1. **Title names the decision.** A question with the object in it. Sentence case. "Delete "{name}" for everyone?" The title is not "Are you sure?" and not a noun phrase.
2. **Body adds the one thing the title doesn't say.** The consequence: scope, reversibility, or what keeps working. One or two sentences. If the title already says it, drop the body.
3. **The button is the verb.** "Remove", "Delete for everyone". The confirm button matches the title's verb. The other button is "Cancel". Never "Yes", "OK", "Continue", or "Proceed".
4. **Say what actually happens.** Name who loses access, what is deleted, and what survives. Don't say instant, automatic, or reversible unless the code does that.
5. **No filler.** Cut "please", "successfully", "just", "simply", "are you sure", "this action", and marketing words (unlock, seamless, supercharge). No exclamation marks.
6. **Same word for the same thing.** If the product says "project", don't say "workspace" in the same dialog. Match the surrounding UI.
7. **Errors say what failed and the next step.** "Couldn't remove that design system." Don't paste the exception, and don't write "Something went wrong. Please try again later."
8. **Each element says something new.** Title, body, and button don't paraphrase each other.

Routine actions get less copy. Destructive or shared actions get the consequence. A little warmth is fine on an empty state. Confirmations stay flat.

## Also applies to

- **Empty state:** what's missing, then the next step. "No teammates yet. Invite someone to this project."
- **Toast:** the outcome. "Removed "{name}"." On failure, what failed. No "successfully".
- **Button elsewhere:** start with the verb. "Add source", "Try again".

## Checklist

- First read is enough.
- Every sentence adds something.
- It matches the code, including anything irreversible.
- Terms match the rest of the screen.
