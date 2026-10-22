# Implementation Plan: Todo List Life Dashboard

## Overview

This plan builds the dashboard incrementally in vanilla HTML, CSS, and JavaScript with no frameworks, no backend, and no build step. All structure lives in a single `index.html`, all styling in the one allowed `css/styles.css`, and all behavior in the one allowed `js/app.js`. The JavaScript is organized as module objects inside that single file (`Storage`, `Notify`, `Greeting`, `Timer`, `Todo`, `Links`). No test framework is installed and no test files or directories are created; the property-bearing logic is factored into small pure functions (`greetingFor`, `format`, `validateText`, `Links.validate`) so correctness is easy to reason about and verify manually per the design's Testing Strategy.

Each step builds on the previous one and ends by wiring the new module into the `DOMContentLoaded` bootstrap so there is no orphaned code.

## Tasks

- [ ] 1. Scaffold the page, stylesheet, and app entry point
  - [x] 1.1 Create `index.html` with the semantic page structure
    - Add `<head>` linking the single stylesheet `css/styles.css`
    - Add `<header id="greeting">` region for date, time, and greeting
    - Add `<main>` with `<section id="timer">`, `<section id="todo">`, and `<section id="links">`, each with headings and the input/control/list placeholders described in the HTML Page Structure
    - Add `<div id="notify" aria-live="polite">` for status/error messages
    - Add `<script src="js/app.js">` as the single JS file at the end of `<body>`
    - _Requirements: 1.1, 1.2, 2.1, 6.4, 8.5_

  - [-] 1.2 Create `css/styles.css` with the base layout and visual hierarchy
    - Style the header, main sections, timer display, lists, buttons, inputs, empty-state elements, and the notify region
    - Ensure a clean minimal interface, readable typography, clear visual hierarchy, and responsive layout at narrow and wide widths
    - _Requirements: 5.1, 5.2_

  - [~] 1.3 Create `js/app.js` with the bootstrap skeleton
    - Add a `DOMContentLoaded` listener that will call each module's `init()` in order (Greeting, Timer, Todo, Links)
    - Leave module objects as stubs to be filled in subsequent tasks
    - _Requirements: 1.1, 2.1, 7.1, 9.3_

- [ ] 2. Implement shared helpers (Storage and Notify)
  - [~] 2.1 Implement the `Notify` helper in `js/app.js`
    - Implement `Notify.error(message)`, `Notify.info(message)`, and `Notify.clear()` targeting the `#notify` aria-live region
    - _Requirements: 1.7, 3.2, 3.3, 3.5, 4.2, 4.3, 4.5, 5.4, 6.3, 8.2, 9.2_

  - [~] 2.2 Implement the `Storage` helper in `js/app.js`
    - Implement `read(key)` and `write(key, value)` wrapping `localStorage` in try/catch, returning `{ ok }` results
    - Implement `loadJSON(key)` returning `{ ok, data, corrupt }` (corrupt when the value is present but not parseable / fails shape validation)
    - Implement `saveJSON(key, data)` serializing with `JSON.stringify` and delegating to `write`
    - _Requirements: 3.4, 3.5, 4.4, 4.5, 5.3, 5.4, 6.2, 6.3, 7.1, 7.2, 7.4, 9.1, 9.2, 9.3, 9.5_

- [ ] 3. Implement Greeting_Module
  - [~] 3.1 Implement the pure `greetingFor(hour)` function
    - Map hours 5–11 → "morning", 12–17 → "afternoon", 18–23 and 0–4 → "evening"
    - _Requirements: 1.4, 1.5, 1.6_

  - [~] 3.2 Implement clock rendering and wire Greeting into bootstrap
    - Implement `Greeting.renderClock()` to read `Date`, format time as HH:MM:SS and date as day/month/year, update the DOM, and select the greeting via `greetingFor`
    - Wrap date reading/formatting in try/catch; on failure call `Notify.error` and render a placeholder in the clock region
    - Implement `Greeting.init()` to render once and start a 1000 ms `setInterval`; call it from the bootstrap
    - _Requirements: 1.1, 1.2, 1.3, 1.7_


