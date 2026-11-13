# IT PMO Project Board

A single-page Kanban board for a fictitious bank's internal IT PMO. It is an internal demo and training tool. It uses a neutral "IT PMO" text wordmark and does not use any real bank logo or imitate any official system.

Everything is in one file, [index.html](index.html), in plain HTML, CSS and JavaScript. There are no frameworks, build step, external resources or server.

## Run it

Double-click `index.html`, or open it in any modern browser.

## Features

- Four columns: Backlog, In Progress, Blocked, Done, each with a live task count.
- Cards show ID, title, project, assignee, priority, due date and category. Cards are colour-coded by priority and flagged "Overdue" when past due and not Done.
- Move cards by drag and drop, or with the keyboard-accessible **Move ▸** button on each card.
- Delete with an inline "Delete? Yes / No" confirmation.
- Add tasks through a validated form (`UOB-ITPM-####` IDs).
- Filter by project, assignee (contains) and priority.
- Header summary: total, per-status counts and overdue count.

## No persistence

The board lives in memory only. No localStorage, cookies or any other storage is used. Refreshing the page resets it to the eight seeded demo tasks. This is intended.

## Email notifications (FormSubmit)

Each new task can trigger an email through [FormSubmit](https://formsubmit.co) using its AJAX endpoint.

1. Open `index.html` and change the address in the config constant at the top of the script:
   ```js
   const FORMSUBMIT_ENDPOINT = "https://formsubmit.co/ajax/YOUR_EMAIL@example.com";
   ```
2. Add one task. FormSubmit sends a confirmation email to that address the first time.
3. Click the activation link in that email. Notifications only deliver after this one-time step.

While the placeholder address is unchanged, or if the request fails, the card is still added and a warning toast says the email notification failed. The board never depends on the network call. The email address is sent only to this endpoint.

## Making it persistent

Replace the in-memory seed in `init()` with a load from a backend API or database, and have `addTask()`, `moveTask()` and `deleteTask()` write to it. Generate task IDs on the server, and add authentication and conflict handling for multi-user use. Rendering reads only from `state`, so it would not need to change.
