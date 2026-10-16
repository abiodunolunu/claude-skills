# claude-skills

Skills for [Claude Code](https://claude.com/claude-code), published as a plugin marketplace.

## Install

```
/plugin marketplace add abiodunolunu/claude-skills
/plugin install page-spec@abiodunolunu
/plugin install spec-to-todo@abiodunolunu
```

## Skills

### page-spec

Writes a behaviour spec for one page, layout, website page, component or app screen. It reads everything the target renders, including the dialogs and forms it opens, and writes `<NAME>_PAGE.md`, `<NAME>_LAYOUT.md`, `<NAME>_COMPONENT.md` or `<NAME>_SCREEN.md` in the repo root. For a website with no source code, it works from the live URL.

The spec covers what users see and can do: exact copy, validation rules, statuses, what's kept in the URL, loading and empty states, permissions, and quirks in the current build. It deliberately leaves out how things look or where things sit, so a designer can build a fresh UI from it.

Usage:

```
/page-spec pages/settings/index.vue
/page-spec layouts/default.vue
/page-spec components/DatePicker.vue
/page-spec src/screens/CheckoutScreen.tsx
/page-spec https://example.com/pricing
```

It works with any component-based front end, web or native. Some examples in the skill use Vue and Nuxt.

### spec-to-todo

Turns a spec written by page-spec into a build checklist for your project, saved as `<NAME>_TODO.md`.

It checks what's already built, then lists clashes with your current code, quirks in the spec to fix, and decisions you need to make. After that comes every requirement in build order, one per checkbox, with what already works ticked.

Usage:

```
/spec-to-todo REPAYMENTS_PAGE.md
```