- [ ] 4. Implement Focus_Timer
  - [~] 4.1 Implement the pure `format(sec)` function
    - Produce zero-padded two-digit `MM:SS` for seconds in the range 0–1500
    - _Requirements: 2.1, 2.7_

  - [~] 4.2 Implement timer state, controls, and wire Timer into bootstrap
    - Implement `Timer.init()` to set `remainingSeconds = 1500`, `state = "stopped"`, and render
    - Implement `start()` (idempotent while running — only create an interval if none is active), `stop()` (pause, retain remaining), `reset()` (stopped at 1500), and `tick()` (decrement by 1; at 0 stop and show the end indication)
    - Wire the Start/Stop/Reset controls and call `Timer.init()` from the bootstrap
    - _Requirements: 2.1, 2.2, 2.3, 2.4, 2.5, 2.6, 2.7_



- [ ] 5. Checkpoint - verify header and timer in the browser
  - Load the page and confirm the clock advances, the greeting shows, and the timer starts/stops/resets and ends correctly. Ask the user if questions arise.

- [ ] 6. Implement Todo_List add, validation, and persistence
  - [~] 6.1 Implement the pure `validateText(text)` function
    - Trim, then accept when trimmed length is 1–500; reject with reason "empty" when trimmed is empty and "too_long" when trimmed length exceeds 500
    - _Requirements: 3.1, 3.2, 3.3, 4.1, 4.2, 4.3_

  - [ ] 6.2 Implement Todo load/render and `add`, and wire Todo into bootstrap
    - Implement `Todo.init()` to load via `Storage.loadJSON("dashboard.tasks")` (empty array when missing; on corrupt, use empty array and overwrite on next write) and render
    - Implement `Todo.render()` to rebuild the list DOM from state in stored order and show the empty state when there are zero tasks
    - Implement `Todo.add(text)` to validate, append a not-done task with a unique id, persist via `Storage.saveJSON`, and render; on save failure keep the in-memory task and call `Notify.error`; on validation failure leave the list unchanged and call `Notify.error`
    - Wire the add input/button and call `Todo.init()` from the bootstrap
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.5, 6.4, 7.1, 7.3, 7.4_


- [ ] 7. Implement Todo_List edit, toggle, delete
  - [ ] 7.1 Implement `Todo.edit(id, text)`
    - Validate via `validateText`; when valid replace only the text (same id, done, order, length); when invalid leave the task unchanged and call `Notify.error`
    - Persist via `Storage.saveJSON`; on save failure keep the updated text and call `Notify.error`
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5_

  - [ ] 7.2 Implement `Todo.toggle(id)`
    - Flip the task's done state, persist, and re-render with the completed visual distinction added/removed; on save failure revert to the previous completion state and call `Notify.error`
    - _Requirements: 5.1, 5.2, 5.3, 5.4_

  - [ ] 7.3 Implement `Todo.remove(id)`
    - Remove the task, persist, and render (showing the empty state when the last task is removed); on save failure retain the task in the displayed list and call `Notify.error`
    - _Requirements: 6.1, 6.2, 6.3, 6.4_

  - [ ]* 7.4 Manually verify edit preserves shape and only changes text when valid (no test framework)
    - **Property 6: Editing a task preserves list shape and only changes text when valid**
    - **Validates: Requirements 4.1, 4.2, 4.3**

  - [ ]* 7.5 Manually verify completion toggle is an involution (no test framework)
    - **Property 7: Task completion toggle is an involution**
    - **Validates: Requirements 5.1, 5.2**
    - Confirm one toggle flips state and two toggles return to the original, with id and text unchanged

  - [ ]* 7.6 Manually verify deleting a task removes exactly that task (no test framework)
    - **Property 8: Deleting a task removes exactly that task**
    - **Validates: Requirements 6.1**

  - [ ]* 7.7 Manually verify tasks persistence round-trip (no test framework)
    - **Property 9: Tasks persistence round-trip**
    - **Validates: Requirements 7.1, 7.2, 3.4, 4.4, 5.3, 6.2**
    - Reload the page and confirm saved tasks reappear with the same text and completion states in the same order

