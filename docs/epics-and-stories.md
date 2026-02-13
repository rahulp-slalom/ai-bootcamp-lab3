# Epics and Stories - TODO App Enhancement

## MVP

### Epic: Task Data Model Enhancement

#### Story: Add dueDate field to task model
**Acceptance Criteria:**
- Task model includes optional `dueDate` field
- Field accepts ISO format `YYYY-MM-DD` strings
- Field can be null or undefined
- Existing tasks without dueDate continue to function normally

**Technical Requirements:**
- **Backend**: Database schema already includes `due_date DATE` column in `packages/backend/src/app.js`
- **Backend**: Ensure all API endpoints (POST, PUT) properly handle `due_date` field
- **Backend**: Validate date format is ISO `YYYY-MM-DD` or null
- **Frontend**: Update task state management in `packages/frontend/src/App.js` to include `dueDate`
- **Frontend**: Ensure `dueDate` is included in API requests (create/update)
- **Testing**: Add backend tests in `packages/backend/__tests__/tasks.test.js` for date validation
- **Testing**: Add frontend tests in `packages/frontend/src/__tests__/App.test.js` for dueDate handling

#### Story: Add priority field to task model
**Acceptance Criteria:**
- Task model includes `priority` field
- Field accepts string values: `P1`, `P2`, or `P3`
- Field is present on all task objects

**Technical Requirements:**
- **Backend**: Add `priority TEXT NOT NULL DEFAULT 'P3'` column to tasks table in `packages/backend/src/app.js`
- **Backend**: Update CREATE TABLE statement to include priority field
- **Backend**: Ensure POST/PUT endpoints accept and validate priority field
- **Backend**: Add CHECK constraint or application-level validation for P1, P2, P3 values
- **Frontend**: Update task object interfaces/types to include priority field
- **Frontend**: Include priority in all API request payloads
- **Testing**: Add backend tests for priority field validation
- **Testing**: Add frontend tests for priority in task objects

#### Story: Set default priority value to P3
**Acceptance Criteria:**
- New tasks without explicit priority are assigned `P3`
- Default value is applied during task creation
- Tasks can override default by explicitly setting priority

**Technical Requirements:**
- **Backend**: Set DEFAULT 'P3' in database schema in `packages/backend/src/app.js`
- **Backend**: Apply default in POST endpoint if priority not provided
- **Frontend**: Set default value `P3` in TaskForm component state in `packages/frontend/src/TaskForm.js`
- **Frontend**: Pre-select P3 in priority dropdown for new tasks
- **Testing**: Test that tasks created without priority have P3 value
- **Testing**: Test that explicit priority values override default

#### Story: Validate dueDate format as ISO YYYY-MM-DD
**Acceptance Criteria:**
- System validates dueDate format matches `YYYY-MM-DD` pattern
- Valid examples: `2026-02-13`, `2026-12-31`
- Invalid formats are rejected (e.g., `02/13/2026`, `2026-2-1`)

**Technical Requirements:**
- **Backend**: Add validation function in `packages/backend/src/app.js` to check date format
- **Backend**: Use regex pattern `/^\d{4}-\d{2}-\d{2}$/` for validation
- **Backend**: Return 400 error for invalid date formats in POST/PUT endpoints
- **Frontend**: Use HTML5 date input type which enforces YYYY-MM-DD format in `packages/frontend/src/TaskForm.js`
- **Frontend**: Add client-side validation before API submission
- **Testing**: Add test cases for valid and invalid date formats
- **Testing**: Test boundary cases (leap years, month/day ranges)

#### Story: Validate priority enum values (P1, P2, P3)
**Acceptance Criteria:**
- System only accepts `P1`, `P2`, or `P3` as valid priority values
- Case-sensitive validation
- Invalid values are rejected with appropriate error

**Technical Requirements:**
- **Backend**: Create validation function in `packages/backend/src/app.js`
- **Backend**: Check if priority is in ['P1', 'P2', 'P3'] before INSERT/UPDATE
- **Backend**: Return 400 status with error message for invalid values
- **Frontend**: Use dropdown/select component to restrict input to valid values in `packages/frontend/src/TaskForm.js`
- **Frontend**: Add PropTypes or TypeScript types for type safety
- **Testing**: Test all valid values (P1, P2, P3) are accepted
- **Testing**: Test invalid values (p1, P4, empty string) are rejected
- **Testing**: Test case-sensitivity enforcement

