---
name: fostrwski-way
description: Code-shape, lint, typing, import, styling, git and review rules for large frontend codebases. Use when writing, refactoring, reviewing or configuring tooling in any frontend/TypeScript project.
---

# fostrwski-way

Rules describe the shape of code, not the stack. Project config and lint output override this file where they are stricter.

## Lint is the spec

- Treat the linter as the source of truth for style. Never argue format in review.
- Keep strict presets on. Never disable a rule silently; override explicitly in config.
- Fix every lint error. Never suppress without a written reason.
- Use one formatter. Linter-driven formatting over a second competing tool.
- Reuse `.gitignore` as the lint ignore list. Never keep a parallel list.
- Run lint with autofix on all staged files in pre-commit.
- Regenerate generated artifacts before committing so lint never fights them.

## Functions and components

- Use function declarations for plain functions, not `const fn = () =>`.
- Use `const X = wrapper(...)` only when a wrapper requires it (e.g. `forwardRef`, `memo`).
- Write components as stateless functions. No class components.
- Default props via destructuring defaults. No `defaultProps`.
- Name state and setter as a pair (`value` / `setValue`).
- Use the modern JSX transform. Never import the framework namespace just for JSX.
- Let types flow from props and inference. Do not demand explicit return types on public API.

## Types

- Use `type`, never `interface`.
- No enums. Use unions or `as const` objects.
- No `any`. No non-null assertion `!`.
- Narrow instead of casting with `as`.
- Run the compiler in strict mode with no unused locals/params and no switch fallthrough.
- Use `@ts-expect-error` only with a description. Never `@ts-ignore`.
- Write method signatures in property style.
- Merge overloads into unified signatures.
- Mark Promise-returning functions `async`.
- No redundant or duplicate union members. No `void` outside return types.
- Prefer `??`, `?.`, `includes`, `startsWith`/`endsWith`.

## Loops

- No `for`, `for…of`, `for…in`, `while`. Iterate with array methods (`map`, `filter`, `reduce`, `forEach`, `Object.entries`).
- No labels, no `with`.

## Naming

- Prefix booleans with `is` / `has` / `should` / `can` / `did` / `will` followed by PascalCase (`isDisabled`, `hasError`).
- Apply this to variables, params and props alike.

## Imports and exports

- Use named exports. No default exports except where a tool requires one.
- No anonymous default exports.
- No `import * as`.
- Use named imports from the framework. No default namespace import.
- No parent-relative imports (`../`). Use a root alias (`@/`). Use `./` only within the same folder.
- Put type imports in a separate top-level `import type` statement. No inline `type` specifiers.
- Order groups: builtin → external (framework first) → internal alias → sibling/index → `import type`.
- Separate groups with a blank line. Sort alphabetically, case-insensitive, within groups and within specifier lists.
- Map the alias to source and the package name to its public entry.
- Import devDependencies only in tooling, stories, scripts and tests.

## Layout

- Cap lines at 120 characters. URLs are exempt.
- Break multiline arrays after `[` and before `]`, one element per line.
- Break multiline ternaries on every branch.

## Comments

- Comment only non-obvious intent. Never narrate code.
- Start comments with a capital letter.
- Use `/* * */` block style for multiline comments, not stacked `//`.
- Put a blank line before a comment, not after. Start of block is exempt.
- Mark sections of compound files with a banner whose `=` rule matches the name length:

```text
/*
 * ======
 * Button
 * ======
 */
```

## JSX

- Use `<>` fragments, not `<Fragment>`. Keyed fragments are the only exception.
- Put the first prop on a new line when there is more than one prop.
- Sort props: `key`/`ref` → shorthand booleans → alphabetical → multiline last.
- Never leak falsy values into render. Use `cond ? <X /> : null`, not `cond && <X />`.
- Pair controlled inputs with a handler or `readOnly`.
- No raw HTML injection or unsafe lifecycles without a documented reason.

## Utility CSS

- Validate classes against the built CSS output. Never guess a class exists.
- Error on unknown classes, conflicting classes and duplicates.
- Enforce a stable class order and a stable syntax for variables and `!important`.
- Use shorthands over longhands where equivalent.
- Keep class strings on one line. Order matters, wrapping does not.
- Warn on deprecated classes. Never silence them.
- Maintain an actual ban list for restricted classes (physical direction utilities, raw hex colors, legacy tokens). An empty ban rule is unfinished work.
- Teach the class-merge utility the project's custom tokens so merging never drops valid classes.

## Git and PRs

- Name branches `type/TICKET-short-description`.
- Write commits as `type(scope): lowercase summary (TICKET)`, one-line subject, code identifiers in backticks.
- Make one logical step per commit. No WIP, noise or empty commits.
- Generate the changelog from conventional commits. Keep pre-release tags out of it.
- Open a draft PR immediately. Mark ready only when all checks and visual review pass.
- Ship a pre-release build from the branch for testing when the project publishes packages.
- Never merge with red CI.

## Dependencies

- Automerge patch updates on green CI.
- Never automerge accessibility, headless-UI or other behavior-defining libraries. Read the changelog and verify visually and against consumers.
- Never let bots bump peer dependencies.
- Treat peer-dependency workarounds as a known compromise, never as permission to loosen peers.

## Component docs and visual tests

- Use the product's real breakpoints as viewports, not tool defaults.
- Cover light and dark themes and reduced motion in visual tests.
- Render docs on design tokens and theme classes, not the tool's default backgrounds.
- Disable saving changes from the docs UI.
- Order docs: getting started → foundations → utils → components → domains → work in progress.
- Make accessibility checks able to fail CI. A non-blocking a11y check is debt.

## Writing for models

- Write for the model: no motivation, no background, no reassurance.
- One line, one rule, imperative mood.
- State the rule, not the reason. Add an example only when the sentence is ambiguous.
- Generalize. Never paste the incident that caused the rule.
- Write descriptions as what + when. Cut "try to", "usually", "consider".
