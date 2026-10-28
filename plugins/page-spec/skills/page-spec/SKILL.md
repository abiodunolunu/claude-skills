---
name: page-spec
description: Write a design brief for an existing web app feature, layout, website page, component or app screen, so it can be redesigned from scratch without copying the current design. Reads everything it renders (or the live URL when there's no source) and writes NAME_PAGE.md, NAME_LAYOUT.md, NAME_COMPONENT.md or NAME_SCREEN.md, organised by user jobs, never by screen areas, placement or visuals. Use when asked to spec a page, feature, layout, component, screen or URL for a redesign, to "write a spec for" one, or to do the next one after a previous page-spec run.
---

# Page, layout, component or screen spec

The output is a Markdown spec of ONE thing in an existing product. A design agent or designer uses it to design a new, better UI. It captures everything the thing does and everything inside that, and describes **what users need and do, not how the current version is built or looks**.

## Why it must not mirror the current design

The design agent sees only the spec and copies whatever shape the spec gives it. Visual words are the smallest leak. The bigger ones, and what the spec does about each:

| Leak | What goes wrong | So the spec… |
| --- | --- | --- |
| Structure | Sections follow the screen's areas in render order, so the agent rebuilds the same areas in the same order. | is organised by user jobs, drafted from the user before any code is read |
| Structure names, even with a disclaimer | Today's tabs, groups, steps, areas or sub-routes get used even when marked "not a requirement". A disclaimer doesn't cancel a ready-made answer. | never names them, in prose, copy, routes or URL values |
| Inventory | A full list of today's fields, in today's order, is enough to redraw today's screen. | keeps full detail in a Reference grouped by business object, in an order that carries no information |
| Equal weight | Every item at the same depth gets kept in the same proportions. | weights jobs by priority and keeps job sections short |
| Current solutions | "Progress across the three bands" gets drawn as three bands. | states needs, not solutions |
| Today as the baseline | "Today approvers can't filter" produces "today plus fixes". | states unmet needs as needs |

**User jobs.** A job is a reason someone uses the thing: a question they want answered or a task they want done, in their words. For example, on a commissions page:
- "How much have I earned, and when was I last paid?"
- "Is a particular client earning for me?"
- "I need records or statements for my accountant."
- "Which tier am I on, and can I do better next quarter?"

A job is never a screen area ("the summary", "the tier tab", "the breakdown table").

## What to spec, and the files

| Target | What it is | Spec file |
| --- | --- | --- |
| **Feature** | What users think of as one thing in a web app, even across several routes: for example a list and its detail (`/orders` and `/orders/:id`) and the flows they open. Spec them together; splitting by route bakes today's split into the spec. Spec a single route on its own only when it serves different jobs from its neighbours. | `<NAME>_PAGE.md` |
| **Layout** | The shell that pages render inside: navigation, identity, organisation or account switching, quick-create actions, app-wide summaries, prompts and banners, and the flows they start. | `<NAME>_LAYOUT.md` |
| **Website page** | A public page of a marketing or content site, such as a home or pricing page. Its shell (site navigation and footer) is part of the spec unless another spec covers it. Specced from source or from the live URL. | `<SITE>_<PAGE>_PAGE.md` |
| **Component** | One reusable component on its own, for example a date picker, an upload widget or a payment form: every way it can be set up and every state it can be in, across all the places that use it. | `<NAME>_COMPONENT.md` |
| **App screen** | One screen (or feature across screens) of a native or mobile app. The app's navigation shell is its layout. | `<NAME>_SCREEN.md` |

If it's unclear which one the user means (a bare domain name, a component that's also a route), check the code or the URL first, then ask.

A feature, website page or screen spec relies on the layout spec and doesn't repeat it, except for layout parts that belong to it. Repeat those so the spec stands on its own.

**Files and numbering.** The `spec-to-todo` skill relies on these, so keep them exact:
- Spec: named as in the table, in the repo root or wherever the project keeps docs.
- Research: `.page-spec/research/<spec file name minus .md>.research.md`, for example `.page-spec/research/ORDERS_PAGE.research.md`. The `.research.md` ending keeps it from being mistaken for a spec.
- Jobs are numbered **J1, J2…** in priority order.
- Reference groups are lettered **Ref A, Ref B…**.

