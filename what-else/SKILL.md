---
name: what-else
description: "How to build, check, and post a What-else drop: two code-rendered stills of the same product job. The first is bad UX with good UI that follows the specified design system. The second is generic AI slop, posted with an analysis of what is generic about that image. Use whenever you are making or posting a What-else drop."
license: MIT
compatibility: Tuned for Grok Bot; adapts to other agents that can run code, read a repo, and post to Slack. Needs Node and Playwright. Stills only, no video.
---

# What-else

A drop is two stills of one job. Same facts. Different craft.

1. **Bad UX (good UI).** Follows the specified design system. Looks like it shipped. One UX failure.
2. **Generic AI slop.** The same job in the default generated look. The caption analyzes what is generic about this image.

People should be able to cover the logo and still tell which screen is the product.

## 1. What you keep on disk

Three notes, written at setup, updated when the product changes:

- **Design system.** The spec the user pasted, or a short note you wrote from the repo or the live page. Don't ask them to review it. Colors, type (families and weights), spacing, radii, shadow, components, logo, voice. This is the only source of visual decisions for piece 1. A pasted spec wins.
- **Jobs.** Real tasks the product already has (cancel a plan, invite someone, empty a project, change a permission). One sentence each, plus the facts a screen would show. No customer names, quotes, or ticket ids.
- **Log.** One line per drop: job, the UX failure, the slop tells used. Read it before picking the next pair. Do not reuse a failure from the last five drops. Do not reuse a majority of the same slop tells from the last drop.

## 2. The shared job

Pick one job from the jobs note. Write a facts file first. Both scenes import it. Names, prices, dates, and counts match. If a fact is on one screen and not the other, the comparison is unfair. Fix the screens.

```
job: Cancel Pro
goal: stop the renewal without losing work already shared
facts:
  plan: Pro
  price: $29/mo
  renews: April 12
  keeps: projects already shared
```

Use real product facts when you know them. Invent a specific fact rather than writing "Your plan". Never use lorem, "John Doe", or "Acme".

Write the pair brief before building (template in `examples/pair-brief.md`).

## 3. Bad UX (good UI)

The screen uses only the specified design system. Type, color, radius, shadow, buttons, and logo from that spec. Copy in the product's voice. Alignment, spacing, and contrast are correct. A visual QA would pass it.

One failure, visible on the screen without the caption. Readable in a couple of seconds. Pick one the log allows:

| Failure | What the screen shows |
|---|---|
| Confirm before the facts | A real dialog asks for the decision before the plan, the date, or what the person keeps is on screen. |
| Dangerous action is the primary | The destructive verb wears the primary button. The safe action is a quiet text button. Both are real components. |
| Asks for what it already has | The form asks for email, plan, or company the product already stores. The fields look correct. |
| Next step drops the work | Save, done, or continue leaves the thing they just made off screen. |
| Equal options, wrong default | Every choice is the same component. The one people need is not selected. |
| Mechanism, not outcome | The label names the setting ("Webhook retries") and not what will happen. |
| Extra step | A real form step exists only to collect an email or a reason. |
| Success lies | The copy says it worked. The screen still shows the old state. |
| You are nowhere | The real nav is on screen and the current place is not indicated. |
| Disabled, no reason | A real control is disabled and nothing on the screen says why. |

The failure has to be possible inside the design system. If showing it means breaking alignment, color, or type, pick a different failure. Do not stack two. Do not make the UI ugly so the point is obvious. Do not use slop words (unlock, seamless, supercharge, effortless, elevate, in seconds).

Caption, after the image:

```
**Bad UX (good UI)**
Confirm before the facts. The dialog uses the real type, buttons, and color. It asks you to cancel Pro before the plan, the April 12 renewal, or the shared projects are anywhere on screen.
```

Name the failure in the first sentence. Then say what the screen withheld or did in the wrong order. Do not say the UI looks bad. Do not explain design systems.

## 4. Generic AI slop

Same job, same facts. None of the product's font, accent, radius, shadow, logo, or components. If a teammate could mistake it for the product, rebuild it.

Build the genericity on purpose. Use at least five tells, and cover three kinds: one in the copy, one in the layout, one in color or type. Every tell has to be visible in the PNG.

