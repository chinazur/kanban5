# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

An IT PMO Kanban board for a fictitious bank's internal IT PMO, built as a demo/training tool. Everything is in one file, `index.html`, which holds the markup, one `<style>` block and one `<script>` block.

## Hard constraints (from the original spec — keep them)

- Vanilla HTML/CSS/JS only: no frameworks, libraries, build step, bundler or npm.
- Single file. It must run when `index.html` is double-clicked (`file://`), with no server.
- No external resources: no CDNs, web fonts or image files. Use the system font stack and inline SVG or Unicode for icons.
- No persistence. Do not use `localStorage`, `sessionStorage`, IndexedDB, cookies or any other storage API. A refresh resets the board to the seed data on purpose, and the header note says so.
- No `alert()` or `confirm()`. Validation errors show inline, and deleting uses an inline "Delete? Yes / No" confirm on the card.
- No `!important`. Colours and spacing come from the CSS custom properties on `:root`.
- Branding: a neutral "IT PMO" text wordmark in a corporate blue palette. Never use real UOB logos or trademarks, or imitate an official UOB system. The `UOB-ITPM-####` task ID format and the `[UOB IT PMO]` email subject come from the spec.
- The only network call allowed is FormSubmit.

## Commands

There is no build, lint or test tooling.
- Run it with `open index.html`.
- Check JS syntax by extracting the script and running `node --check`:
  `sed -n '/<script>/,/<\/script>/p' index.html | sed '1d;$d' > /tmp/app.js && node --check /tmp/app.js`
- Check for constraint violations with this grep. It should return no matches:
  `grep -nE 'localStorage|sessionStorage|indexedDB|document\.cookie|alert\(|confirm\(|!important|<link|src="http' index.html`

## Architecture (inside the `<script>` block)

- **State is the single source of truth:** `state = { tasks, filters, ui, nextId }`. `state.ui` holds short-lived card UI, namely `moveOpenId` (the open Move menu) and `confirmDeleteId` (the open delete confirm), so those views come from state as well.
- **Render from state, never patch cards directly.** Every change goes through `addTask` / `moveTask` / `deleteTask` / `setUi`, which update `state` and then call `renderBoard()`. `renderBoard()` rebuilds all four columns (via `renderCard()`) and the header summary (`renderSummary()`) with `innerHTML`. Because cards are rebuilt on each render, focus is put back afterwards with `focusCard(id, selector)`.
- **Escaping:** every user-supplied or data string must go through `escapeHtml()` before it is added to the HTML templates.
- **Events are delegated** to `#board`. Card buttons carry `data-action` (`move-toggle`, `move-to`, `delete`, `delete-yes`, `delete-no`) and `data-id`, and are handled in `handleBoardClick`. Native HTML5 drag-and-drop handlers sit on the board too, carrying the task id in `dataTransfer` `text/plain`, and the column under the pointer gets the `.drop-target` class.
- **Filters** (`applyFilters`) only affect what is shown. The header summary always counts the unfiltered `state.tasks`, while column badges show `shown / total` when a filter is active.
- **Dates** are local `YYYY-MM-DD` strings created by `toISODate`/`todayISO`, not `toISOString` (which uses UTC and can be a day off). Seed due dates are set relative to today so some cards always show as overdue. `isOverdue` means the due date is before today and the status is not Done.
- **Add Task flow** (`handleTaskSubmit`): validate (`validateForm` → `showFormErrors`), then add the task straight away (optimistic UI), reset the form and show a success toast. After that, call `notifyNewTask()` inside try/catch while the submit button shows "Sending…". If that fails, the card stays and a warning toast appears. The modal stays open on purpose so the "Sending…" state is visible.
- **FormSubmit:** `FORMSUBMIT_ENDPOINT` at the top of the script is the one place to set the email address. It uses the AJAX JSON endpoint with a 15s abort timeout, and a `success: "false"` response counts as a failure. A new address needs a one-time activation, explained in the HTML comment above `<script>`.
- Reference lists (`STATUSES`, `PROJECTS`, `CATEGORIES`, `PRIORITIES`) feed the columns, the filter selects and the form selects through `fillSelect()` in `init()`. Change those lists rather than editing the markup.
