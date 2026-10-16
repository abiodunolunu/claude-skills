# claude-skills

Skills for [Claude Code](https://claude.com/claude-code), published as a plugin marketplace.

## Install

```
/plugin marketplace add abiodunolunu/claude-skills
/plugin install page-spec@abiodunolunu
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