#### Story: Handle invalid date values by treating as absent
**Acceptance Criteria:**
- Invalid date formats are ignored without throwing errors
- Tasks with invalid dates are treated as having no due date
- Invalid dates do not prevent task creation or updates
- System logs or provides feedback about invalid dates

**Technical Requirements:**
- **Backend**: Implement try-catch in date validation in `packages/backend/src/app.js`
- **Backend**: Set due_date to NULL if validation fails
- **Backend**: Log warning message for invalid dates using morgan/console
- **Backend**: Continue with task creation/update despite invalid date
- **Frontend**: Sanitize date input before sending to backend in `packages/frontend/src/TaskForm.js`
- **Frontend**: Display user-friendly warning message for invalid dates
- **Testing**: Test that tasks with invalid dates are created with NULL due_date
- **Testing**: Verify no errors thrown for invalid date formats

---

### Epic: Task Form Enhancement

#### Story: Add due date input to task form
**Acceptance Criteria:**
- Task creation form includes date input field
- Task editing form includes date input field
- Date input accepts ISO format `YYYY-MM-DD`
- Field is optional and can be left empty
- Date picker UI element provided for ease of use

**Technical Requirements:**
- **Frontend**: Already implemented in `packages/frontend/src/TaskForm.js` (line 122-145)
- **Frontend**: Uses Material-UI TextField with `type="date"`
- **Frontend**: Includes `InputLabelProps={{ shrink: true }}` for proper label behavior
- **Frontend**: State variable `dueDate` manages field value
- **Frontend**: `normalizeDateString()` helper function formats dates properly
- **Frontend**: Field sends value as `due_date` in API payload
- **Testing**: Add test for date input rendering
- **Testing**: Test date input change handling and form submission
- **Testing**: Verify empty date field sends null/empty to backend

#### Story: Add priority dropdown to task form
**Acceptance Criteria:**
- Task creation form includes priority dropdown
- Task editing form includes priority dropdown
- Dropdown displays three options: P1, P2, P3
- Dropdown defaults to P3 when creating new tasks
- Selected priority is saved with the task

**Technical Requirements:**
- **Frontend**: Add new TextField/Select component to `packages/frontend/src/TaskForm.js`
- **Frontend**: Add state variable `const [priority, setPriority] = useState('P3')`
- **Frontend**: Use Material-UI Select component with MenuItem for P1, P2, P3
- **Frontend**: Position field after description, before due date
- **Frontend**: Include priority in handleSubmit payload: `{ title, description, priority, due_date: dueDate }`
- **Frontend**: Update useEffect to set priority from initialTask when editing
- **Testing**: Test priority dropdown renders with all three options
- **Testing**: Test default value is P3 for new tasks
- **Testing**: Test priority value updates on selection change
- **Testing**: Test priority is included in form submission

#### Story: Display priority options (P1, P2, P3) in form
**Acceptance Criteria:**
- All three priority levels are visible in dropdown
- Options are clearly labeled (P1, P2, P3)
- Current priority value is pre-selected when editing
- User can change priority selection before submitting

**Technical Requirements:**
- **Frontend**: Implement in same Select component from previous story in `packages/frontend/src/TaskForm.js`
- **Frontend**: Use Material-UI MenuItem components for each option:
  ```jsx
  <MenuItem value="P1">P1 - High Priority</MenuItem>
  <MenuItem value="P2">P2 - Medium Priority</MenuItem>
  <MenuItem value="P3">P3 - Low Priority</MenuItem>
  ```
- **Frontend**: Ensure `value={priority}` prop on Select matches current state
- **Frontend**: Apply consistent Material-UI styling to match existing form fields
- **Testing**: Test all three options are rendered in dropdown
- **Testing**: Test correct option is pre-selected when editing existing task
- **Testing**: Verify visual labels are clear and intuitive

---

### Epic: Task Filtering System

#### Story: Create filter tabs UI (All, Today, Overdue)
**Acceptance Criteria:**
- Three filter tabs are visible: All, Today, Overdue
- Only one tab can be active at a time
- Active tab is visually distinct from inactive tabs
- Clicking a tab switches the active filter
- Default view is "All" filter on page load