- [ ] 8. Checkpoint - verify the full to-do flow in the browser
  - Add, edit, toggle, and delete tasks; confirm the empty state and reload persistence; corrupt `dashboard.tasks` in dev tools to confirm fallback-to-empty and overwrite on next write. Ask the user if questions arise.

- [ ] 9. Implement Quick_Links
  - [ ] 9.1 Implement the pure `Links.validate(label, url)` function
    - Accept when label length is 1–50 and URL length is 1–2048 and the URL begins with "http://" or "https://"; otherwise reject and identify the offending field ("label" or "url")
    - _Requirements: 8.1, 8.2_

  - [ ] 9.2 Implement Quick_Links load/render, `add`, `remove`, `open`, and wire into bootstrap
    - Implement `Links.init()` to load via `Storage.loadJSON("dashboard.quicklinks")` (empty array when missing; on corrupt, render zero buttons and do NOT overwrite until the next mutation) and render
    - Implement `Links.render()` to rebuild at most 50 buttons from state and show the empty-state indicator when none exist
    - Implement `Links.add(label, url)` to validate, append with a unique id, persist, and render; on validation failure leave the set unchanged and call `Notify.error` naming the field; on save failure show the attempted change and call `Notify.error`
    - Implement `Links.remove(id)` to delete, persist, and render; on save failure keep the set and call `Notify.error`
    - Implement `Links.open(id)` using `window.open(url, "_blank", "noopener")`
    - Wire the label/url inputs, add button, per-button open, and delete controls; call `Links.init()` from the bootstrap
    - _Requirements: 8.1, 8.2, 8.3, 8.4, 8.5, 9.1, 9.2, 9.3, 9.4, 9.5_

  - [ ]* 9.3 Manually verify quick link validation classification and field identification (no test framework)
    - **Property 10: Quick link validation classification and field identification**
    - **Validates: Requirements 8.1, 8.2**

  - [ ]* 9.4 Manually verify deleting a quick link removes exactly that link (no test framework)
    - **Property 11: Deleting a quick link removes exactly that link**
    - **Validates: Requirements 8.4**

  - [ ]* 9.5 Manually verify quick links persistence round-trip with render cap (no test framework)
    - **Property 12: Quick links persistence round-trip with render cap**
    - **Validates: Requirements 9.1, 9.3**
    - Reload to confirm order-preserving persistence and confirm rendering shows min(list length, 50) buttons

- [ ] 10. Final checkpoint - full dashboard verification
  - Smoke-test all four components together in a modern browser, confirm clean responsive layout, and corrupt `dashboard.quicklinks` to confirm zero buttons render and the bad data is preserved until the next add/delete. Ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional lightweight manual-verification steps. No test framework, test runner, or test files/directories are created — verification is done by reasoning about the pure functions and exercising the app in a browser, per the design's Testing Strategy.
- Exactly one CSS file (`css/styles.css`) and one JS file (`js/app.js`) are used; no additional CSS or JS files are introduced.
- All JavaScript modules (`Storage`, `Notify`, `Greeting`, `Timer`, `Todo`, `Links`) live inside the single `js/app.js`.
- Persistence uses Local Storage keys `dashboard.tasks` and `dashboard.quicklinks` only.
- Each task references specific requirement clauses for traceability, and optional verification tasks reference the design's correctness properties by number.

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1.1", "1.2", "1.3"] },
    { "id": 1, "tasks": ["2.1", "2.2"] },
    { "id": 2, "tasks": ["3.1", "3.2", "4.1", "4.2", "6.1", "9.1"] },
    { "id": 3, "tasks": ["3.3", "4.3", "4.4", "6.2"] },
    { "id": 4, "tasks": ["6.3", "6.4", "7.1", "7.2", "7.3", "9.2"] },
    { "id": 5, "tasks": ["7.4", "7.5", "7.6", "7.7", "9.3", "9.4", "9.5"] }
  ]
}
```
