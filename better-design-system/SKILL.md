---
name: better-design-system
description: Rules for building, extending, reviewing, or auditing a design system or component library, whatever the stack. Use when adding a component, changing tokens or theming, designing a component API, writing component docs or stories, or auditing a UI library for consistency and accessibility.
---

# Better Design System

These rules hold for any stack. The headless layer, styling approach, and framework are implementation details. The invariants are **the public contract, semantic tokens, accessibility, and consistency**.

When the repo already has a convention, follow it. When it has none, pick one, apply it everywhere, and write it down.

## Philosophy

- Build only what was asked, plus its edge cases. Propose extensions separately.
- The smaller API wins when two would work.
- No speculative features.
- Reuse and compose before adding a new primitive.
- A design system is a shared language between design and engineering, not a pile of widgets.
- Encode rules in types, lint, and docs so reviews discuss behavior, not taste.
- One bullet, one rule. Keep area rules next to the area.

## Public API

- One clear public surface. Everything else is visibly internal.
- If it is not documented, it is not public.
- Named, predictable exports. No anonymous default export as the component API.
- Document what consumers install (peers) and what is bundled or provided by the host.
- Publishing is not shipping. Document the path to production.
- CSS, fonts, and assets are part of the contract. Make import order and side effects explicit.
- Tree-shaking metadata must tell the truth about side effects.
- Wrap third-party libraries. Expose the contract you own, not theirs.

## Component shape

- One component, one folder: implementation, public entry, docs, examples.
- Split files only when a piece is genuinely large.
- Register every public component in the package entry point.
- Layers: primitives are styled leaves, composed components orchestrate parts, domain components sit on top. Business logic never leaks into primitives.
- The closest existing component of similar complexity is the template. Study it first.

## Composition

- Use compound components for layout-heavy UI (header/body/footer, field/label/description).
- Extra content (icons, badges) goes in as children, not as a growing list of slot props.
- Every slot has an explicit identity. Use one detection mechanism across the system.
- Unknown or invalid children fail loudly.
- A slot wrapped in an extra node is unsupported unless passthrough was designed. Say so in docs.
- Triggers decorating a child accept exactly one element. Use one pattern for this across the system.
- A wrapper that replaces a slot declares which slot it replaces.
- Don't mix compound slots and a config object in one component. Data-driven wrappers that feed the composition API are the exception.

## Props and types

- Props extend the native element's props. Refs reach the underlying element.
- When wrapping a library, type each adopted prop from the library's own types, one by one.
- String unions over enums.
- Boolean props read as questions: `is`, `has`, `should`, `can`.
- States (disabled, invalid, selected, open, loading) are first-class props, not class names.
- `variant` is visual treatment, `size` is dimensions. Intent/color and taxonomy (status kind, badge kind) are separate concepts.
- Size scales can differ per component. Don't force a global scale that doesn't fit.
- Defaults live in the component, visible where props are read.
- Mutually exclusive modes are discriminated unions.
- One instance is either controlled or uncontrolled, never both. Document which. Prefer controlled in examples.
- Each prop gets one short doc sentence. Mark controlled/uncontrolled.
- Event names stay boring and consistent: value change, open change, selection change.
- Consumer `className`/`style` merge last. One override mechanism across the system.
- Polymorphism is fine (render as link, as another element). One Button that can be a link beats two skins. A disabled link is not a dead anchor.

## Styling and tokens

- No hardcoded colors, spacing, or sizes in UI code. Use semantic tokens.
- Token names describe purpose (role, then variant, then state), not palette values.
- State and intent suffixes follow one systematic scheme.
- Primitive palettes exist for generating semantic tokens, not for components.
- Borders, rings, shadows, and outlines use their own color tokens.
- Typography tokens cover size and weight. Deprecated scales stay out of new work.
- Spacing and radius come from a scale. No magic numbers.
- Generated token files are never hand-edited. The source (design tool, token file) is the truth; the pipeline is the only writer.
- Themes are set on an ancestor. Portals and overlays inherit the same theme.
- One way to define variants and merge classes. Not three.
- Style states via attributes, ARIA, and pseudo-classes, not by swapping classes in JS.
- Any CSS flavor is fine. Raw values and one-off arbitrary values are the smell.

## Layout, RTL, motion

- Use logical properties (inline/block, start/end). Physical left/right only when the effect is physical.
- RTL lives in the layout model, not one-off overrides.
- Breakpoints are a small, documented set that matches the product.
- Shared widths (content, page max) are tokens.
- Decorative motion respects reduced-motion preferences.
- Hover effects apply only to fine pointers. Touch doesn't depend on hover.
- Press feedback is a subtle scale, not just a color flash. Skip it where it fights the control.
- Transition specific properties, never `all`. Controls ~150–200ms ease-out; longer only for large surfaces.
- Overlays use shared named animations, not per-file keyframes.
- Overlays grow from their trigger. True modals stay centered.
- Don't animate actions people repeat constantly.

## Accessibility

