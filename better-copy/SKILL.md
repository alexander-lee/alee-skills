---
name: better-copy
description: >-
  Rewrites product UI copy so it is short, specific, and matches what the
  product actually does. Use when writing or editing dialog, button,
  empty-state, error, toast, or onboarding copy, or when the user says
  "better copy" or "/better-copy".
---

# better-copy

Write the shortest copy that still tells the user what will happen. One recommended version. If a file is in scope, edit the strings. If they only pasted copy, return the rewrite.

Read the component (and the action it calls) before writing. Copy must match the real behavior: who is affected, whether it can be undone, and what keeps working. Match the words already used on nearby screens.

## Voice

```tsx
<AlertDialog.Title>Remove "{name}" from this project?</AlertDialog.Title>
<AlertDialog.Description>You can add it again later.</AlertDialog.Description>
// Cancel · Remove

<AlertDialog.Title>Delete "{name}" for everyone?</AlertDialog.Title>
<AlertDialog.Description>
  People won't be able to use it for anything new. Anything already using it
  keeps working.
</AlertDialog.Description>
// Cancel · Delete for everyone
```

Avoid title case (`Remove Project`), "Are you sure?", and a body that repeats the title (`This action is permanent and cannot be undone.`).

## Rules

1. **Title names the decision.** A question with the object in it. Sentence case. "Delete "{name}" for everyone?" The title is not "Are you sure?" and not a noun phrase.
2. **Body adds the one thing the title doesn't say.** The consequence: scope, reversibility, or what keeps working. One or two sentences. If the title already says it, drop the body.
3. **The button is the verb.** "Remove", "Delete for everyone". The confirm button matches the title's verb. The other button is "Cancel". Never "Yes", "OK", "Continue", or "Proceed".
4. **Say what actually happens.** Name who loses access, what is deleted, and what survives. Don't say instant, automatic, or reversible unless the code does that.
5. **No filler.** Cut "please", "successfully", "just", "simply", "are you sure", "this action", and marketing words (unlock, seamless, supercharge). No exclamation marks. No flavor: no jokes, warmth, or personality added on top of the fact.
6. **Same word for the same thing.** If the product says "project", don't say "workspace" in the same dialog. Match the surrounding UI.
7. **Errors say what failed and the next step.** "Couldn't remove that project." Don't paste the exception, and don't write "Something went wrong. Please try again later."
8. **Each element says something new.** Title, body, and button don't paraphrase each other.

Routine actions get less copy. Destructive or shared actions get the consequence. Confirmations stay flat.

## Also applies to

- **Empty state:** what's missing, then the next step. "No teammates yet. Invite someone to this project."
- **Toast:** the outcome. "Removed "{name}"." On failure, what failed. No "successfully".
- **Button elsewhere:** start with the verb. "Add source", "Try again".

## Checklist

- Clear on the first read, and no sentence is there for flavor.
- Every sentence adds something. Cut anything that restates the title or the button.
- Wording, tense, and tone match the copy already used for this kind of action.
