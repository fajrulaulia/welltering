# Elena — Angular UI Agent Instructions

Applies to all files and subdirectories under `elena/`. These instructions are binding for AI-assisted frontend work. Read root `AGENTS.md` as the monorepo entry point.

## Scope and technology

- **Application:** Elena, frontend for Welltering.
- **Framework:** Angular 22.3.x stable, standalone components, strict TypeScript.
- **Styles:** Tailwind CSS v4 + plain CSS for tokens and complex component styles.
- **UI primitives:** Angular CDK when needed for accessible overlay, focus management, and interaction primitives.
- **Icons:** Consistent, lightweight Lucide-style iconography through an Angular-compatible integration.
- **API calls:** Angular HttpClient, consuming Irena's documented REST API contract.
- **No backend edits:** Do not add Java, database code, or Spring configuration to Elena.
- **No unapproved styling stacks:** Do not introduce Bootstrap, Angular Material as a global theme, multiple UI kits, or other CSS frameworks without an explicit decision.

**Important:** Elena has not been scaffolded yet. These are design requirements for subsequent frontend tasks; do not create a frontend app merely because this instruction file exists.

## Non-negotiable Welltering visual identity

Use **exactly two brand colors**:

| Role | Token | HEX | Intent |
| --- | --- | --- | --- |
| Primary | `espresso` | **`#4E3727`** | Main navigation, headings, primary buttons, links, active states |
| Secondary | `cream` | **`#F6F0E6`** | Page background, soft surfaces, quiet sections |

Supporting **neutral** white, charcoal and grays are allowed for cards, table borders, disabled states, and readable content. Semantic success/warning/error colors are allowed **only** for their meanings; they are not brand accents. Do not add a third brand/accent color.

Do not use green, blue, orange, purple, or Tailwind default color scales to substitute for the brand. Do not use visual wood-grain textures, decorative gradients, glassmorphism, neon effects, or heavy shadows in everyday application pages.

### Design tokens (Tailwind v4)

When Tailwind is configured, define brand colors centrally in `elena/src/styles.css` (or an imported token stylesheet). Target:

```css
@import "tailwindcss";

@theme {
  --color-espresso: #4E3727;
  --color-cream: #F6F0E6;
}
```

- Prefer Tailwind semantic brand classes such as `bg-espresso`, `text-espresso`, `bg-cream`, and `text-cream`.
- Prefer reusable component-level tokens for spacing, radius, borders, and focus treatment rather than repeated magic values.
- Do not scatter arbitrary HEX colors in Angular templates or component styles. If a new neutral or semantic color is needed, define it once and explain its role.
- Keep UI color contrast accessible (WCAG AA for normal text where applicable). Always provide visible keyboard focus indicators.

## Required dashboard visual language

Match the agreed Welltering concept:

- **Sidebar:** Dark Espresso background, cream text/icons, clearly distinguished active menu entry. Desktop sidebar typically 240–264px; collapse or use a mobile drawer on smaller screens.
- **Main canvas:** Warm Cream background with generous, consistent spacing.
- **Cards and tables:** White or subtly separated neutral surfaces, restrained borders, soft corners (~10–14px), very light or no shadows.
- **Typography:** Clean modern sans-serif; clear hierarchy, readable forms and tabular data. No ornate or script fonts.
- **Buttons:** Primary = Espresso background with high-contrast text; secondary = outlined/neutral. Use consistent padding, radius, hover, disabled, and focus states.
- **Forms:** Labels above fields; clear validation, error and help text; avoid unlabeled icon-only controls.
- **Lists and tables:** Compact enough for a real operations dashboard but not cramped; readable headers, empty/loading/error states, pagination where appropriate.
- **Charts:** Restrained presentation; do not introduce unrelated bright brand colors simply to decorate charts.
- **Layout:** Desktop-first dashboard, responsive on tablets and mobile (test widths around 1440px, 768px, and 390px).
- **Consistency:** Same spacing scale, card treatment, icon sizing, typography and button variants across pages.

Use the feel of a **premium, warm, modern SaaS dashboard**, not a rustic restaurant menu, generic admin template, or flashy marketing site.

## Component engineering conventions

- Use Angular standalone components and feature-oriented folders; choose predictable names and small reusable UI components.
- Use Angular signals where suitable for local reactive state; use RxJS thoughtfully for asynchronous streams.
- Keep domain/network logic out of purely presentational components.
- Favor typed reactive forms, accessible labels, and meaningful error states.
- Avoid direct DOM manipulation and large template expressions.
- Build reusable elements for navigation, page headers, cards, buttons, inputs, tables, badges, and feedback **only when actually needed**, not as empty placeholders.
- Prefer CSS/Tailwind over inline `style=` attributes or template-specific arbitrary color values.
- Respect reduced-motion preferences. Animations should be subtle and functional.

## AI implementation workflow — follow for every UI task

1. **Inspect:** Read this file, the existing Angular app, existing components, and the shared theme/tokens before generating UI. Never assume Elena exists.
2. **Plan:** State which existing component/layout patterns will be reused and which files need changing.
3. **Implement:** Use the exact Espresso + Cream tokens and the shared dashboard visual language. Never silently invent a new accent palette or switch styling frameworks.
4. **Verify:** Run available Angular lint, test, and build commands. Once rendering tools exist, inspect responsive screenshots at desktop and mobile sizes and compare with the design checklist.
5. **Report:** Summarize files changed, visual consistency checks, accessibility checks, actual test commands, and unresolved gaps. Do not claim visual verification if no screenshots or browser inspection occurred.

## UI acceptance checklist

A UI task is **not ready for review** until all applicable items are checked:

- [ ] Primary `#4E3727` and secondary `#F6F0E6` come from shared design tokens.
- [ ] No unapproved accent color, decorative wood texture, third-party admin dashboard theme, or unrelated visual language.
- [ ] Sidebar/navigation, background, cards, buttons, and form elements match the shared styling conventions.
- [ ] Layout handles desktop and narrow screens without horizontal overflow.
- [ ] Interactive elements work with keyboard and have discernible focus states.
- [ ] Normal text has sufficient contrast; form fields have labels and useful errors.
- [ ] Loading, empty, error, disabled, and success states are handled where relevant.
- [ ] Angular lint/tests/build were run where tooling exists; any unrun checks are explicitly reported.

## Enforcement beyond instruction files

Instructions alone cannot guarantee an AI follows every design rule. **When Elena is scaffolded**, add enforceable checks appropriate to the project:

- Central CSS design tokens and shared reusable UI primitives.
- Lint/style checks (or a small script) rejecting unapproved hard-coded brand colors in application files.
- Component accessibility tests and responsive/visual regression screenshots for key layouts.
- Pull request review checklist for palette, layout, responsive behavior, and accessibility.

Do not invent test results or add CI workflows that cannot run before the Angular project exists.
