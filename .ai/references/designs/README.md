# References — Designs

> Topic reference folder. Agents read this folder **only when the active task needs it** (`../../system/CONTEXT_MANAGEMENT_RULES.md`, `TOKEN_OPTIMIZATION_RULES.md`) — never the whole tree. Starts empty; material is added as the project develops. **Do not create fake reference content.**

## What belongs here

Design references: mockups, wireframes, design-system exports, style/brand notes, and links to design tools — the intended look and interaction.

## Supported file types

Images (`.png`, `.jpg`, `.svg`), `.md` notes, and links to design sources. Keep binaries lean; prefer links to the source of truth.

## Naming conventions

`kebab-case` by screen/flow (`login-screen.png`, `dashboard-wireframe.svg`). Version with a suffix (`-v2`) rather than overwriting when the prior version still matters.

## How agents should use it

Read when building UI (screens, pages, components) to match the intended design. A design reference guides implementation.

## References are not requirements

Material here is **context, not commitment.** A design reference does **not** automatically become an approved spec; it is a requirement only when reflected in `../../templates/REQUIREMENTS.md` / tasks and approved at the gates (`../../system/QUALITY_GATES.md`). Cite it when it informs implementation; never let an unreviewed reference silently drive scope.
