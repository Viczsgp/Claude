# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file IT PMO Kanban demo app for a fictitious bank: [index.html](index.html) is the entire project (markup, `<style>`, `<script>`). There is no build, lint, test, package manager or server. Run it by opening the file in a browser (double-click, or `Start-Process index.html` on Windows). Node is not installed in this environment, so there is no scripted syntax check; verify changes in the browser.

## Hard constraints (from the original brief)

- Vanilla HTML/CSS/JS only: no frameworks, bundlers, CDNs, web fonts or image files. Icons are Unicode/inline SVG.
- Must work from `file://`.
- No persistence of any kind: no localStorage, sessionStorage, IndexedDB or cookies. A refresh resets to seed data, by design (the header says so).
- No `!important`; colours and spacing come from `:root` custom properties.
- Use a neutral "IT PMO" text wordmark and blue palette. Do not use real UOB logos or imitate official UOB systems. The `UOB-ITPM-####` ID format and `[UOB IT PMO]` email subject prefix are deliberate, as specified.
- The only network call is `notifyNewTask()` to FormSubmit. The destination email lives only in `FORMSUBMIT_ENDPOINT` (top of the script) and must not be sent anywhere else.

## Architecture

All state lives in one `state` object (`tasks`, `filters`, and a transient `ui` sub-object for the open Move menu, pending delete and post-render focus target). The data flow is one-directional: actions mutate `state`, then call `renderBoard()`, which rebuilds the whole board and header summary via `innerHTML`. Never mutate card DOM directly; add the state flag and render it in `renderCard()`.

Things that span the file and are easy to break:

- **Escaping:** every user-supplied string must go through `escapeHtml()` when concatenated into HTML. Toasts use `textContent`.
- **Event delegation:** because the board is re-rendered wholesale, drag/drop and click handlers are attached once to `#board` (see `wireBoardEvents()`) and dispatch on `data-action` / `data-id` attributes. New card controls should follow that pattern rather than binding per-element listeners.
- **Focus restoration:** `state.ui.focusSelector` is set before a render and consumed at the end of `renderBoard()` so keyboard focus survives the re-render.
- **Add Task flow** (`handleSubmit`): validate, then `addTask()` (optimistic render), then `await notifyNewTask()` in try/catch, with a warning toast on failure, and finally reset the form and close the modal. A FormSubmit failure must never break the board. `notifyNewTask()` short-circuits (throws) while the endpoint still contains the `YOUR_EMAIL@example.com` placeholder.
- **Counts:** the header summary counts all tasks; column badges count only filtered (visible) tasks.
- **Dates:** ISO `YYYY-MM-DD` strings compared lexicographically against a local-time `todayISO()` (not UTC). Seed due dates are relative to today so overdue examples always exist.
- **Task IDs:** `nextTaskId()` uses a module-level counter (seed uses 0001-0008).