**Agents.** This skill uses subagents for research and for one review. In Claude Code, use `Explore` agents for research (on a faster model such as `sonnet` if you can choose) and a `general-purpose` agent for the review. On other tools, use read-only research subagents run in parallel, and for the review, a fresh agent or session that sees only the spec. Without subagents: research the chunks one at a time yourself, saving each to the research file as you go, and ask the user to run the review in a fresh session.

## 0. Check for saved research

Look for this target's research file.

- **None:** start at step 1.
- **Found:** see what changed since it was saved.
  - With git: `git diff --name-only <saved commit>`. This includes uncommitted changes.
  - Without git: compare file modification times with the saved date.
  - From a live site: fetch it again and compare the extracted text and links with the saved copy.
  - Always redo steps 2.1–2.2 quickly (entry points and direct children) to spot new parts.

  Then:
  - nothing the research covered changed: skip steps 1–3 and work from the file
  - some parts changed: re-research only the chunks covering them, and update the file
  - new parts, many changed chunks, or the user asks for a fresh look: research from scratch, but keep the saved job draft as a starting point
- Tell the user which of these happened, in one line.

Use the research file for every rewrite, including fixes after the step 6 checks.

## 1. Draft the jobs before reading any code

Reports are organised by code and screen area. Jobs built by sorting them come out as today's areas with question-shaped names. So draft the jobs first, from the domain:
- what the business does, who uses this thing, and why
- different roles separately: an approver brings different questions from a payer
- what the user has told you, the product's name, its navigation labels and any product docs. Don't open the target's components or page source yet.

By target:
- **website page:** the visitor's jobs, such as "Is this for me?", "What does it cost?", "Can I trust them?", "How do I get started?"
- **component:** what users are trying to do with it in each place it's used ("pick the date a payment goes out", "attach the invoice")
- **app screen:** as for a feature, plus jobs that only exist on a phone ("deal with this notification", "do this while offline")

Write the jobs as questions or tasks in the user's words. Most working pages have a "what needs me?" job. Put the draft at the start of the research file. Step 4 revises it.

## 2. Map it

The research follows the code's structure. The spec doesn't.

First, read the project's agent instructions (`CLAUDE.md`, `AGENTS.md` or similar) for stack hints (how components are found, where design-system primitives live), the brand name to keep out of the spec, and where specs are kept. They win over this skill's defaults.

1. **Entry points:**
   - **feature:** every route in it and the root component each renders
   - **layout:** the layout file, plus which pages use it
   - **website page, from source:** the route or template file, plus the site shell it renders inside
   - **website page, live only:** the URL (see "Working from a live site")
   - **component:** the component file, plus **every place it's used** (search for its name). The places it's used decide which options and states matter.
   - **app screen:** the screen file(s), how they're registered with the navigator, and every way to reach them (other screens, deep links, notifications)
2. **Direct children** and the flows they open (dialogs, drawers, sheets, forms, global dialogs triggered by events). Also:
   - **layout:** the stores and data it loads, and the global events it listens to
   - **component:** the options the host can set, what it reports back to the host, and any content the host can slot in
   - **app screen:** device permissions it asks for (camera, location, notifications), and what it does offline or when the app comes back to the foreground
3. **Divide the code into research chunks.** This split is only for coverage and speed. It never shapes the spec.
   - Balance by **amount of code** (files and lines each chunk recurses into), not by how big an area looks on screen. The slowest chunk sets the pace.
   - Use as many chunks as the code needs. A small target (under about 500 lines, or a single component) needs no agents: read it yourself. A large feature may need eight. Split a big flow into its own chunk.
   - **Each shared component belongs to exactly one chunk.** The others name it and say who covers it.
   - **Skip the layout's components** if the layout spec exists.
   - One chunk owns **access**: route guards, redirects, middleware, deep-link handling.

   Typical chunks:
   - **feature or app screen:** browsing many items; one item and its actions; each major flow; prompts and access
   - **layout:** navigation; account and quick-create actions; app-wide summaries; banners and app-wide dialogs; the stores and events that keep it current
   - **website page:** the content; forms and calls to action and where each goes; the site shell; differences between screen sizes
   - **component:** usually one chunk. Split by mode (single vs range selection) or by the places that use it if they set it up very differently.
