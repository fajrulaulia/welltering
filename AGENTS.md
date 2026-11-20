# Welltering — AI Coding Agent Rules

This is a two-project monorepo. **Before editing, determine which project the request targets.** Read the relevant project instructions and keep changes in that project's directory.

| Project | Directory | Stack | Instructions |
| --- | --- | --- | --- |
| Elena | `elena/` | Angular frontend | [`elena/AGENTS.md`](elena/AGENTS.md) |
| Irena | `irena/` | Java 25 / Spring Boot backend | [`AGENT.MD`](AGENT.MD) |

## Required routing

- For frontend pages, Angular components, styling, layouts, UX, and visual design: **read and follow `elena/AGENTS.md`**. Do not edit Irena.
- For Java, REST APIs, security, database, Flyway, Hibernate, and Maven: **read and follow `AGENT.MD`**. Put application code inside `irena/`; do not edit Elena.
- For cross-project changes: explain why each project must change; do not silently modify the other application.
- Do not assume the apps have been scaffolded. At the time these instructions were added, this repository contained documentation only.
- Keep all changes small, testable, and reviewable. Disclose actual verification results; never claim unexecuted commands passed.

## Design-specific requirements

Elena's visual identity is **Dark Espresso (`#4E3727`) + Warm Cream (`#F6F0E6`)**. This palette applies to the Angular UI only and must **not** be used to make Irena API decisions.

AI instructions guide generation but cannot technically guarantee design fidelity on their own. For Elena, also follow the review/verification checklist in `elena/AGENTS.md`; when the app exists, automate token checks and visual regression tests where feasible.
