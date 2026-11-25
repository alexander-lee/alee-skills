---
name: what-else
description: "How to build, check, and post a What-else drop: two code-rendered stills of one simple product task. The first follows the specified design system and does that task in a wacky way. The second is a high-fidelity execution of a generic prompt, then a summary of what feels generic about the result. Use whenever you are making or posting a What-else drop."
license: MIT
compatibility: Tuned for Grok Bot; adapts to other agents that can run code, read a repo, and post to Slack. Needs Node and Playwright. Stills only, no video.
---

# What-else

A drop is two stills of one simple task the product already does.

1. **Bad UX (good UI).** The UI follows the specified design system. The way the task gets done is incredibly wacky. You invent the way.
2. **A generic prompt, rendered.** You write a generic prompt for that same task, execute it at high fidelity, then look at the result and summarize what feels generic. You are not trying to make slop. You are finding out what a generic prompt produces.

Invent both. This skill has no menu of gags, layouts, or traits. Do not reach for a pattern you already know. If a choice is here because you have seen it before, throw it out and make another.

## Notes

- **Design system.** Piece 1 only. The spec the user pasted, or a short note from the repo or the live page. Don't ask them to review it. A pasted spec wins.
- **Log.** After a drop, one line naming the task, so you don't repeat it. Read the log only for that. Do not mine it for ideas.

## The task

Pick one simple thing a person already does in the product. Then build. Don't workshop a list.

## Bad UX (good UI)

Draw it only with the design system. Type, color, spacing, radius, shadow, components, logo, voice. It looks like the product. A visual check would pass it.

The interaction is the bad part: an incredibly wacky way to do that simple thing. The procedure is yours. Don't break the UI to sell the joke. Don't do an ordinary mistake. The method is what's strange. The task stays simple.

Caption, after the image: **Bad UX (good UI)**, then the task and how this screen makes the person do it. No lesson about design systems.

## The generic prompt

Write the prompt first. Do not edit it after you build. It asks for the same simple task and nothing else. No design system. No art direction. No request to look generic, and no request to look original.

Execute that prompt at high fidelity. Polished, specific, finished, faithful to the prompt. Do not apply the design system. Do not steer the screen toward a look or away from one. Do not import piece 1's styles.

When the PNG exists, look at it and write what feels generic. Only what you can see in this image. The summary is a finding, not a build step. Do not change the screen so the summary has more to say. If little feels generic, say that.

Post the prompt with the image, then the summary under **What feels generic**.

## Build

Two scenes, one app. React + Vite, plain CSS. Piece 1's CSS uses only the design system. Piece 2's CSS is its own file and comes only from executing the prompt.

- Stage: **960×600 CSS px**. Playwright screenshot, **deviceScaleFactor 2** (1920×1200 PNG). Built bundle, not the dev server.
- Wait for `document.fonts.ready`. Piece 1 aborts unless the design-system families and weights loaded.
- No garbled text. No AI-generated imagery, no stock photos, no video.
- Dated folder. `out/on-system.png`, `out/prompt.png`, the prompt in `prompt.txt`, `CHECKPOINT.json`. Resume from the checkpoint. Don't overwrite an older drop.

## Check

Open both PNGs. Don't judge from the code.

1. Piece 1 looks like the product. The wacky part is the procedure.
2. Piece 2 is a high-fidelity execution of `prompt.txt`, and the prompt was not rewritten after the screenshot.
3. The summary refers only to what is visible. The screen was not adjusted to fit the summary.

## Posting

From the bot's own Slack app.

1. One or two sentences naming the simple task.
2. `out/on-system.png`, then the Bad UX (good UI) caption.
3. The generic prompt, then `out/prompt.png`, then what feels generic.

Don't number the posts. Confirm each image landed. Delete the stray filename message. Send the owner one line with the intro link. If posting fails, send the images and words to the owner. If nothing is worth posting, say what blocked it.

No customer names, quotes, ticket ids, or internal notes.