4. **Legacy or test versions** (old routes, `-old` files, unused variants): note them, don't spec them.
5. **Structure names note.** Every tab, group, section heading, step title and area name the target uses, plus sub-route paths, URL parameter names and values (`?tab=refunds`, `/orders/:id/refund`), and for a website the headings of its content sections. They're for the checks in step 6 and must never appear in the spec. Save it in the research file.

### Working from a live site

When there's no source, only a URL:
- Fetch the raw HTML (for example `curl -sL -A "Mozilla/5.0" <url>`) into a new, empty folder of its own. A summarising fetch paraphrases long copy, and the spec needs exact wording.
- Extract the text and links in page order with a small script kept in a different folder. Run Python with `-I`.
- Site builders (Framer, Webflow, Wix and others) often render a separate copy of the page for each screen size, hidden or shown with media queries. Map each copy to its screen-size range, and report copy that differs between them.
- Content only revealed by interaction (closed FAQ answers, menus, counters, carousels past the first slide) is often missing from the HTML. Look for it in the site's JavaScript bundles.
- If a browser tool is available, use it to check the interactive parts.
- Save the extracted text and links in the research file. Date the spec and say it was taken from the live site. Without source, there's no "Not currently active" part beyond what the bundles reveal.

## 3. Research

Launch one research agent per chunk, all at once (or read a small target yourself). Reading code and reporting facts doesn't need the strongest model; writing the spec does, so the writing stays with you. Each brief says:

