# Requirements Document

## Introduction

The Todo List Life Dashboard is a client-side web application that combines a personal productivity dashboard with task management. The dashboard presents the current time and a time-based greeting, a 25-minute focus timer, a to-do list with full editing capability, and a set of customizable quick links to favorite websites. All user data persists in the browser's Local Storage so that tasks and links remain available across sessions. The application is built with HTML, CSS, and vanilla JavaScript, requires no backend server, and runs in modern browsers either as a standalone web app or as a browser extension.

## Glossary

- **Dashboard**: The complete Todo List Life Dashboard web application that presents the greeting, focus timer, to-do list, and quick links on a single page.
- **Greeting_Module**: The component that displays the current time, the current date, and a greeting message selected by the time of day.
- **Focus_Timer**: The component that counts down from 25 minutes and supports start, stop, and reset controls.
- **Todo_List**: The component that manages tasks, including adding, editing, completing, and deleting tasks.
- **Task**: A single to-do item consisting of descriptive text and a completion state.
- **Quick_Links**: The component that displays buttons that open user-defined favorite websites.
- **Quick_Link**: A single saved entry consisting of a display label and a destination URL.
- **Local_Storage**: The browser Local Storage API used to persist to-do tasks and quick links on the client device.
- **Time_Of_Day**: The current local clock hour used to select the greeting message, divided into morning, afternoon, and evening ranges.

## Requirements

### Requirement 1: Display Time, Date, and Greeting

**User Story:** As a user, I want to see the current time, date, and a greeting when I open the dashboard, so that I feel welcomed and oriented.

#### Acceptance Criteria

1. WHEN the Dashboard loads, THE Greeting_Module SHALL display the current local time in hours, minutes, and seconds within 1 second of load completion.
2. WHEN the Dashboard loads, THE Greeting_Module SHALL display the current local date including day, month, and year within 1 second of load completion.
3. WHILE the Dashboard is open, THE Greeting_Module SHALL update the displayed time once every 1 second with a maximum deviation of 1 second from the actual local time.
4. WHERE the Time_Of_Day is between 05:00:00 and 11:59:59 inclusive, THE Greeting_Module SHALL display a morning greeting message.
5. WHERE the Time_Of_Day is between 12:00:00 and 17:59:59 inclusive, THE Greeting_Module SHALL display an afternoon greeting message.
6. WHERE the Time_Of_Day is between 18:00:00 and 23:59:59 inclusive, or between 00:00:00 and 04:59:59 inclusive, THE Greeting_Module SHALL display an evening greeting message.
7. IF the current local time or date cannot be retrieved, THEN THE Greeting_Module SHALL display an error indication informing the user that the time or date is unavailable.

### Requirement 2: Focus Timer Countdown

**User Story:** As a user, I want a 25-minute focus timer with start, stop, and reset controls, so that I can time focused work sessions.

#### Acceptance Criteria

1. WHEN the Dashboard loads, THE Focus_Timer SHALL enter the stopped state and display a remaining time of 25 minutes and 0 seconds formatted as two-digit minutes and two-digit seconds separated by a colon (MM:SS).
2. WHILE the Focus_Timer is in the stopped state, WHEN the user activates the start control, THE Focus_Timer SHALL enter the running state and decrease the remaining time by one second once every one second.
3. WHILE the Focus_Timer is in the running state, IF the user activates the start control, THEN THE Focus_Timer SHALL retain the current remaining time without resetting it and continue counting down uninterrupted.
4. WHEN the user activates the stop control, THE Focus_Timer SHALL pause the countdown and retain the current remaining time until the user next activates the start control or the reset control.
5. WHEN the user activates the reset control, THE Focus_Timer SHALL enter the stopped state and set the remaining time to 25 minutes and 0 seconds.
6. WHEN the remaining time reaches 0 minutes and 0 seconds, THE Focus_Timer SHALL stop the countdown and display a visible indication that the focus session has ended.
7. WHILE the Focus_Timer is counting down, THE Focus_Timer SHALL update the displayed remaining time once per second in two-digit minutes and two-digit seconds separated by a colon (MM:SS).

### Requirement 3: Add Tasks

**User Story:** As a user, I want to add tasks to my to-do list, so that I can track work I need to complete.

#### Acceptance Criteria

1. WHEN the user submits a task with text containing 1 to 500 characters after leading and trailing whitespace is removed, THE Todo_List SHALL add the task to the displayed list with a completion state of not done.
2. IF the user submits a task whose text is empty or contains only whitespace after leading and trailing whitespace is removed, THEN THE Todo_List SHALL reject the submission, leave the displayed list unchanged, and display an error indication that task text is required.
3. IF the user submits a task with text exceeding 500 characters after leading and trailing whitespace is removed, THEN THE Todo_List SHALL reject the submission, leave the displayed list unchanged, and display an error indication that the maximum task length has been exceeded.
4. WHEN a task is added, THE Todo_List SHALL save the updated list of tasks to Local_Storage within 1 second.
5. IF saving the updated list of tasks to Local_Storage fails, THEN THE Todo_List SHALL retain the added task in the displayed list and display an error indication that the task could not be saved.

### Requirement 4: Edit Tasks

**User Story:** As a user, I want to edit an existing task, so that I can correct or update its text.

#### Acceptance Criteria