**Technical Requirements:**
- **Frontend**: Add Material-UI Tabs component to `packages/frontend/src/App.js` or create new FilterTabs component
- **Frontend**: Add state: `const [activeFilter, setActiveFilter] = useState('all')`
- **Frontend**: Use Material-UI Tabs and Tab components:
  ```jsx
  <Tabs value={activeFilter} onChange={(e, val) => setActiveFilter(val)}>
    <Tab label="All" value="all" />
    <Tab label="Today" value="today" />
    <Tab label="Overdue" value="overdue" />
  </Tabs>
  ```
- **Frontend**: Pass activeFilter prop to TaskList component
- **Frontend**: Position tabs above TaskList, below TaskForm
- **Testing**: Test tabs render with correct labels
- **Testing**: Test tab switching updates activeFilter state
- **Testing**: Test default active tab is "All"
- **Testing**: Verify only one tab can be active at a time

#### Story: Implement All filter to show all tasks
**Acceptance Criteria:**
- All filter displays every task regardless of due date
- Shows both completed and incomplete tasks
- Tasks without due dates are included
- No date-based filtering is applied

**Technical Requirements:**
- **Frontend**: Modify TaskList component in `packages/frontend/src/TaskList.js`
- **Frontend**: Accept `activeFilter` prop from parent
- **Frontend**: When activeFilter === 'all', display all tasks from API response
- **Frontend**: No filtering logic needed for "all" - just render tasks array as-is
- **Frontend**: Ensure existing behavior (showing completed tasks) is preserved
- **Backend**: No changes needed - GET /api/tasks already returns all tasks
- **Testing**: Test that all tasks are visible when "All" filter is active
- **Testing**: Verify both completed and incomplete tasks are shown
- **Testing**: Test tasks with and without due dates are included

#### Story: Implement Today filter to show tasks due today
**Acceptance Criteria:**
- Today filter shows only tasks due on current date
- Compares task dueDate with current system date
- Only incomplete tasks are displayed
- Tasks without due dates are excluded

**Technical Requirements:**
- **Frontend**: Add filtering logic in `packages/frontend/src/TaskList.js`
- **Frontend**: Create helper function:
  ```javascript
  const getTodayDate = () => {
    const today = new Date();
    return today.toISOString().split('T')[0]; // Returns YYYY-MM-DD
  }
  ```
- **Frontend**: Filter tasks array:
  ```javascript
  const filteredTasks = tasks.filter(task => {
    if (activeFilter === 'today') {
      return task.due_date === getTodayDate() && !task.completed;
    }
    // ... other filter logic
  });
  ```
- **Frontend**: Render filteredTasks instead of tasks in List component
- **Testing**: Test only tasks with today's date are shown
- **Testing**: Test completed tasks are excluded
- **Testing**: Test tasks without due dates are excluded
- **Testing**: Mock Date to ensure consistent test results

#### Story: Implement Overdue filter to show past-due tasks
**Acceptance Criteria:**
- Overdue filter shows only tasks with dueDate before current date
- Only incomplete tasks are displayed
- Tasks without due dates are excluded
- Completed tasks are excluded even if overdue

**Technical Requirements:**
- **Frontend**: Add to filtering logic in `packages/frontend/src/TaskList.js`
- **Frontend**: Extend filter condition:
  ```javascript
  if (activeFilter === 'overdue') {
    const today = getTodayDate();
    return task.due_date && task.due_date < today && !task.completed;
  }
  ```
- **Frontend**: Ensure null/undefined due_date values are excluded
- **Frontend**: Use string comparison for dates (works because ISO format)
- **Testing**: Test only past-due tasks are shown
- **Testing**: Test completed tasks are excluded
- **Testing**: Test tasks without due dates are excluded
- **Testing**: Test tasks due today or in future are excluded

#### Story: Hide completed tasks in Today filter
**Acceptance Criteria:**
- Today filter excludes tasks marked as completed
- Only incomplete tasks due today are shown
- Completing a task removes it from Today view
- Uncompleting a task adds it back to Today view if due today