- **Repo path and stack hints,** from the project's agent instructions, or worked out from the code (for example how the framework resolves auto-imported components from their tag names).
- **The purpose:** "I'm writing a PRODUCT/BEHAVIOUR spec, not a code map."
- **Its root components,** what the other chunks cover, and which shared components belong to another chunk (name them, don't read them).
- **Recursion:** recurse into everything rendered, including the dialogs, drawers and forms it opens, and global dialogs. Stop at the design system's primitives and icons. Shared components are reported once, by their owner: their user-visible behaviour (closing, Escape, selection, paging options, accepted files).
- **What to report:**
  - exact copy and labels, defaults, and conditions (status, role, feature flag)
  - validation rules and messages
  - success and error copy, and what refreshes afterwards
  - URL state: what's kept, invalid values, and whether other links or apps depend on it. For an app screen: deep links, navigation parameters, and what's restored after the app is closed.
  - loading, empty and error states
  - what data each thing needs and where it comes from, in one line
  - permissions and feature flags, and what each changes
  - outputs the user takes away (exported files, emails, receipts): format, contents and naming
  - for each fact shown: whether the user sees it when browsing many items, or only when looking at one
  - **component:** each option and what it changes, what it reports back and when, and how each place that uses it sets it up
  - **app screen:** iOS vs Android differences, the device back action, keyboard handling, offline behaviour, and device permission prompts with their copy
  - **website page:** where every call to action and link goes, every form and what happens when it's sent, the browser title and search description, and claims that contradict each other (numbers, timings, prices)
  - **what question each piece of information answers, or what task each action serves**
  - **friction:** dead ends, actions that silently disappear or fail, things the user must piece together from several places, and how many steps common tasks take
- **"Read files fully. Report facts as short bullets. Include file:line refs only for conditions, rules and anything surprising. List dead, disabled and commented-out code separately. No recommendations."**

Leave out analytics unless the user asked for them. When reports conflict, check the code yourself.

**Save the research file:** the date, the commit (if git) or the URL, the repo, the chunks and what each covered, the job draft, the structure names note, then the reports in full, plus anything you checked yourself. If `.page-spec/` isn't ignored by version control, tell the user once and suggest it. Don't change their ignore file yourself.

## 4. Find the jobs and the unmet needs

**4a. Sort every fact into the draft jobs.** A fact can serve several jobs. A fact that fits no job is a missing job (add it) or something to question (a quirk).

**4b. Find each job's answer.** For a question job, it's the answer. For a task job, it's what the user must know before committing (for example how much can still be refunded), or the outcome they need confirmed. Everything else in the job supports it.

**4c. Check the jobs against the screen.** In the research file, not the spec, list which of today's areas feed each job.
- If most jobs are fed by exactly one area, and most areas feed exactly one job, the jobs may be today's areas renamed. Redraft once, from the domain.
- If they still map one-to-one, keep them: the current split may already match the jobs. Say so in the hand-off, so the design brief can explore merging them.
- A job that pulls from three or four areas is a good sign.

**4d. Weigh and number the jobs.** For each: a name in the user's words; **who** has it (every user, admins only, customers on a paid plan, first-time visitors…); **how often** (every visit, weekly, monthly, rarely); and a **priority** (primary, secondary or rare). Order by priority and number them J1, J2… Say how this was judged, for example "judged from the domain; no usage data".

**4e. Write the unmet needs.** From the friction and from 4c, write what users need and don't get, as needs, never as differences from today:
- Not: "Today approvers can only filter by status and currency."
- But: "Approvers need to see the items waiting for them separately from those waiting on a colleague."

Good sources: things a job needs that are spread across places; things grouped together that need different actions; dead ends; actions that vanish without saying why; primary jobs that take several steps; for a website, contradicting claims and questions the page never answers. These give the designer a reason to change things.

## 5. Write the spec

Open with a 1–2 line description, a line saying what the document covers, and the source (repo and commit, or URL and date, so the research can be found). Then:

> This document is for designing a new UI for this [feature / layout / page / component / screen]. It describes what users need and what it must let them **see and do**. It's organised by user jobs, not by how the current version is laid out. The order of sections says nothing about the order on screen.
>
> You decide where and how each thing appears, and how it's split up: merge jobs into one view, split one across several, bring a secondary fact forward, or put rare jobs further away. Copy marked as fixed, rules and conditions can't change.
>
> The job sections say what matters. The **Reference** part at the end holds the full detail, grouped by business object, so nothing is lost. Its order and length say nothing about placement or how much space anything deserves.

For a layout, list the pages that render inside it. For a component, list where it's used. Everything after this opening must keep its promises: no section, sentence or list may decide where things go.

### Feature sections, in order (skip any that don't apply)

These also serve website pages and app screens; their differences follow the list.

1. **Domain context:** the concepts, how the money or rules work, who the user is and why they come.
2. **Who can reach it:** conditions, guards and gaps. Give only the entry route(s).
3. **Jobs at a glance:** J-number, job, who, how often, priority. Nothing else.
4. **Unmet needs:** short bullets, each a need.
5. **Shared concepts,** defined once:
   - **statuses:** status, meaning, what the user can do. "Must be able to tell them apart at a glance." No colours, and nothing about how statuses are grouped today. If items have several independent statuses (payment and fulfilment, say), give one table per dimension and don't say how they're combined today.
   - **what's kept in the link:** describe the state, not the parameters ("the chosen status filter", "which item is open"), plus defaults and what happens with invalid values. Give parameter names or values only if outside links or other systems depend on them, and then as a fixed contract in the Reference.
6. **One section per job,** in priority order, in the job format.
7. **Things it keeps up to date,** and what doesn't refresh. Where the user ends up after an action is said by the item's new status ("they see the order as refunded"), never by a place ("lands on the Refunds tab").
8. **Devices:** must work on desktop and mobile. Gaps are written as needs ("on a phone, the user must be able to compare totals and statuses without scrolling sideways"). Accessibility facts can be stated as facts ("relies on colour alone").
9. **Quirks in the current build:** behaviour that may not be intended, grouped by job. Describe the behaviour, not the control: "refunding stays available after an order is fully refunded", not "the Refund button stays visible".
10. **Not currently active:** features switched off or never shown, in product terms.
11. **Not part of this spec** (optional): legacy or test versions.
12. **Reference.**

**For a website page:**
- The jobs are the visitor's. Content goes into the Reference by topic (product, pricing, proof, company, getting started…), never in page order. Exact copy goes in Copy: fixed when it makes a claim or promise.
- Also cover: the browser title and search description; every call to action and where it goes; every form, with its fields, rules and what happens when it's sent; the site navigation and footer, unless another spec covers them; copy that differs between screen sizes.
- Claims that contradict each other (numbers, timings, prices) go in Quirks, quoted.

**For an app screen:**
- "What's kept in the link" becomes **deep links and restored state**: what can be opened directly, and what's kept when the app is closed or sent to the background.
- Devices becomes **Platforms and devices**: iOS vs Android differences, small and large phones, tablets, keyboard, the device back action, offline, returning from the background, all written as needs.
- Device permission prompts go in the jobs whose flows trigger them, with their copy.

### Layout sections, in order (skip any that don't apply)

1. **Domain context:** the concepts the whole app relies on; who the user is and why they come.
2. **Jobs at a glance.**
3. **Unmet needs.**
4. **Jobs the layout serves,** in priority order, in the job format. For example: "Get to the part of the app I need", "Know which account I'm in, and switch", "Start something new from anywhere", "See where my account stands at a glance", "Finish a required step".
5. **One-off introductions and notices:** what each tells the user, who sees it, when it stops.
6. **Things the layout keeps up to date.**
7. **Devices** (for an app's navigation shell: Platforms and devices).
8. **Quirks in the current build.**
9. **Not currently active.**
10. **Reference.**

### Component sections, in order (skip any that don't apply)

A component is a part, so its spec can be about the part itself. It still mustn't copy how today's version looks or works when a different design would serve the same jobs.

1. **Purpose:** what it lets a user do, in one or two lines, and the concepts it relies on.
2. **Jobs at a glance:** what users are trying to do with it, across the places it's used.
3. **Where it's used:** each place, what the user is doing there, and which jobs matter there.
4. **Unmet needs.**
5. **Options:** what the host can choose and what each choice changes for the user, with defaults. Describe each option by its effect, not its code name: "whether the user can pick a range", not `isRange`.
6. **States:** empty, filled, loading, disabled, read-only, error and any others: what the user can see and do in each, with copy.
7. **What the user can do:** input rules, limits, formatting while typing, selection, keyboard use, closing. Order only where the order is a rule.
8. **What it tells the host:** each thing it reports back (a value chosen, a file added, a flow cancelled), and exactly when.
9. **Content the host can supply,** and what shows when the host supplies nothing.
10. **Accessibility:** what's announced, how it's used without a mouse, how focus moves.
11. **Devices.**
12. **Quirks in the current build,** including places that set it up inconsistently.
13. **Not currently active:** options no place uses, and states that can't be reached.
14. **Reference** (copy lists, and full tables if the options or rules are long).

### Job format

Keep job sections short. Their length follows the job's priority, not how complex today's component is. Detail goes to the Reference.

- **J-number and the job,** in the user's words, with who, how often and priority.
- **The answer they need first:** one line (see 4b).
- **What supports it:** other facts and actions, as needs, most important first. Not how today's version shows them, and not a full field list.
- **Rules that shape it:** only those a designer must design around (limits, required order, when the job is unavailable). Point to the Reference for the rest.
- **Order the system requires,** for a flow: only steps whose order is a rule, then "the rest of the order is the designer's choice". A confirmation, review or extra sign-in before money moves, data is lost or something legal happens is a rule: write "the user must confirm X before Y", never how. Never number today's steps.
- **Loading, empty and error:** what the user is told.
- **Unmet needs** for this job.

### The Reference part

Full detail, in lettered groups by **business object** (Ref A: Order, Ref B: Refund…; for a website, by topic), never by screen area or job. Open it with: "These lists are unordered: their order says nothing about importance or placement."

**Order within each group:** alphabetical, or the data model's order. Never the order of the reports, and never the order of today's lists, tables, forms or page sections.

Each group holds what applies:
- **facts**, each marked *browsing* (needed to pick an item out of many) or *one item* (needed only when looking at it)
- actions, when they're available, and permissions, as a table
- fields, validation rules and messages, as a table
- flow rules: checks in order, limits, defaults, calculations, rounding, timers
- success and failure outcomes, and what refreshes
- outputs the user takes away: format, contents, naming. Treat them as fixed when other systems rely on them.

Then two copy lists at the end of the Reference:
- **Copy: fixed:** wording that carries a rule, a promise, or a legal or money meaning: errors, validation messages, status names, consent statements, empty states, explanations of how things work, claims on a website. Quote exactly. If it wasn't captured exactly, say so and give the meaning. **If fixed copy refers to today's tabs, steps, areas or controls,** quote it with that part replaced by `[where the user finds X]`, and mark it "wording must be adapted to the new structure".
- **Copy: current wording:** labels of fields, facts and actions, quoted, so the designer knows they can change. Never names of today's tabs, groups, sections, steps or areas.

### Writing rules

**The test, for every sentence.** Would it still be true if a designer rebuilt the UI from scratch, with a different structure? If not, rewrite it as what the user needs to know or do, or delete it.

**Never write** (examples, not the full set):
- **placement:** left, right, top, bottom, above, below, beside, next to, underneath, "the second section"
- **visuals:** colours, icons, logos, bold, highlighted, greyed out, blurred, animations, sticky
- **widget names:** panel, drawer, sheet, card, modal, dialog, menu, dropdown, button, tab, table, column, row, banner, badge, pill, tooltip, toast, wizard, accordion, carousel, header, footer (Markdown tables in the spec itself are fine)
- **mechanics:** hover, click, tap, swipe, slide in, collapse, expand
- **current structure, in any form:** "the X section", names of today's tabs, groups, steps, areas or sub-routes (even marked "not a requirement"), numbered steps copied from today's flow, fields or content in today's order
- **current solutions:** how today's version solves a need, even without visual words ("progress across the three bands", "the user can hide and show columns", "a preview after saving")
- **today as the baseline:** "today", "currently", "the current page", except in Quirks, Not currently active, Copy: current wording and the opening lines

**Rephrase as what the user sees, needs or can do:**

| Don't write | Write |
| --- | --- |
| Opens in a panel that slides in from the right | Opening it shows its full details. The user doesn't lose their place among the others. |
| Paid is green, Failed is red | The user must be able to tell Paid from Failed at a glance |
| The button shows a spinner and is disabled | Show that it's busy, and don't let it start again |
| Closes on X, outside click or Escape | The user can close it, including with Escape |
| Tabs: Upcoming / History | The user needs both upcoming and past items, and must always know which they're looking at |
| Groups today: Unpaid, Awaiting approval… (not a requirement) | (Delete.) The user can reach items in any status, always knows which statuses they're looking at, and can come back to the same view. |
| After paying, the user lands on the Paid tab | After paying, the user sees the item as paid |
| Columns: … | Facts in the Reference, each marked *browsing* or *one item* |
| 1. What's being paid / 2. When / 3. Period | Eligibility is checked before any options, and confirmation is last. The rest of the order is the designer's choice. |
| Progress across the three bands, with a marker | The user must be able to tell how far they are from the next tier's threshold |
| Today approvers can only filter by status | Approvers need to see the items waiting for them separately from those waiting on a colleague |
| The FAQ is an accordion with the first item open | The visitor can read any answer; the most common question is answered without them asking |
| Swipe left on a row to delete it | The user can delete an item while browsing |
| "Issued refunds appear in the Refunds tab" (fixed copy) | "Issued refunds appear in `[where the user finds issued refunds]`" (wording must be adapted to the new structure) |

**Always keep** (in the job or the Reference): fixed copy in quotes; validation rules, limits and the order of checks; calculations, thresholds and rounding; defaults; what's kept in the link or deep link, and what can be linked to; when the user can or can't leave a flow; success and failure outcomes; what refreshes; outputs; missing behaviour, as unmet needs.

**Style:**
- Short paragraphs or bullets; tables for statuses, fields and messages.
- No file paths or code names. Entry routes are fine.
- No company or brand names: "Acme Pay" → "Pay", "the Acme sign-in page" → "the sign-in page", "Acme" → "the company". Where fixed copy contains the name, write `<company>`. An address the user must be given, such as a support email, goes in Copy: fixed.
- No analytics unless the user asks.

## 6. Check before handing over

1. **Structure check.** Read only the headings. If they could be today's screen areas (or a website's sections) in today's order, regroup by job.
2. **Structure names check.** Search the spec for every name in the structure names note, including sub-routes and URL values. Allowed: status names, labels in Copy: current wording, and fixed copy with the `[where…]` replacement. Remove anything else.
3. **Order check.** Does any Reference list follow the order of a list, table, form or page section in the research? Reorder it.
4. **Weight check.** A secondary job longer than a primary one is carrying detail that belongs in the Reference.
5. **Word search.** A backstop. Judge each hit; a hit isn't automatically wrong:
   ```
   grep -niE 'slides? in|from the right|at the top|on the (right|left)|next to|side by side|beside|underneath|\babove\b|\bbelow\b|\btop\b|\bbottom\b|corner|overlay|blurred|greyed|colou?r|\bicons?\b|\blogos?\b|panel|drawer|\bsheets?\b|modal|dialog|popover|popup|placeholder|shimmer|darkened|\bbold\b|highlight|\bdots?\b|\bcards?\b|sidebar|button|dropdown|\bmenus?\b|\bbox\b|hover|click|\btap\b|swipe|arrow|chevron|illustration|\bgrid\b|shake|spinner|tooltip|toast|banner|badge|\bpills?\b|\bchips?\b|accordion|carousel|collaps|expand|sticky|full-width|scroll|\btabs?\b|\btables?\b|column|\brows?\b|header|footer|wizard|\bstep [0-9]|\bscreen\b|detail (view|page)|section\b|\barea\b|marker|\bbar\b|\bbands?\b|lands on|\btoday\b|currently|current page' <FILE>
   ```
   Fine: "credit card", "spreadsheet", "balance sheet", a threshold that's "below" a value, "screen size", "app screen" as the target type, an accessibility fact, "expand" meaning "ask for more", "band" as a domain term, the spec's own section references, Markdown tables, and "today" where the writing rules allow it.
6. **Reverse-sketch test.** Give a fresh agent **only the spec**, and tell it not to read any code:

   > "This spec describes an existing [feature / layout / page / component / screen], but deliberately not its layout. Using only the spec:
   > 1. Sketch in words what you think the current version looks like: its main areas, how items are grouped, the order of things, and the steps of each flow.
   > 2. For each guess, quote the part of the spec that gave it away.
   > 3. Flag any sentence that describes appearance, placement, widgets, interaction mechanics or a current solution rather than a need, and suggest a rewrite that keeps fixed copy, rules and conditions. Also flag contradictions and broken references.
   > 4. If you designed a new version from this spec, which parts would you feel obliged to build a particular way, and why?"

   Compare its sketch with the research and the structure names note. **Every correct guess is a leak:** fix the quoted parts. Two exceptions: guesses that are just the common default for this kind of thing (a list of items with a detail view), and guesses that follow from a stated rule or fixed copy. Leave those, and mention the defaults in the hand-off.
7. **Fix pass.** Fix what 5 and 6 flagged, then re-read the job sections against the sentence test.
8. **Unsure facts.** Check surprising claims against the code or the live site. These are often wrong: idempotency, sort orders, timers, what a flag does, which screen size gets which copy.
9. **Format** the file with the project's formatter, if it has one.
10. **Hand off. Don't commit.** Tell the user:
    - what the spec covers, and the jobs and their priorities
    - notable findings: unmet needs, silent failures, dead features, mobile gaps, contradicting claims
    - whether the jobs matched today's split one-to-one (4c)
    - the common defaults the reverse sketch fell back on. Suggest the design brief asks for two or three concepts that differ in structure, at least one avoiding that default.
    - what to spec next:
      - **feature or screen:** the next item in the app's navigation
      - **layout:** the first item in its navigation
      - **website page:** the next item in the site's navigation
      - **component:** the next most-used shared component without a spec
      - if there's no clear order, ask.
