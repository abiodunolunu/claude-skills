---
name: spec-to-todo
description: Turn a spec written by the page-spec skill (e.g. ORDERS_PAGE.md) into a build checklist (ORDERS_TODO.md) for the current project — findings and decisions first, then every requirement in build order, with what's already built ticked. The checklist is the brief for the agent that designs and builds it. Use when the user says "make the todo for this spec", "do the todo for" a page, or sends or opens a *_PAGE.md, *_LAYOUT.md, *_COMPONENT.md or *_SCREEN.md spec and asks what to build next from it.
---

# Spec → build checklist

The spec (`<NAME>_PAGE.md`, `<NAME>_LAYOUT.md`, `<NAME>_COMPONENT.md` or `<NAME>_SCREEN.md`, written by the `page-spec` skill) says what people must **see and do**, organised by user jobs (J1, J2…), with the full detail in a lettered Reference (Ref A, Ref B…). It deliberately says nothing about layout.

The checklist says how the feature gets built in *this* project: what clashes with the app as it stands, what needs deciding, and every requirement in build order, with what already works ticked. Save it next to the spec: `<NAME>_TODO.md` for a feature or website page, and `<NAME>_LAYOUT_TODO.md`, `<NAME>_COMPONENT_TODO.md` or `<NAME>_SCREEN_TODO.md` for the others.

**The checklist is the design agent's brief.** The agent that designs and builds the feature works from this file, and copies whatever shape it's given, just as it would from the spec. So the checklist doesn't decide layout either. It says what must be seen and done, the build order, and which project rules apply. The design agent decides the shape in 0.2.

Write for the user: plain English, short sentences, no jargon. Expect them to decide one thing at a time.

## How to read the spec

- **Jobs at a glance** says what matters most. The primary jobs' answers must be reachable without scrolling; rare jobs can be further away.
- **Each job section** gives the answer the user needs first, then what supports it. That decides emphasis, not placement.
- **Unmet needs** are things users need that the old build didn't do. Each one is a decision: meet it now, later, or not at all.
- **The Reference** holds every field, rule, message and condition, by business object. Its order means nothing. Facts are marked *browsing* (needed to pick an item out of many) or *one item*.
- **Copy: fixed** is kept word for word, except a `[where the user finds X]` placeholder, which the design agent fills in once the structure exists. **Copy: current wording** can change.
- **Quirks** are behaviour that may not be intended. The user decides which to fix.

If the spec has no J numbers or Ref letters (an older spec), number the jobs and letter the Reference groups yourself, in the spec's order, and say so at the top of the checklist. If an older spec has no jobs at all (it's organised by screen areas), say so to the user and suggest re-running page-spec first: a checklist built from it will copy the old page.

**By spec type:**
- **Component:** the "who can reach it" section becomes "where it's used". Options and states are Reference items; job sections hold what users are trying to do with it.
- **Website page:** forms, calls to action and where each goes, the browser title and search description, and copy that differs by screen size all need boxes. Contradicting claims are quirks: the user decides which is true.
- **App screen:** Devices becomes Platforms and devices (iOS and Android, keyboard, device back action, offline, returning from the background), and deep links replace what's kept in the link.

## 1. Read first (don't skip)

1. The spec, all of it.
2. **The project's own rules:** agent instructions (`CLAUDE.md`, `AGENTS.md` or similar), design guidelines (`DESIGN.md` or similar), the README and contributing docs, and anything they point to. **These rules beat this skill's defaults** wherever they disagree.
3. **An existing checklist, if there is one,** only to match how boxes are worded (tone, one requirement per box, level of detail). Don't copy its sections or the page shape it describes.
4. **What already exists that this feature can build on:**
   - data fetching, types, business rules and helpers for this domain
   - mock API and seed data, if the project has them
   - any checklist for the app shell (layout) that's already done
   - shared components and helpers (see §4)
5. **The structure names list** from the research file (`.page-spec/research/`, named after the spec, for example `ORDERS_PAGE.research.md`). If it isn't available, ask the user for it. If they don't have it, list the feature's tab, group, step and sub-route names from the code yourself: names only, without reading the component bodies.

