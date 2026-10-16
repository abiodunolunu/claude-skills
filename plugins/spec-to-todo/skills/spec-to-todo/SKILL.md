---
name: spec-to-todo
description: Turn a behaviour spec (for example REPAYMENTS_PAGE.md, written by the page-spec skill) into its build checklist (REPAYMENTS_TODO.md) for the current project. Findings and decisions come first, then every requirement in build order, with what's already built ticked. Use when the user says "make the todo for this spec", "do the todo for <page>", or sends or opens a *_PAGE.md, *_LAYOUT.md, *_COMPONENT.md or *_SCREEN.md and asks what's next.
---

# Spec → build checklist

The spec says what people must **see and do**. The checklist (`<NAME>_TODO.md`) is how to build it in this project: what clashes with what's already there, what needs deciding, and every requirement in build order, with what already works ticked.

Write for the user: plain English, short sentences, no jargon.

## 1. Read first

1. The spec, all of it.
2. The project's `CLAUDE.md` and any design docs, if they exist. Follow any rules they set.
3. Other `*_TODO.md` files, if any. If there's one, copy its structure, tone and level of detail.
4. What's already built for this page, so nothing is planned twice: the route or screen, its components, the flows it opens, its data fetching and types, and the API it calls.
5. Shared components and helpers that the new work can reuse.

## 2. The file's shape

If the project already has a checklist to copy, use its shape. Otherwise:

```
# <Name> — Build Checklist

Every requirement in [<NAME>_PAGE.md](...), in build order. Tick each box only when it works on desktop and mobile.

- `[x]` already works (where it lives is noted).
- `[ ]` … **Reuse:** part of it already exists; extend it.
- `[ ]` new work.

---

## Findings (read first)

### Clashes with what's built
C1, C2…: data we don't have, flows that behave differently today, placeholders

### Quirks in the spec
The old build's bugs, which we fix instead of copying (one line each)

### Decisions needed
Questions about behaviour, rules or content, for the user

---

## 0. Decisions
0.1 = "fix the quirks" (confirm with the user)

## 1. Data and shared foundation

## 2. Who can reach the page

## 3…n. One section per part of the spec, in the spec's order

## n+1. Things the page keeps up to date

## n+2. Devices

## n+3. Final pass

---

## Not currently active (do not build now)
From the spec's "Not currently active"
```

**Item rules:**
- One requirement per box, numbered (`3.4`), written as what someone sees or does.
- Already built: `[x]` plus `— where it lives` (a file link).
- Partly built: `[ ]` plus `— **Reuse:** …`.
- Keep the spec's copy. Reword it only to fix a quirk.
- Leave out the spec's "Not part of this page" items.
- Every requirement in the spec appears once. Check nothing was dropped.

## 3. Rules

- The spec says *what*, not *where* or *how*. Don't ask the user about layout or looks; only ask about behaviour, rules and content.
- Fix the spec's quirks instead of copying them (decision 0.1).
- Don't invent data the backend doesn't provide. If the spec needs it, it's a clash.
- Ask decisions one at a time.
- Don't start building until the user says so, and never commit on your own.

## 4. After writing

1. Tell the user in a few lines: what's already built, the main gaps, and the clashes.
2. Ask decision 0.1 (fix the quirks?), then any other decisions, one at a time.
3. Propose starting with section 1.
