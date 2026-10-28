# claude-skills

Skills for [Claude Code](https://claude.com/claude-code), published as a plugin marketplace.

## Install

```
/plugin marketplace add abiodunolunu/claude-skills
/plugin install page-spec@abiodunolunu
/plugin install spec-to-todo@abiodunolunu
```

You can also copy a skill folder (`plugins/<name>/skills/<name>`) into `~/.claude/skills/` for all your projects, or into a project's `.claude/skills/`.

## The problem these solve

Give a design agent a description of an existing page and ask for a better one, and you usually get the same page back. Even with every word about looks removed, the description still carries the page's shape: sections that follow the screen's areas in order, the names of its tabs and steps, a full field list in screen order, everything at equal weight.

These two skills keep that shape out, from the first spec to the build checklist.

## Skills

### page-spec

Writes a design brief for an existing web app feature, layout, website page, component or app screen. It reads everything the target renders (or the live URL when there's no source) and writes `<NAME>_PAGE.md`, `<NAME>_LAYOUT.md`, `<NAME>_COMPONENT.md` or `<NAME>_SCREEN.md`.

The spec is organised by **user jobs** — the questions people bring and the tasks they come to do — drafted from the domain before any code is read, and weighted by priority. Each job states the answer the user needs first. Things users need and don't get are listed as **unmet needs**, so the designer has a reason to change things. Every field, rule, message and condition is kept in a **Reference** grouped by business object, in an order that carries nothing from the current screen. Today's tab, group, step and route names never appear.

Before handing over, it checks itself: headings, structure names, list order, section weight, a word search, and a **reverse-sketch test**, where a fresh agent tries to guess the current page from the spec alone. Every correct guess is a leak to fix.

Research is saved in `.page-spec/research/` and reused when the code hasn't changed, so reruns are fast.

Usage:

```
/page-spec the orders page
/page-spec layouts/default.vue
/page-spec components/DatePicker.vue
/page-spec src/screens/CheckoutScreen.tsx
/page-spec https://example.com/pricing
```

It works with any component-based front end, web or native. It reads your project's `CLAUDE.md` (or `AGENTS.md`) for stack hints and your brand name, and those win over its defaults.

### spec-to-todo

Turns a spec written by page-spec into a build checklist for your project, saved next to the spec as `<NAME>_TODO.md`.

The checklist is the brief for the agent that designs and builds the page, so it keeps the same discipline: boxes say what, never where; sections are jobs, not parts of a page; page-shaping components are only suggested conditionally. The agent designs the whole page first, with a height budget, before building any piece.

It checks what already exists, then lists clashes with your code, quirks to fix (you decide which), unmet needs to meet now or later, and decisions you need to make. Then every requirement in build order, one per box, each citing where it came from in the spec, with a coverage check so nothing in the spec is lost.

Usage:

```
/spec-to-todo ORDERS_PAGE.md
```

Your project's own rules (in `CLAUDE.md`, `AGENTS.md` or design docs) win over the skill's defaults, so you can add house rules without editing the skill.