**Is the old feature in this codebase?** If the spec was written from this same codebase, the feature's current route and components are what's being replaced. Their behaviour is already in the spec. **Never tick, link or reuse them.** Mention them once, in the opening paragraph, as "to be replaced: `<route>`", without listing their parts. This keeps the design agent from following links back to the old page. Tick and link only data, types, rules, helpers, mock data and shared parts.

## 2. The file's shape

```
# <Feature> — Build Checklist
Every requirement in [<NAME>_PAGE.md](...), in build order. Tick each box only when it works on desktop and mobile (and in every theme the app has).
- `[x]` already works (where it lives is noted).
- `[ ]` … **Reuse:** part of it already exists; extend or extract it.
- `[ ]` new work.
Each box ends with its source in the spec: (J2) for job 2, (Ref D) for Reference group D.
<one paragraph: the feature's shape is decided in 0.2, not here; what already exists; what this checklist adds; "to be replaced: <route>" if the old feature is in this codebase>
---
## Findings (read first)
### Clashes with what exists       C1, C2…: data we lack, rules that differ, missing backend support
### Quirks in the spec             Q1, Q2…: one line each, with a recommendation (fix / keep)
### Unmet needs                    U1, U2…: each from the spec, with a recommendation (now / later / no)
### Decisions needed               only behaviour and content questions for the user
---
## 0. Decisions                     0.1 = which quirks to fix (confirm with the user)
                                    0.2 = the design agent designs the whole feature: its parts, which
                                          jobs each serves, its height budget (§3), shown to the user
                                          before any piece is built. A task, left open.
                                    0.3 = which unmet needs to meet now (confirm with the user)
                                    The checklist writer adds nothing else here. The design agent
                                    records its own calls as 0.4, 0.5… while building.
## 1. Data and shared foundation    types, mock data, routes, data fetching, test settings (if the project has them)
## 2. Who can reach it
## 3…n. One section per job (J1 first), in priority order.
                                    Titles are the job in the user's words, never a part of a page.
                                    Boxes hold the job's answer, what supports it, and its rules,
                                    citing Ref items rather than repeating them.
## n+1. Things the feature keeps up to date
## n+2. Devices                     desktop / phones
## n+3. Reference coverage          one box per Reference item, grouped by Ref letter, ticked when
                                    built and tested
## n+4. Final pass                  every condition on and off, every role, the project's design rules
---
## Not currently active (don't build now; keep room for them)   X.1, X.2… from the spec
## Not doing                        quirks and unmet needs the user turned down, and anything the project's rules rule out
```

**Item rules:**
- One requirement per box, numbered (`3.4`), written as what someone sees or does, in plain words, ending with its source: (J2), (Ref D), or both.
- **Boxes say what, never where or how.** The page-spec writing rules apply: no placement, no widget names (tab, panel, card, dialog, column…), no names from the structure names list, and no numbered steps copied from an old flow. If a box can't be written without a widget name, the widget is a design decision and belongs to 0.2.
- Within a job, keep the spec's priority order (the answer first, then what supports it), so the design agent knows what to emphasise.
- A Reference item that serves several jobs appears once, under Reference coverage. Job boxes cite it.
- Already exists → `[x]` plus `— where it lives` (file link). Partly → `[ ]` plus `— **Reuse:** …`.
- **Reuse notes name pieces for parts, not structure.** A confirmation step, a record row, a pricing helper: name them directly. Pieces that shape a page (tabs, a side panel, a page template) are written conditionally: "if 0.2 groups the records, reuse the shared tabs component".
- Keep fixed copy word for word; reword only for a quirk the user chose to fix, or a project rule. Current wording is free to change.
- "Not part of this feature" items from the spec are left out.

## 3. Defaults (the project's own rules win)