**Technical Requirements:**
- **Frontend**: Already implemented in "Implement Today filter" story's `!task.completed` condition
- **Frontend**: Ensure fetchTasks() is called after task completion toggle in `packages/frontend/src/TaskList.js`
- **Frontend**: Verify handleToggleComplete triggers re-render with updated filter
- **Testing**: Test completing a task removes it from Today filter
- **Testing**: Test uncompleting a task (set completed=false) shows it in Today filter if due today
- **Testing**: Test real-time update when task completion status changes

#### Story: Hide completed tasks in Overdue filter
**Acceptance Criteria:**
- Overdue filter excludes tasks marked as completed
- Only incomplete overdue tasks are shown
- Completing a task removes it from Overdue view
- Uncompleting a task adds it back to Overdue view if overdue

**Technical Requirements:**
- **Frontend**: Already implemented in "Implement Overdue filter" story's `!task.completed` condition
- **Frontend**: Same pattern as Today filter - uses `!task.completed` in filter logic
- **Frontend**: Ensure consistent behavior across both Today and Overdue filters
- **Testing**: Test completing overdue task removes it from Overdue filter
- **Testing**: Test uncompleting task shows it in Overdue filter if still overdue
- **Testing**: Test consistency between Today and Overdue filter behavior

#### Story: Show completed tasks in All filter
**Acceptance Criteria:**
- All filter displays completed tasks
- Completed tasks are visually distinguishable (e.g., strikethrough)
- Both completed and incomplete tasks appear together
- No tasks are hidden based on completion status in All view

