---
name: page-spec
description: Write an exhaustive product/behaviour spec for ONE page, layout (the shell pages render inside), website page, component, or app screen, by recursively reading everything it renders. Works from source code, or from a live URL when there's no source. Output is <NAME>_PAGE.md, <NAME>_LAYOUT.md, <NAME>_COMPONENT.md or <NAME>_SCREEN.md. Use when the user asks to "do the same for <page/layout/component/screen>", "write a spec for <x>", "list everything in/on <x>", gives a URL to spec, or names the next item in the navigation. The spec describes what users see and do, never placement or visuals.
---

# Page, layout, component or screen spec

The output is a Markdown spec of ONE thing that an agent (or designer) can rebuild it from, with a better UI. It lists everything in it, everything inside those things, and everything inside those. It describes **behaviour, not appearance**. Don't mention the "better UI" goal in the spec or the conversation.

**Why no visuals.** A design agent reads this spec to come up with a fresh UI. Every word about how the current UI looks or where things sit steers it towards copying the current design. If a sentence describes the current appearance, it doesn't belong.

**What are you specifying?**

| Target | What it is | Output file |
| ------ | ---------- | ----------- |
| **Page** | One route's content in a web app. It relies on the layout and doesn't repeat it, except for the layout parts that belong to the page's own feature. Repeat those so the page spec stands on its own. | `<PAGE>_PAGE.md` |
| **Layout** | The shell that pages render inside, for example `layouts/default.vue` or a root `App` with navigation. It covers navigation, identity, organisation switching, quick-create actions, app-wide summaries, prompts and banners, and the flows they start. | `<NAME>_LAYOUT.md` |
| **Website page** | A public page of a marketing or content site, such as a home or pricing page. Its shell (site navigation and footer) is part of the spec unless another spec covers it. It can be specced from source or from the live URL. | `<SITE>_<PAGE>_PAGE.md` |
| **Component** | One reusable component specced on its own, for example a date picker, an upload widget or a payment form. It covers every way it can be set up and every state it can be in, across all the places that use it. | `<NAME>_COMPONENT.md` |
| **App screen** | One screen of a native or mobile app (React Native, Flutter, SwiftUI, Jetpack Compose and so on). Treat the app's navigation shell (tab bar, drawer, stack) as its layout, and spec that shell with the layout sections if asked. | `<SCREEN>_SCREEN.md` |

If it's unclear which one the user means, for example a bare domain name or a component that's also a route, check the code or the URL first, then ask.

## 1. Map it