1. WHEN the user confirms an edit to a task with text of 1 to 500 characters after leading and trailing whitespace is trimmed, THE Todo_List SHALL replace the task text with the trimmed new text.
2. IF the user confirms an edit with text that is empty or contains only whitespace after trimming, THEN THE Todo_List SHALL reject the edit, retain the original task text, and display an error message indicating that the task text cannot be empty.
3. IF the user confirms an edit with text exceeding 500 characters after trimming, THEN THE Todo_List SHALL reject the edit, retain the original task text, and display an error message indicating the maximum character limit.
4. WHEN a task edit is accepted, THE Todo_List SHALL save the updated list of tasks to Local_Storage within 1 second.
5. IF saving the updated list of tasks to Local_Storage fails, THEN THE Todo_List SHALL retain the updated task text in the displayed list and display an error message indicating that changes could not be saved.

### Requirement 5: Mark Tasks as Done

**User Story:** As a user, I want to mark a task as done, so that I can distinguish completed work from remaining work.

#### Acceptance Criteria

1. WHEN the user marks a task as done, THE Todo_List SHALL set the completion state of that task to done and SHALL visually distinguish the task as completed.
2. WHEN the user marks a done task as not done, THE Todo_List SHALL set the completion state of that task to not done and SHALL remove the completed visual distinction.
3. WHEN the completion state of a task changes, THE Todo_List SHALL save the updated list of tasks to Local_Storage within 1 second of the state change.
4. IF saving the updated list of tasks to Local_Storage fails, THEN THE Todo_List SHALL retain the previous completion state of the task and SHALL display an error message indicating that the change could not be saved.

### Requirement 6: Delete Tasks

**User Story:** As a user, I want to delete a task, so that I can remove items I no longer need.

#### Acceptance Criteria

1. WHEN the user selects the delete control for a task, THE Todo_List SHALL remove that task from the displayed list within 1 second.
2. WHEN a task is deleted, THE Todo_List SHALL save the updated list of remaining tasks to Local_Storage within 1 second.
3. IF the deletion cannot be saved to Local_Storage, THEN THE Todo_List SHALL retain the task in the displayed list and present an error indication to the user that the deletion did not persist.
4. WHEN the last remaining task is deleted, THE Todo_List SHALL display an empty-state indication showing zero tasks.

### Requirement 7: Persist and Restore Tasks

**User Story:** As a user, I want my tasks to persist across sessions, so that I do not lose my to-do list when I close the browser.

#### Acceptance Criteria

1. WHEN the Dashboard loads, THE Todo_List SHALL read the saved list of tasks from Local_Storage and display each saved task with its stored text and completion state in the same order in which the tasks were saved.
2. WHEN a task is added, edited, deleted, or has its completion state changed, THE Todo_List SHALL write the complete current list of tasks to Local_Storage within 1 second of the change.
3. IF no saved tasks exist in Local_Storage, THEN THE Todo_List SHALL display an empty list containing zero task items.
4. IF the saved task data in Local_Storage cannot be read as valid task data, THEN THE Todo_List SHALL display an empty list containing zero task items and SHALL overwrite the invalid data with an empty list on the next write operation.

### Requirement 8: Manage Quick Links

**User Story:** As a user, I want to add and remove quick links to my favorite websites, so that I can open them directly from the dashboard.

#### Acceptance Criteria

1. WHEN the user submits a Quick_Link with a label between 1 and 50 characters and a URL between 1 and 2048 characters that begins with "http://" or "https://", THE Quick_Links SHALL add a button displaying the label to the dashboard and persist the Quick_Link across page reloads.
2. IF the user submits a Quick_Link with an empty label, a label exceeding 50 characters, an empty URL, or a URL that does not begin with "http://" or "https://", THEN THE Quick_Links SHALL reject the submission, leave the set of quick links unchanged, and display an error message indicating which field is invalid.
3. WHEN the user activates a Quick_Link button, THE Quick_Links SHALL open the destination URL of the Quick_Link in a new browser tab.
4. WHEN the user deletes a Quick_Link, THE Quick_Links SHALL remove the corresponding button from the dashboard and persist the removal across page reloads.
5. WHILE no quick links exist, THE Quick_Links SHALL display an empty-state indicator in place of Quick_Link buttons.

### Requirement 9: Persist and Restore Quick Links

**User Story:** As a user, I want my quick links to persist across sessions, so that I do not have to re-enter my favorite websites.

#### Acceptance Criteria

1. WHEN a Quick_Link is added or deleted, THE Quick_Links SHALL save the complete current set of quick links to Local_Storage within 1 second of the change.
2. IF saving the updated set of quick links to Local_Storage fails, THEN THE Quick_Links SHALL retain the in-memory set of quick links unchanged and display an error indication informing the user that the change was not saved.
3. WHEN the Dashboard loads, THE Quick_Links SHALL read the saved set of quick links from Local_Storage and display one button for each saved Quick_Link, up to a maximum of 50 quick links.
4. IF no saved quick links exist in Local_Storage, THEN THE Quick_Links SHALL display zero quick link buttons.
5. IF the saved quick link data in Local_Storage cannot be parsed as valid quick link data, THEN THE Quick_Links SHALL display zero quick link buttons and preserve the unreadable stored data without overwriting it until the next add or delete action.