**Technical Requirements:**
- **Frontend**: Already implemented in `packages/frontend/src/TaskList.js` (lines 134-145)
- **Frontend**: Existing styling applies textDecoration: 'line-through' for completed tasks
- **Frontend**: Color changes to gray (#9e9e9e) for completed tasks
- **Frontend**: Ensure "all" filter bypasses completed status check
- **Frontend**: Verify existing Checkbox toggle functionality works correctly
- **Testing**: Test completed tasks are visible in All filter
- **Testing**: Verify strikethrough styling is applied to completed tasks
- **Testing**: Test toggling completion status updates visual appearance

---

### Epic: Local Storage Updates

#### Story: Update local storage schema to include dueDate
**Acceptance Criteria:**
- Task objects in local storage include `dueDate` field
- Field is persisted when saving tasks
- Field is retrieved when loading tasks
- Null/undefined values are handled correctly

**Technical Requirements:**
- **Note**: PRD specifies local storage only (no backend), but current implementation uses backend API
- **Backend**: Database already includes `due_date` field and persists it via SQLite in `packages/backend/src/app.js`
- **Backend**: Better-sqlite3 handles persistence automatically (though in-memory currently)
- **Frontend**: No localStorage implementation currently exists - using API calls
- **If implementing localStorage**: Use `localStorage.setItem('tasks', JSON.stringify(tasks))` in App.js
- **If implementing localStorage**: Retrieve with `JSON.parse(localStorage.getItem('tasks') || '[]')`
- **Decision needed**: Clarify if localStorage should replace API or supplement it
- **Testing**: Test dueDate persists across page reloads
- **Testing**: Test null/undefined dueDate values are handled gracefully

#### Story: Update local storage schema to include priority
**Acceptance Criteria:**
- Task objects in local storage include `priority` field
- Field is persisted when saving tasks
- Field is retrieved when loading tasks
- Priority value is maintained across page refreshes

**Technical Requirements:**
- **Backend**: Add `priority` column to database schema (as specified in "Add priority field" story)
- **Backend**: Ensure priority is included in SELECT queries and persisted via INSERT/UPDATE
- **Frontend**: Include priority in task objects throughout application state
- **If implementing localStorage**: Priority field automatically included when serializing task objects
- **If implementing localStorage**: Ensure JSON.stringify/parse handle priority correctly
- **Testing**: Test priority persists across page reloads
- **Testing**: Test default value P3 is maintained if not specified
- **Testing**: Verify priority retrieved matches priority saved

#### Story: Migrate existing tasks with default priority P3
**Acceptance Criteria:**
- Existing tasks without priority are assigned `P3` on load
- Migration happens automatically on first load after update
- No existing tasks are lost during migration
- Migrated tasks function identically to newly created tasks

**Technical Requirements:**
- **Backend**: Use DEFAULT 'P3' in ALTER TABLE or CREATE TABLE statement in `packages/backend/src/app.js`
- **Backend**: If database already has tasks, run migration SQL:
  ```sql
  UPDATE tasks SET priority = 'P3' WHERE priority IS NULL;
  ```
- **Frontend**: Add migration logic in App.js useEffect on initial load:
  ```javascript
  useEffect(() => {
    const migrateTasks = async () => {
      const tasks = await fetchTasks();
      const needsMigration = tasks.some(t => !t.priority);
      if (needsMigration) {
        // Update tasks without priority
      }
    };
    migrateTasks();
  }, []);
  ```
- **Testing**: Test existing tasks receive P3 priority after migration
- **Testing**: Test migration runs only once (idempotent)
- **Testing**: Verify no data loss during migration
- **Testing**: Test tasks with existing priority are not modified

---

## Post-MVP

### Epic: Visual Task Indicators

#### Story: Add red highlighting for overdue tasks
**Acceptance Criteria:**
- Tasks with dueDate before current date display with red visual indicator
- Highlighting is prominent and easily noticeable
- Only incomplete overdue tasks are highlighted
- Highlighting updates automatically when date changes
- Completed tasks are not highlighted even if overdue

**Technical Requirements:**
- **Frontend**: Modify ListItem styling in `packages/frontend/src/TaskList.js` (around line 134)
- **Frontend**: Add helper function to check if task is overdue:
  ```javascript
  const isOverdue = (task) => {
    if (!task.due_date || task.completed) return false;
    const today = new Date().toISOString().split('T')[0];
    return task.due_date < today;
  };
  ```
- **Frontend**: Apply conditional styling to ListItem:
  ```javascript
  sx={{
    borderColor: isOverdue(task) ? 'error.main' : 'existing color',
    background: isOverdue(task) ? 'rgba(244, 67, 54, 0.08)' : 'existing background',
    borderLeft: isOverdue(task) ? '4px solid #f44336' : 'none'
  }}
  ```
- **Frontend**: Optionally add warning icon for overdue tasks
- **Testing**: Test overdue incomplete tasks have red highlighting
- **Testing**: Test completed overdue tasks are not highlighted
- **Testing**: Test highlighting updates when date changes (mock Date)

#### Story: Add color-coded badges for priority levels
**Acceptance Criteria:**
- Each task displays a visual badge indicating priority
- Badge appears consistently across all views
- Badge is visible but not obtrusive
- Badge color corresponds to priority level

**Technical Requirements:**
- **Frontend**: Add priority badge in `packages/frontend/src/TaskList.js` near due date Chip (line 203)
- **Frontend**: Use Material-UI Chip component for priority badge
- **Frontend**: Position badge in the Box alongside existing action buttons
- **Frontend**: Helper function for badge color:
  ```javascript
  const getPriorityColor = (priority) => {
    switch(priority) {
      case 'P1': return '#f44336'; // red
      case 'P2': return '#ff9800'; // orange  
      case 'P3': return '#9e9e9e'; // gray
      default: return '#9e9e9e';
    }
  };
  ```
- **Frontend**: Render badge:
  ```jsx
  <Chip 
    label={task.priority}
    size="small"
    sx={{ background: getPriorityColor(task.priority), color: 'white' }}
  />
  ```
- **Testing**: Test badge renders for all priority levels
- **Testing**: Test correct colors for P1, P2, P3
- **Testing**: Verify badge appears in all filter views

#### Story: Display P1 tasks with red badge
**Acceptance Criteria:**
- P1 priority tasks show red badge
- Red badge is visually distinct from other priorities
- Badge includes "P1" text or identifier
- Color is consistent with red = high priority convention

**Technical Requirements:**
- **Frontend**: Implemented as part of "Add color-coded badges" story
- **Frontend**: Ensure getPriorityColor('P1') returns '#f44336' (Material-UI red[500])
- **Frontend**: Chip component displays 'P1' text in white color for contrast
- **Frontend**: Consider adding priority icon: `startIcon={<PriorityHighIcon />}`
- **Testing**: Test P1 tasks display red badge
- **Testing**: Test badge color matches Material-UI error/red theme color
- **Testing**: Verify text "P1" is visible on badge
- **Testing**: Test color contrast is accessible (WCAG AA)

#### Story: Display P2 tasks with orange badge
**Acceptance Criteria:**
- P2 priority tasks show orange badge
- Orange badge is visually distinct from P1 and P3
- Badge includes "P2" text or identifier
- Color indicates medium priority level

**Technical Requirements:**
- **Frontend**: Implemented as part of "Add color-coded badges" story
- **Frontend**: Ensure getPriorityColor('P2') returns '#ff9800' (Material-UI orange[500])
- **Frontend**: Use same Chip component structure as P1
- **Frontend**: Maintain consistent sizing and positioning with P1/P3 badges
- **Testing**: Test P2 tasks display orange badge
- **Testing**: Test badge color is visually distinct from red (P1) and gray (P3)
- **Testing**: Verify text "P2" is visible on badge
- **Testing**: Test color provides clear visual hierarchy

#### Story: Display P3 tasks with gray badge
**Acceptance Criteria:**
- P3 priority tasks show gray badge
- Gray badge is visually distinct from P1 and P2
- Badge includes "P3" text or identifier
- Color indicates lower priority level

**Technical Requirements:**
- **Frontend**: Implemented as part of "Add color-coded badges" story
- **Frontend**: Ensure getPriorityColor('P3') returns '#9e9e9e' (Material-UI gray[500])
- **Frontend**: Use same Chip component structure as P1/P2
- **Frontend**: Consider slightly different styling to indicate lower urgency
- **Testing**: Test P3 tasks display gray badge
- **Testing**: Test badge color is subdued compared to P1/P2
- **Testing**: Verify text "P3" is visible on badge
- **Testing**: Test default tasks (without explicit priority) display P3 gray badge

---

### Epic: Task Sorting

#### Story: Sort overdue tasks to top of list
**Acceptance Criteria:**
- All overdue tasks appear before non-overdue tasks
- Overdue determination based on dueDate < current date
- Only incomplete tasks are considered for overdue sorting
- Order is maintained when filters are applied

**Technical Requirements:**
- **Frontend**: Modify TaskList component in `packages/frontend/src/TaskList.js`
- **Frontend**: Add sorting logic after filtering, before rendering
- **Frontend**: Create isOverdue helper (if not already exists from highlighting story)
- **Frontend**: Sort tasks array:
  ```javascript
  const sortedTasks = filteredTasks.sort((a, b) => {
    const aOverdue = isOverdue(a);
    const bOverdue = isOverdue(b);
    if (aOverdue && !bOverdue) return -1;
    if (!aOverdue && bOverdue) return 1;
    // Continue with other sort criteria...
  });
  ```
- **Backend**: Optionally add sorting to GET /api/tasks endpoint for performance
- **Testing**: Test overdue tasks appear before non-overdue tasks
- **Testing**: Test completed tasks not considered overdue
- **Testing**: Test sorting persists across filter changes

#### Story: Sort tasks by priority within each group
**Acceptance Criteria:**
- Within overdue group, P1 appears before P2, P2 before P3
- Within non-overdue group, P1 appears before P2, P2 before P3
- Priority sorting is consistent across all filter views
- Ties in priority are broken by next sort criterion

**Technical Requirements:**
- **Frontend**: Extend sorting logic in `packages/frontend/src/TaskList.js`
- **Frontend**: Define priority order mapping:
  ```javascript
  const priorityOrder = { 'P1': 1, 'P2': 2, 'P3': 3 };
  ```
- **Frontend**: Add priority comparison after overdue comparison:
  ```javascript
  const sortedTasks = filteredTasks.sort((a, b) => {
    // Overdue comparison first (from previous story)
    if (aOverdue !== bOverdue) return /* overdue logic */;
    
    // Priority comparison
    const aPriority = priorityOrder[a.priority] || 3;
    const bPriority = priorityOrder[b.priority] || 3;
    if (aPriority !== bPriority) return aPriority - bPriority;
    
    // Continue to date sorting...
  });
  ```
- **Testing**: Test P1 tasks appear before P2 within overdue group
- **Testing**: Test P2 tasks appear before P3 within overdue group
- **Testing**: Test same priority order applies to non-overdue tasks
- **Testing**: Test tasks without priority default to P3 position

#### Story: Sort tasks by due date in ascending order
**Acceptance Criteria:**
- Within same priority level, earlier dates appear first
- Sorting is chronological (soonest first)
- Applies to both overdue and upcoming tasks
- Tasks with same date and priority maintain stable order

**Technical Requirements:**
- **Frontend**: Extend sorting logic in `packages/frontend/src/TaskList.js`
- **Frontend**: Add date comparison after priority comparison:
  ```javascript
  const sortedTasks = filteredTasks.sort((a, b) => {
    // Overdue comparison (from story 1)
    // Priority comparison (from story 2)
    
    // Date comparison
    if (a.due_date && b.due_date) {
      return a.due_date.localeCompare(b.due_date); // Ascending order
    }
    if (a.due_date && !b.due_date) return -1; // Dated before undated
    if (!a.due_date && b.due_date) return 1;
    
    return 0; // Maintain stable order
  });
  ```
- **Frontend**: ISO date format allows string comparison
- **Testing**: Test earlier dates appear before later dates
- **Testing**: Test sorting within same priority level
- **Testing**: Test overdue dates sorted chronologically
- **Testing**: Test future dates sorted chronologically

#### Story: Move tasks without due dates to bottom of list
**Acceptance Criteria:**
- Tasks with null/undefined dueDate appear at end of list
- Undated tasks still sorted by priority among themselves
- Undated tasks appear after all dated tasks
- Behavior consistent across all filter tabs

**Technical Requirements:**
- **Frontend**: Already partially implemented in "Sort by due date" story
- **Frontend**: Ensure undated tasks (no due_date) sort to bottom:
  ```javascript
  // In sort function:
  if (a.due_date && !b.due_date) return -1; // a (dated) comes first
  if (!a.due_date && b.due_date) return 1;  // b (dated) comes first
  // If both undated, continue to priority comparison
  ```
- **Frontend**: Undated tasks should still respect priority order among themselves
- **Frontend**: Final sort order: Overdue dated → Due today dated → Future dated → Undated (by priority)
- **Testing**: Test undated tasks appear after all dated tasks
- **Testing**: Test undated P1 tasks appear before undated P3 tasks
- **Testing**: Test overdue tasks appear before undated tasks regardless of priority

#### Story: Implement comprehensive multi-level sorting algorithm
**Acceptance Criteria:**
- Sort order: Overdue status → Priority → Due date → Undated last
- All four sort levels work together correctly
- Sorting is performant with large task lists
- Sort algorithm is testable and maintainable
- Sorted order updates dynamically when task properties change

**Technical Requirements:**
- **Frontend**: Consolidate all sorting logic from previous stories into single function in `packages/frontend/src/TaskList.js`
- **Frontend**: Create dedicated sorting utility function:
  ```javascript
  const sortTasks = (tasks) => {
    return [...tasks].sort((a, b) => {
      // 1. Overdue status (overdue tasks first)
      const aOverdue = isOverdue(a);
      const bOverdue = isOverdue(b);
      if (aOverdue !== bOverdue) return aOverdue ? -1 : 1;
      
      // 2. Priority (P1 > P2 > P3)
      const priorityOrder = { 'P1': 1, 'P2': 2, 'P3': 3 };
      const aPriority = priorityOrder[a.priority] || 3;
      const bPriority = priorityOrder[b.priority] || 3;
      if (aPriority !== bPriority) return aPriority - bPriority;
      
      // 3. Due date (earlier first, undated last)
      if (a.due_date && b.due_date) {
        return a.due_date.localeCompare(b.due_date);
      }
      if (a.due_date && !b.due_date) return -1;
      if (!a.due_date && b.due_date) return 1;
      
      // 4. Maintain stable order for identical criteria
      return 0;
    });
  };
  ```
- **Frontend**: Apply sorting after filtering: `const displayTasks = sortTasks(filteredTasks)`
- **Frontend**: Consider memoization with useMemo for performance
- **Testing**: Create comprehensive test suite with multiple scenarios
- **Testing**: Test all four sort levels interact correctly
- **Testing**: Test performance with 100+ tasks
- **Testing**: Test edge cases (all same priority, all undated, etc.)
- **Testing**: Test sorting updates when task properties change
- **Testing**: Integration test covering all filter + sort combinations
