# Project Spec: Inkpad Todo App

## 1. Overview:

Inkpad is a single-page task tracker for personal daily planning. It must load instantly, work without an account or internet backend, and keep the user's tasks between visits.

## 2. Goals:

- Let a user capture a task in under five seconds.
- Make it obvious what is urgent (priority, due date, overdue) and what is finished.
- Ship as one static file that can be deployed to any host.

**Out of scope (v1):** user accounts, multi-device sync, shared lists, notifications.

## 3. Target user:

Students and individuals who want a lightweight daily checklist without signing up for a productivity tool.

## 4. Functional requirements:

| ID | Requirement | Priority |
| --- | --- | --- |
| F1 | Create a task with a title (required, max 140 characters) | Must |
| F2 | Optional due date and high-priority flag when creating | Must |
| F3 | Mark a task done or not done | Must |
| F4 | Edit a task's title inline | Must |
| F5 | Delete a task, with undo for 5 seconds | Must |
| F6 | Filter tasks: All, Active, Done, with counts | Must |
| F7 | Search tasks by title | Should |
| F8 | Clear all finished tasks at once, with undo | Should |
| F9 | Show progress (done out of total) | Should |
| F10 | Flag overdue tasks and label "Due today" and "Due tomorrow" | Should |
| F11 | Persist tasks and the chosen filter across reloads | Must |

**Ordering rule:** unfinished tasks first, sorted by priority (high first), then due date (soonest first, undated last), then creation time. Finished tasks follow, most recently completed first.

**Validation:** an empty or whitespace-only title is rejected with the message "Type a task before adding it." An edit that leaves the title empty keeps the old title.

## 5. Non-functional requirements:

- **Performance:** first render under 1 second; no framework or build step.
- **Accessibility:** WCAG 2.1 AA contrast, every control labelled, full keyboard use, visible focus, live region for progress and undo messages, reduced motion respected.
- **Responsive:** usable from 320px wide phones to desktop.
- **Theming:** light and dark mode from the system setting.
- **Resilience:** if `localStorage` is unavailable, the app still works for the session.
- **Security:** task titles are rendered with `textContent`, never as HTML, to prevent script injection.

## 6. Data model:

```
Task {
  id: string          // unique, generated client-side
  title: string       // 1 to 140 characters
  due: string         // "YYYY-MM-DD" or ""
  priority: "normal" | "high"
  done: boolean
  created: number     // epoch ms
  completed?: number  // epoch ms, set when done
}
```

Storage keys: `inkpad.tasks.v1` (task array), `inkpad.filter.v1` (last filter).

## 7. UI structure:

```
+------------------------------------------+
| Inkpad                                   |
| Friday, October 9                        |
| 1 of 3 tasks done  [=====-----------]    |
+------------------------------------------+
| [What needs doing?] [date] [High] [Add]  |
+------------------------------------------+
| (All 3) (Active 2) (Done 1)   [Search]   |
+------------------------------------------+
| [ ] Write the project README    [e][x]   |
|     Due today, High priority             |
| [ ] Deploy the app              [e][x]   |
| [x] ~~Sketch the layout~~       [e][x]   |
+------------------------------------------+
| 2 tasks left       Clear finished tasks  |
+------------------------------------------+
```

**Visual direction:** engineering-paper grid background, navy ink text, highlighter yellow for high priority, red for overdue. Finishing a task animates an ink strike-through.

## 8. Tools and tech:

- **Design and code generation:** Claude (design plan, HTML, CSS and JavaScript).
- **Stack:** HTML5, CSS custom properties, vanilla JavaScript, `localStorage`.
- **Deployment:** published as a Claude artifact; portable to GitHub Pages, Netlify or Vercel.

## 9. Acceptance criteria:

1. Adding a task with Enter shows it in the list immediately and clears the input.
2. Reloading the page keeps all tasks, their states and the selected filter.
3. Ticking a task strikes it through, moves it below unfinished tasks and updates the progress bar and counts.
4. Deleting a task and pressing Undo within 5 seconds restores it.
5. A task with a past due date that is not done shows "Overdue" in red.
6. Every action can be completed with the keyboard alone.
7. The layout has no horizontal scroll at 320px width.

## 10. Future work:

Drag-and-drop reordering, multiple lists, tags, JSON export and import, reminders, and cloud sync.
