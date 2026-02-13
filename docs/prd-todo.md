# Product Requirements Document (PRD) - TODO App Enhancement: Due Dates, Priorities, and Filters

## 1. Overview

We are upgrading the basic TODO app to support due dates, priorities, and filters so users can better organize and manage their tasks. The current app only supports task titles and completion status, which is too basic for practical use. This enhancement will add critical functionality including optional due dates, three-level priority system (P1, P2, P3), and filtering capabilities (All, Today, Overdue) to help users focus on what matters most. The goal is to create a simple, teachable MVP without backend changes, keeping all data in local storage.

---

## 2. MVP Scope

### Core Data Model
- Add `dueDate` field to tasks
  - Optional field
  - ISO format `YYYY-MM-DD`
  - Invalid date values should be ignored/treated as absent
- Add `priority` field to tasks
  - Enum values: `P1 | P2 | P3`
  - Default value: `P3`
  - Required field with default
- Maintain existing `title` field (required)
- Maintain existing `completed` field

### Filtering Functionality
- Implement three filter tabs: **All**, **Today**, **Overdue**
- **All** filter
  - Display all tasks (both complete and incomplete)
- **Today** filter
  - Display only tasks due today
  - Show incomplete tasks only
- **Overdue** filter
  - Display only past-due tasks (due date < current date)
  - Show incomplete tasks only

### Data Storage
- Continue using local storage (no backend changes)
- No external storage integration

### Data Validation
- Title: Required (existing validation)
- Priority: Must be one of `P1`, `P2`, or `P3`; defaults to `P3`
- Due Date: Optional; invalid formats ignored and treated as absent

---

## 3. Post-MVP Scope

### Visual Enhancements
- Overdue task highlighting
  - Highlight overdue tasks in red for visual prominence
- Priority color-coded badges
  - P1: Red badge
  - P2: Orange badge
  - P3: Gray badge

### Sorting Logic
- Implement comprehensive sorting algorithm with the following priority order:
  1. Overdue tasks first
  2. Sort by priority (P1 → P2 → P3)
  3. Sort by due date (ascending)
  4. Tasks without due dates last

---

## 4. Out of Scope

- Notifications (push, email, or in-app)
- Recurring tasks functionality
- Multi-user functionality or sharing
- Keyboard navigation features
- Accessibility enhancements (beyond basic standards)
- External storage or backend integration
- Database connectivity
- User authentication or accounts
