---
name: imagegen-frontend-web
description: Generate design reference images (mockups) for the IT PMO Kanban project, such as a new board view, card or modal variant, a header or filter redesign, or a section for the GitHub Pages / README landing page. Use only when the user asks for a visual mockup or reference image. The images are references for building the UI in index.html, not assets to ship.
---

# Image-Directed Frontend Reference: IT PMO Kanban

Adapted for this repo from the `imagegen-frontend-web` skill. It guides generating mockup images that a developer can then recreate in code. It does not build the UI itself: use the `frontend-design` skill for that.

## Requirements

An image-generation tool or model must be available in the session. If none is, say so, offer a text description or an ASCII wireframe instead, and stop. Do not invent a tool.

## Project context (this overrides any generic art direction)

- **Product:** internal IT PMO Kanban board for a fictitious bank. A demo and training tool for PMO analysts and project managers.
- **Identity:** neutral "IT PMO" text wordmark, corporate blue palette. Never show real UOB logos, trademarks or an imitation of an official UOB system.
- **Palette to match** (from `:root` in `index.html`): navy header `#0b2a4a` to `#14508c`, pale blue surfaces `#e7eff9`, page background `#f2f5f9`, white cards, text `#1b2733`, muted `#5b6b7c`. Priority colours: Critical `#c62828`, High `#c77700`, Medium `#1a5ea8`, Low `#7d8996`. Do not introduce a second accent colour or gradients.
- **Type:** the system sans-serif stack. Show hierarchy through weight and size, not a decorative typeface.
- **Content:** use the real vocabulary and fictitious sample data. Columns are Backlog, In Progress, Blocked and Done. Cards carry a `UOB-ITPM-####` ID, a title, a project, an assignee, a due date, a priority badge and a category tag. Overdue cards show a red "Overdue" badge. Keep copy plain and specific, with no marketing language such as "unleash", "seamless" or "next-gen".
- **Look:** calm, dense, legible, scannable at a glance. Use the existing card shape: white card, priority stripe on the left edge, `8px` radius, one soft shadow.

## Hard constraints the mockup must respect

A mockup is only useful if `index.html` can reproduce it, so do not depict anything that breaks these:

- Vanilla HTML, CSS and JS in a single file. No frameworks or libraries.
- No photography, stock imagery, full-bleed photo backgrounds, custom web fonts or external icon sets. Icons are inline SVG or Unicode only.
- No persistence features (saved views, remembered filters, login) and no browser `alert()` or `confirm()` dialogs. Errors and delete confirmation are inline.
- Must work on `file://` with no backend.
- Mobile-implied: tap-friendly targets and a sane single-column stacking order.

Ignore the generic advice to use full-bleed photos, big hero statements, Awwwards-style asymmetry or image-led layouts. They do not suit a working tool.

## Output rules

- **One image per view or section**, horizontal (16:9 or 16:10), never a collage of several. Label each "View X of N: <name>". Typical views: full board, Add Task modal, card with Move menu open, delete confirm, filter bar with a filter active, empty column, narrow/mobile width.
- If the user does not say how many views, propose the list and the count first, then generate. Do not default to eight.
- Keep every image in one brand world: same palette, type scale, radius and shadow.
- Each image must show layout, hierarchy, spacing, component states and CTA priority clearly enough to code from. Primary action ("Add Task") is unmistakable, and secondary actions look secondary.
- Show state accurately: an overdue card, a Blocked column, a focused control with the amber focus ring (`#ffb300`), or an inline error, when relevant.
- No fake dashboards, charts or KPI strips that the board does not have. The header summary is the only stats element: Total, Backlog, In Progress, Blocked, Done, Overdue.

## For the README or GitHub Pages page

If the user wants a project landing page or README visuals:

- Show the real product. Prefer a screenshot of the running board (`docs/screenshot.png`, taken with Playwright over a local server) to a generated picture. Use generated mockups only for concepts that don't exist yet.
- A landing page for this project is small: hero with the board screenshot, features, how to run it, link to the repo. Default to 4 views, not 6 to 12.

## Where images go

- Save generated references to the scratchpad directory, not the repo, and show them to the user.
- Put an image in `docs/` only if the user approves it as documentation. Never add images to the repo root or reference them from `index.html`.
- Do not commit or push unless asked.

## Process

1. Confirm the subject, the views and the count in one short message.
2. Pick the composition and show component states for each view, keeping to the palette above.
3. Generate each view as its own horizontal image, labelled.
4. Check each against this list: hierarchy obvious, palette and type match the board, no real branding, buildable in single-file vanilla HTML/CSS/JS, copy plain and in the project's vocabulary.
5. After the user picks a direction, hand off to `frontend-design` to implement it in `index.html`.