1. Find the entry point:
   - **page:** the route file and the root component it renders
   - **layout:** the layout file, plus which pages use it (the default layout = pages that don't opt into another one)
   - **website page, from source:** the route or template file, plus the site shell it renders inside
   - **website page, live only:** the URL. See "Working from a live site" below
   - **component:** the component file, plus **every place it's used** (search for its name). The places it's used decide which options and states matter
   - **app screen:** the screen file, how it's registered with the navigator, and every way to reach it (other screens, deep links, notifications)
2. List its direct children and the flows it opens (modals, drawers, sheets, forms, global modals triggered by events). Also list:
   - **layout:** the stores and data it loads, and the global events it listens to
   - **component:** the options the host can set, the things it reports back to the host, and any content the host can slot in
   - **app screen:** device permissions it asks for (camera, location, notifications), and what it does offline or when the app comes back to the foreground
3. Split it into 2–5 areas of roughly equal size. Typical splits:
   - **Page or app screen:**
     - the list (browsing, filters, empty states, export, page access)
     - one item (the detail view and actions on it)
     - each major flow (create, pay, withdraw, and so on)
   - **Layout:**
     - navigation (sidebar, mobile menu, tab bar)
     - the top bar (account, organisation, quick-create actions)
     - the summary areas (balance overview and similar)
     - banners and app-wide modals
     - the stores, data and events that keep it all current
   - **Website page:**
     - the content sections, in page order
     - forms and calls to action, and where each one goes
     - the site shell (navigation, menus, footer)
     - variations between screen sizes
   - **Component:** usually small enough for one agent. Split by mode if it has several (for example single vs range selection), or by consumer if the places that use it set it up very differently.
4. Note any legacy or test versions (old routes, `-old` files, unused variants), so they can be listed separately and not specced.

### Working from a live site

When there's no source, only a URL:

- Fetch the raw HTML (`curl -sL -A "Mozilla/5.0" <url>`) into a new, empty folder of its own. A summarising fetch paraphrases long copy, and the spec needs exact wording.
- Extract the text and links in page order with a small script kept in a different folder. Run Python with `-I`.
- Site builders (Framer, Webflow, Wix and others) often render a separate copy of the page for each screen size, hidden or shown with media queries. Map each copy to its screen-size range, and report copy that differs between them.
- Content that's only revealed when the visitor interacts (closed FAQ answers, menus, counters, carousels past the first slide) is often missing from the HTML. Look for it in the site's JavaScript bundles.
- If a browser tool is available, use it to check the interactive parts.
- Date the spec, and say it was taken from the live site. Without source there's no "Not currently active" section, beyond what the bundles reveal.

## 2. Research with parallel agents

Launch one `Explore` agent per area **in a single message** (very thorough). For a small target (under about 500 lines in all, or a single component), read it yourself instead. Each brief must say:

- **Repo path and stack hints.** For example: in Nuxt the components are auto-imported, so `V2FooBar` = `components/v2/Foo/Bar.vue`.
- **The target's purpose.** "I'm writing a PRODUCT/BEHAVIOUR spec, not a code map."
- **The exact root components for its area,** and what the other agents cover.
- **Recursion rules.**
  - Recurse into every component rendered, including modals, drawers and forms they open, and global modals.
  - Shared and Base components count: report their user-visible behaviour (closing, Escape, outside click, selection, pagination options, accepted files).
  - Stop at UI primitives (`components/ui/*` or the design system's equivalents) and icons.
- **What to report:**
  - exact copy and labels, defaults and conditions (status, role, feature flag)
  - validation rules and their messages
  - success and error copy, and what refreshes afterwards
  - URL state, including invalid values and push vs replace. For an app screen: deep links, navigation parameters and what's restored after the app is closed
  - loading, empty and error states
  - API calls (method, URL, params, which response fields are used and which aren't)
  - permissions and feature flags (what each one changes)
  - **component:** each option and what it changes, what it reports back and when, and how each place that uses it sets it up
  - **app screen:** platform differences (iOS vs Android), the device back action, keyboard handling, offline behaviour, and device permission prompts with their copy
  - **website page:** where every call to action and link goes, every form and what happens when it's sent, the browser title and search description
  - analytics events, briefly
- **"Read files fully. Include file:line refs. List dead, disabled and commented-out code in its own section. No recommendations."**

When reports conflict, check the code yourself, for example a legacy component vs the current one.

## 3. Write the spec

Save it in the repo root, named as in the table above. Open with a 1–2 line description, a line saying what the document covers, then:

> The rest of this document describes what the page must let users **see and do**. Where and how each thing appears is up to you.

Use "the layout", "the component" or "the screen" instead of "the page" where it fits. For a layout, add which pages render inside it. For a component, add where it's used. Then **honour that line**.

### Page sections, in order (skip any that don't apply)

This list also serves website pages and app screens. Their differences follow it.

1. **Domain context:** the concepts, how the money or rules work, and who the user is and why they come.
2. **Who can reach the page:** navigation conditions, route guards, and gaps (for example "no eligibility check on the page itself").
3. **Statuses:** a table of status, meaning and what the user can do. Say "must be able to tell them apart at a glance". Don't name colours.
4. **Browsing:**
   - groups and filters, and what's kept in the link
   - fields shown per item
   - pagination
   - loading and empty states (exact copy)
   - export
5. **Viewing one item:** every field, linkability, loading, closing.
6. **Flows:** where each one starts, when it's blocked (in order, with messages and actions), fields and rules, steps, results.
7. **Related parts of the layout** that belong to this feature.
8. **Things the page keeps up to date,** and what doesn't refresh.
9. **Devices:** must work on desktop and mobile. Name the current mobile gaps.
10. **Quirks in the current build:** behaviour that may not be intended. Group them by area.
11. **Not currently active:** features in the code that are switched off or never shown, described in product terms.
12. **Not part of this page** (optional): legacy or test pages.

**For a website page,** sections 3–5 usually become the content sections in page order: each one's exact copy, and where each call to action goes. Also cover:
- the browser title and search description
- every form, with its fields, rules, and what happens when it's sent
- the site navigation and footer, unless another spec covers them
- copy that differs between screen sizes
- claims that contradict each other (numbers, timings, prices)

**For an app screen,** replace "what's kept in the link" with deep links and restored state. Section 9 becomes **Platforms and devices**: iOS vs Android differences, small and large phones, tablets, keyboard, the device back action, offline, and returning from the background. Add device permission prompts to the flows that trigger them.

### Layout sections, in order (skip any that don't apply)

1. **Domain context:** the business concepts the whole app relies on, and who the user is and why they come.
2. **What the layout must provide:** one subsection per thing, for example:
   - getting around (each section, and when it's available)
   - identity and account
   - organisation
   - quick creation
   - balance or summary information
   - required steps (for example a phone number)
   - one-off introductions
   - notices
3. **Flows the layout starts:** each one in full (fields, rules, steps, results), as in a page spec.
4. **Things the layout keeps up to date:** what changes it, and where the change must show.
5. **Devices.**
6. **Quirks in the current build.**
7. **Not currently active.**

### Component sections, in order (skip any that don't apply)

1. **Purpose:** what the component lets a user do, in one or two lines, and the concepts it relies on.
2. **Where it's used:** each place, how it's set up there, and what the user is doing at that point.
3. **Options:** a table of what the host can choose and what each choice changes for the user, with defaults. Describe each option by its effect, not by its code name. "Whether the user can pick a range" is right, `isRange` is not.
4. **States:** empty, filled, loading, disabled, read-only, error, and any others. What the user sees and can do in each one, with exact copy.
5. **Behaviour:** everything the user can do with it, in order: input rules, limits, formatting while typing, selection, keyboard use, closing.
6. **What it tells the host:** each thing it reports back (a value chosen, a file added, a flow cancelled), and exactly when.
7. **Content the host can supply:** what the host can put inside it, and what shows when the host supplies nothing.
8. **Accessibility:** what's announced, how it's used without a mouse, and how focus moves.
9. **Devices.**
10. **Quirks in the current build,** including places that set it up inconsistently.
11. **Not currently active:** options no place uses, and states that can't be reached.

### Writing rules: behaviour, never appearance

**The test, for every sentence.** Would this sentence still be true if a designer rebuilt the UI from scratch? If not, it's describing appearance. Rewrite it as what the user sees or can do, or delete it.

The lists below are examples of what fails the test. They aren't the full set.

**Never write:**
- **placement:** left, right, top, bottom, above, below, beside, corner, next to, side by side, underneath, "the second section", "between X and Y in the sidebar"
- **visuals:** colours, icons, illustrations, dots, bold, highlighted, greyed out, blurred, darkened, shimmer, placeholder shapes, animations, full-width, sticky
- **widget names:** panel, drawer, sheet, card, modal, popup, dropdown, button, tabs, table, grid, box, arrow, chevron, badge, pill, chip, tooltip, toast, accordion, carousel
- **mechanics:** hover, click, tap, swipe, slide in, shake, collapse, expand, "X button"

**Rephrase as what the user sees or can do:**

| Don't write                                      | Write                                                          |
| ------------------------------------------------ | -------------------------------------------------------------- |
| Opens in a panel that slides in from the right   | Opening it shows its full details. The list stays where it was. |
| A blurred overlay covers the form                | The user can't use the form, and is told why                    |
| Paid is green, Failed is red                     | The user must be able to tell Paid from Failed at a glance      |
| Overdue rows have a red dot                      | Overdue repayments must stand out from the rest                 |
| Hover shows the full name in a tooltip           | Long names can be cut short, but the full name must be readable |
| The button shows a spinner and is disabled       | Show that it's busy, and don't let it start again               |
| Closes on X, outside click or Escape             | The user can close it, including with Escape                    |
| Tabs: Upcoming / History                         | The user moves between two sets, and must always know which one they're in |
| Columns: …                                       | For each item, the user must be able to see: …                  |
| The FAQ is an accordion with the first item open | The visitor can read any answer. The first is available without doing anything |
| Swipe left on a row to delete it                 | The user can delete an item from the list                       |

Colour-only signals in the current build may be kept as a fact, for example "today the calendar relies on colour alone on small screens".

**Always keep:**
- exact copy, in quotes
- validation rules, limits and the order of checks
- defaults
- what's in the URL, or the deep link
- what can be linked to
- when the user can or can't leave a flow
- what happens on success and failure
- what refreshes afterwards
- missing behaviour, such as no search or no retry

**Style:**
- Short paragraphs or bullets, and tables for status, field and message lists.
- No file paths or code names in the spec. Route paths like `/payments` are fine.
- No company or brand name in the spec. Use the plain name instead: "Acme Insights" → "Insights", "the Acme sign-in page" → "the sign-in page", "Acme" → "the company". Where exact copy contains the name, write `<company>`.
- Analytics events are left out unless the user asks for them.

## 4. Check before handing over

1. **Read-through: the main check.** Re-read every sentence against the test in the writing rules, and fix any that fail.
2. **Grep: a backstop.** No word list is complete, and a hit isn't automatically wrong, so judge each hit:
   ```
   grep -niE 'slides? in|from the right|at the top|on the (right|left)|next to|side by side|beside|underneath|\babove\b|\bbelow\b|\btop\b|\bbottom\b|corner|overlay|blurred|greyed|colou?r|icon|panel|drawer|sheet|modal|popup|placeholder|shimmer|darkened|bold|highlight|\bdot\b|card|sidebar|button|dropdown|\bbox\b|hover|click|\btap\b|swipe|arrow|chevron|illustration|grid|shake|spinner|tooltip|toast|badge|\bpill\b|\bchip\b|accordion|carousel|collaps|expand|sticky|full-width|scroll|tab\b|footer|logo' <FILE>
   ```
   Fine to keep:
   - "credit card"
   - a real gap such as "scrolls sideways on mobile"
   - an accessibility fact such as "relies on colour alone"
   - "expand" meaning "the user can ask for more"

   Not fine: anything describing where something sits or how it looks, such as "below the amount" or "a red badge".
3. **Fresh-eyes review.** Launch one `general-purpose` agent with **only the spec file**, and tell it not to read any code. Brief it:

   > "This spec will be given to a designer who must invent a brand-new UI. Flag every sentence that describes the current appearance, placement, widgets or interaction mechanics rather than what the user sees or can do. For each one, quote it and suggest a behaviour-only rewrite that keeps the exact copy, rules and conditions. Don't flag exact UI copy in quotes, rules, or missing behaviour. Also flag contradictions and broken section references."

   Apply the fixes you agree with. It can't fill in visuals from memory of the code, which is why it catches what the author misses.
4. **Unsure facts.** Check any surprising claim against the code (or the live site) yourself. These are often wrong:
   - idempotency
   - sort orders
   - timers
   - what a flag does
   - which screen size gets which copy
5. **Format.** Run `npx prettier --write <FILE>` if the project has Prettier.
6. **Don't commit.** Tell the user what the spec covers and the notable findings (silent failures, dead features, mobile gaps, contradicting claims). Then name what comes next:
   - **page or screen:** the next item in the app's navigation
   - **layout:** the first item in its navigation
   - **website page:** the next item in the site's navigation
   - **component:** the next most-used shared component that has no spec yet
