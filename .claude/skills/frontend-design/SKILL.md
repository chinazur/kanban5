---
name: frontend-design
description: Guidance for UI and visual design changes to the IT PMO Kanban board (index.html). Use when adding or reshaping UI such as cards, columns, modals, filters, toasts or the header, and when polishing layout, typography, colour and accessibility within the project's single-file, vanilla, no-dependency constraints.
license: Complete terms in LICENSE.txt
---

# Frontend Design: IT PMO Kanban

Adapted from the Anthropic `frontend-design` skill for this repo. The subject, audience and visual identity are already decided, so the job is to extend the existing design consistently, not to invent a new one.

## Context

- **Product:** an internal IT PMO Kanban board for a fictitious bank, used as a demo and training tool.
- **Audience:** PMO analysts, project managers and engineers scanning status quickly, often on a laptop screen or a projector.
- **Primary job:** show what is in each status, what is overdue and who owns it, and make moving or adding a task fast.
- **Identity:** a neutral "IT PMO" text wordmark in a corporate blue palette. It is calm, dense and legible. Never use real UOB logos or trademarks, and never imitate an official UOB system.

## Hard constraints (from CLAUDE.md, never break them)

- Everything stays in one `index.html`: markup, one `<style>` block, one `<script>` block.
- Vanilla HTML, CSS and JS. No frameworks, libraries, build step or npm.
- No external resources: no CDNs, web fonts, image files or `<link>`. Use the system font stack (`--font`) and inline SVG or Unicode for icons.
- No `!important`. Manage specificity with selector structure and order.
- No `alert()` or `confirm()`. Errors show inline, and destructive actions use an inline confirm.
- No persistence APIs. Do not store theme, filters or anything else.
- It must work from `file://` with no server.

After any change, run the syntax check and the constraint grep from CLAUDE.md.

## Use the existing design tokens

Colours and spacing come from the custom properties on `:root`. Reuse them and do not hard-code hex values or pixel spacing in new rules.

- **Blues:** `--blue-900` to `--blue-100` for chrome, headings and selected states.
- **Neutrals:** `--bg`, `--surface`, `--text`, `--muted`, `--border`.
- **Priority:** `--prio-critical`, `--prio-high`, `--prio-medium`, `--prio-low`.
- **Feedback:** `--danger`, `--success`, `--warning` with their `-bg` pairs, and `--focus` for the focus ring.
- **Spacing and shape:** `--space-1` to `--space-6`, `--radius`, `--radius-sm`, `--shadow`, `--shadow-lg`.
- **Type:** `--font` (system stack). IDs use the monospace stack already set on `.card-id`.

If a new token is truly needed, add it to `:root` next to its siblings and name it the same way.

## Design principles for this board

1. **Status first.** The column, the priority stripe on the card edge and the Overdue badge carry the meaning. Do not add decoration that competes with them.
2. **Colour is information.** Red is for Critical and Overdue, amber for High, blue for Medium, grey for Low. Never reuse these colours for something else. Never rely on colour alone: keep the text label (for example "Overdue", "Critical") next to it.
3. **Density with breathing room.** Cards should stay scannable at four columns on a laptop. Prefer tightening copy to adding height.
4. **One accent moment.** The header summary and the "Add Task" button are the focal points. Keep the rest quiet.
5. **Consistent shape.** One radius scale (`--radius`, `--radius-sm`) and one shadow for cards. Do not mix styles per component.
6. **Motion only on user action.** Opening the Move menu, the modal and toasts may animate briefly. No page-load or hover flourishes, and respect `prefers-reduced-motion`.
7. **Typography.** Sentence case, system font. Use weight and size, not new typefaces, to build hierarchy. Keep line lengths short. Avoid ALL-CAPS labels, decorative eyebrows and middle-dot meta strings unless the existing UI already uses them.

## Copy

- Name things as the PMO would: Backlog, In Progress, Blocked, Done; Project, Assignee, Priority, Due date.
- Buttons say what happens ("Add Task", "Delete", "Move"). The same word is used in the confirm and the toast.
- Errors say what is wrong and how to fix it, with no apology. Empty columns say what to do next.
- All sample data stays fictitious. Task IDs keep the `UOB-ITPM-####` format.

## Working with the code

- **State drives the view.** Change `state`, then call `renderBoard()`. Never patch card DOM directly. Cards are rebuilt on each render, so restore focus with `focusCard(id, selector)`.
- **Escape everything.** Any data or user string going into a template goes through `escapeHtml()`.
- **Delegated events.** Add new card controls as `data-action` / `data-id` buttons handled in `handleBoardClick`, not as per-element listeners.
- **Reference lists.** To change columns, projects, categories or priorities, edit `STATUSES`, `PROJECTS`, `CATEGORIES` or `PRIORITIES`, not the markup.
- **Dates** are local `YYYY-MM-DD` strings (`toISODate` / `todayISO`). Overdue means due before today and not Done.
- **Specificity.** Keep new selectors flat, such as `.card-title` and `.card--overdue`. Avoid chaining that forces overrides, since `!important` is not available.

## Quality floor

Check these on every UI change and don't announce them:

- **Layout:** works from desktop width down to phone width. Columns stack or scroll without breaking the page.
- **Keyboard:** every control is reachable, shows a visible focus ring (`--focus`) and can be used without a mouse. The modal traps and restores focus, and Escape closes it.
- **Screen readers:** inputs have labels, errors are linked with `aria-describedby`, and toasts are announced politely.
- **Contrast:** text and badges meet WCAG AA on their background, including the white-on-blue header.
- **Reduced motion:** transitions are disabled or minimal under `prefers-reduced-motion`.

## Process

1. Read the relevant CSS and `render*` functions before changing anything, and match the surrounding style and comment density.
2. Sketch the change in a sentence or an ASCII wireframe if it affects layout, and check it against the principles above.
3. Make the smallest change that does the job.
4. Run the syntax check and constraint grep from CLAUDE.md.
5. If Playwright is available, serve the folder over local HTTP (Playwright blocks `file:`), take a screenshot and review it. Stop the server afterwards. Refresh `docs/screenshot.png` only when the board's look has actually changed.