- Interactive behavior comes from a proven headless layer or an equivalent in-house primitive. Never hand-roll keyboard, focus, and ARIA from a div.
- The headless layer is an implementation detail. The public API stays yours.
- Every control has an accessible name. Missing names fail.
- Hidden native input plus visual skin is fine. Only the input is visually hidden, never the only label.
- Decorative icons and visual-only markers are hidden from assistive tech.
- Focus rings are visible and tokenized. Never remove an outline without a replacement.
- Automated a11y checks, interaction tests, and keyboard checks complement each other. None replaces another.
- Built-in UI copy (close, loading, more) is translated.
- A11y IDs come from the framework or primitive, not random generators.

## Forms

- Native constraint validation by default. Custom validation is the documented exception.
- A field is label + control + description + error, wired so click, focus, and invalid state connect.
- Controls adapt to field context. The field owns the label.
- Submit handling is obvious: prevent default, read form data, map clearly.
- Invalid styling follows the platform's user-invalid behavior or the primitive's invalid state.
- Specialized inputs (OTP, IBAN, masked) are field compositions, not regexes in a page.

## Overlays and stacking

- Choose a stacking model (modal, floating, anchored) and document it.
- Floating UI renders above the topmost modal, never under it.
- Anchored lists may stay in flow or portal. Pick one per pattern.
- Toasts, tooltips, and popovers share one portal target. Nested dialogs don't bury them.
- Open state follows the same controlled/uncontrolled rules as any other state.

## Icons, fonts, assets

- Icons are a versioned system with typed names. No ad-hoc pasted SVGs.
- Asset base URLs come from the host or config, never hardcoded in the library.
- Custom icons go through an explicit API, never raw HTML injection.
- Generated icon indexes are never hand-edited.
- One default font stack with fallback metrics. Extra families are opt-in.
- After changing tokens, fonts, or icons, rebuild what docs and consumers actually load.

## Errors and runtime contract

- Missing provider, invalid children, and missing accessible names throw.
- Don't warn your way through a broken tree. Fail clearly in development.
- Error messages name the system and the component.
- Deprecation lives in types, docs, and examples, not chat.
- The host wraps the app in the system's provider. Hooks never silently no-op outside it.

## Docs, examples, tests

- Every public component has short docs. Examples do the teaching.
- Show every variant and every state, one heading per state.
- Include import, usage, real use cases, and a prop table. Skip the essay for obvious components.
- Examples cover light and dark. Overlays get an explicit open snapshot.
- Interaction tests cover validation and open-then-assert flows.
- Visual regression is part of the system.
- A11y checks that can't fail CI are just documentation. Make them a gate.
- Docs are consistent in language and formatting.
- Never guess public props. Docs and types are the source of truth.

## Lint as the spec

- The style guide is enforced by tooling, not debated in review.
- Be consistent in how helpers and components are declared.
- Imports use source-root aliases, not deep relative paths.
- A rule you care about is in lint. Unenforced guidelines drift.
- Ban what you truly ban (enums, default component exports, unknown classes). No empty ban lists.
- If logical properties or semantic tokens matter, lint physical properties and raw values.

## Domain layer and hosts

- Domain components compose primitives. They don't restyle them from scratch.
- Use the domain wrapper when one exists. Drop to the primitive only for deliberate customization.
- Host configuration (events, translations, content config) is part of component docs.
- Inline text actions go through the Button/Link API, not one-off styled anchors.
- Marketing and content pages use tokens too.

## Versioning and change

- Semver is real. Breaking changes are majors. Pre-releases let hosts test early.
- Commits follow the convention the changelog depends on.
- Upgrades of the a11y/headless layer are high-risk. Read the changelog. Don't automerge on green lint.
- One logical change per commit.

## New component checklist

1. Folder, public entry, docs, examples.
2. Public vs internal is obvious. Refs and native props work.
3. Variants and sizes typed. Booleans prefixed. Controlled state documented.
4. Tokens only. Logical properties. States via attributes/ARIA/pseudos. Motion gated.
5. Keyboard, accessible name, and focus ring work. Fields wire label and error.
6. Invalid composition and missing provider throw.
7. Every variant and state documented. Visual snapshot for the interesting state.
8. Lint is clean against the real style pipeline.

## Audit checklist

- [ ] Public API is finite and documented. Internals can't be imported by accident.
- [ ] Composition has an explicit slot contract and fails on misuse.
- [ ] Tokens are semantic. Generated files untouched. Themes reach portals.
- [ ] One styling strategy. No hardcoded values.
- [ ] Logical layout, documented breakpoints, reduced motion, no hover dependence on touch.
- [ ] Interactive controls use a proven a11y layer. Focus rings tokenized. Names required.
- [ ] Forms have label, error, and native or documented validation.
- [ ] Overlay stacking is a documented decision.
- [ ] Icons, fonts, and assets are a system with a host contract.
- [ ] Docs show every state. Visual tests exist. A11y can fail the build.
- [ ] Lint encodes the real rules.
- [ ] Domain wrappers sit on primitives. Hosts know peers and the release path.
- [ ] Deprecations are typed. Controlled and uncontrolled are never mixed.