- Indigo, violet, or blue gradient. Gradient text. Or a purple glow on a dark canvas with blurred orbs in the corners.
- Inter, or another neutral grotesk that is not the product font. A display serif on one word of the headline.
- Three equal cards: icon in a soft tinted circle, short title, two lines of body.
- Four identical stat tiles, plus a chart with no claim.
- Marketing verbs: unlock, elevate, seamless, supercharge, effortless, next-gen, in seconds.
- "Get started" and "Learn more" as the actions, including when the job is a specific verb like cancel or invite.
- One radius and one soft shadow on every surface.
- A centered stack. No dense region, no quiet region.
- A fake person: full name, "Product Designer" or "Founder", colored-initial avatar.
- A "Trusted by" row of gray wordmarks.
- A sidebar of every destination, all the same weight.
- Emoji in the headline or as the status.

Put the real facts on the screen and let the template bury them. That burial is a tell you can analyze. Do not copy a real company's homepage or use their logo. The target is the genre.

## 5. The analysis

Posted after the slop image. Four to six bullets. Each bullet names something in this image, then why that choice is the default. No bullet that is not visible. No essay. No "soulless", "vibes", or a lecture about AI.

```
**Generic AI slop**
Same job: cancel Pro. The $29 and the April 12 date are in the third card.

- The headline is a violet gradient on "Elevate". This is a cancel screen. The gradient is doing the job of a hero the screen doesn't have.
- Inter and indigo, radius 16 on every box. None of that is the product.
- Three equal cards, each an icon in a tinted circle plus two lines. Cancel sits at the same weight as "Learn more".
- "Get started" is the primary button. The verb for this job is cancel.
- "Sarah Chen, Product Designer" under a "Trusted by" row. She is not a customer. The row is not the product's.
```

A bad analysis recites the menu ("it uses a gradient, three cards, and Inter") without saying where they are or why they hurt this job. Delete any bullet that would still be true if you swapped the image for another slop screen.

One last sentence is allowed: the UX is also worse, and the styling is why a person notices the styling first. One sentence. The bullets stay about what is generic.

## 6. Build

Two scenes, one app. React + Vite, plain CSS. The facts file is imported by both. Piece 1's CSS uses only tokens from the design system, in one file. Piece 2 has its own CSS and does not import that file.

- Stage: **960×600 CSS px**. Screenshot with Playwright, **deviceScaleFactor 2** (1920×1200 PNG).
- Screenshot the built bundle, not the dev server.
- Wait for `document.fonts.ready`. Piece 1 aborts unless each family and weight in the design system actually loaded. Piece 2 must load its slop face and must not request the product font.
- Real copy. A garbled string in either PNG is a failure.
- No AI-generated imagery, no stock photos, no video, no animation.

Per-run folder with the date in the name. Write `facts.ts`, both scenes, `out/on-system.png`, `out/slop.png`, and `CHECKPOINT.json` (`on-system`, `slop`, `captions`, `posted`). An interrupted run resumes from the checkpoint. Do not overwrite an older drop.

## 7. Check before posting

Open both PNGs. Look at them. Do not judge from the code.

1. Piece 1 uses only specified tokens. A color, font, or radius that is not in the design system fails it. Fix or rebuild.
2. Piece 1's failure is visible without the caption, and it is the one the brief named.
3. Piece 1 would pass a visual check: alignment, contrast, type, no overflow, no slop words.
4. Piece 2 does not use the product font, accent, radius, or logo.
5. Every analysis bullet points at something you can see in `out/slop.png`. Delete the rest.
6. The facts match across the two files and both screens.

Replace a weak piece. Do not post one you would be embarrassed by. Note small leftover flaws in the log and still post only if the lesson holds.

## 8. Posting

From the bot's own Slack app. Post in this order:

1. One or two sentences. Name the job. Say that the first screen follows the design system and the second is the generic version. No manifesto.
2. `out/on-system.png`, then the Bad UX (good UI) caption.
3. `out/slop.png`, then the analysis.

Use `**Bad UX (good UI)**` and `**Generic AI slop**` so the channel can scan. Do not number the posts. Read the channel back to confirm each image landed. Delete the stray filename message an upload leaves behind. Before retrying, check that it did not already post.

Send the owner one line with the link to the intro. If posting fails, send the images and captions to the owner and say it failed. If nothing is worth posting, say what blocked it. Do not post an old drop again.

No customer names, quotes, ticket ids, or internal notes in the channel.