**Decisions and scope**
- **Placement and look belong to the design agent.** The spec fixes *what*, never *where* or *how*. Only behaviour, rules and content are asked. One question at a time; if an answer is unclear, resolve it before moving on.
- **The feature's structure comes from its jobs,** decided in 0.2. Not from the old build, not from another page, and not from this checklist.
- **Quirks are confirmed one by one** (0.1). Offer "all, except…" as a shortcut. Some may be intended.
- **Unmet needs are offered, not assumed** (0.3). Recommend which to meet now, with one line of why each.
- **Don't invent data** the backend doesn't provide (no made-up timestamps, timelines or totals). Borrow looks from other products, not features. An unmet need that would need invented data goes to "Not doing" until the backend has it.
- **No extra summary figures by default.** Totals the spec asks for are fine; more are a question for the user.

**Design**
- **The whole feature first, with a height budget (0.2), before any piece is built.** Building one job at a time, each piece can look fine alone while together they push the main content out of the first screen. So before section 1:
  - Design from Jobs at a glance, primary jobs first. Consider at least two structurally different options and say why the chosen one fits these jobs, so the result isn't the old page or the last page built.
  - List every part, which job(s) each serves, and the conditional ones too (a running promotion, the busiest test account's data, an admin's extra parts), each with its height in px.
  - Measure at two viewports: 1440×900 and 390×844, unless the project names its own. The primary jobs' answers must be visible without scrolling at both.
  - Show it to the user and record it as 0.2.

  While building, measure each piece against its budget (with the project's browser tooling, or by eye if there is none). A piece that goes over is stopped and raised, not accepted as "the cost". Nothing in the first screen shows the same records twice.
- **A change never jumps ahead of its cause.** Anything that appears or changes because of a control (a filter, a message, a field) shows up where the user's attention already is, after that control, never before it.
- **Uniformity is for parts, not page structure.** When a part does the same job as elsewhere (a record row, a status, an amount, paging, an empty state, a confirmation step), reuse the shared piece so the app stays consistent. A change to a shared piece is proposed for every page, not applied to one. Whether this feature has groups, tabs or a side panel at all is decided in 0.2.
- **Polish is part of done.** Each section ends with a polish pass on desktop and phone, in every theme. A piece that still looks like an unstyled framework default counts as not done.

**Process**
- Don't start building until the user says so. Don't commit unless the user asks.
- **Every status and case the spec names is visible in the main test account's default view** (default filters, first page), if the project has test accounts or seed data. Plan it in section 1, and check it after seeding. A case that needs another account or setting is named where testers will look for it.
- **Test ids from the start,** if the project uses them: every control, record and region a test needs gets a stable test id while it's built.
- If the project has browser tests, keep them short and focused (desktop, phone, every theme), and reviewed by eye. Restore any test settings and remove test records afterwards.

## 4. Shared pieces

Before writing, list the project's shared components and helpers (record rows, status labels, money and date formatting, paging, empty and error states, confirmation steps, file upload, export). If the project keeps such a list (in its agent instructions or design guidelines), use it. Point **Reuse:** notes at these pieces for the parts they fit. They don't decide the feature's structure.

## 5. After writing

1. **Coverage check.** Go through every section of the spec except Domain context and Not part of this feature: who can reach it, every job's answer and what supports it, shared concepts (statuses, what's kept in the link), unmet needs, things kept up to date, devices, quirks, Not currently active, and every Reference item (each table row, rule, message and output). Each must be in a box, in Findings, or in "Not doing". Add anything missing.
2. **Shape check.** Search every box, title and Reuse note for the names in the structure names list, and for the page-spec word search (its step 6.5). Rewrite anything that describes a page (areas, tabs, panels, steps) as a job need or a conditional reuse. Then read only the section titles: they must be jobs, not parts of a page.
3. If the project keeps an index of specs and checklists, add both.
4. Tell the user in a few plain lines: what already exists (ticked), the main gaps, the clashes, and the unmet needs you recommend meeting now.
5. Ask 0.1 (which quirks to fix), then 0.3 (which unmet needs now), then any behaviour decisions, **one at a time**. Then propose starting with 0.2, the whole-feature design, before section 1.
