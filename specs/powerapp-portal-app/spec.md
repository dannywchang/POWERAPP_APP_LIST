# Feature Specification: PowerApp Portal App

**Feature Branch**: `powerapp-portal-app`

**Created**: 2026-06-29

**Status**: Implemented

**Input**: Power Apps Canvas App that serves as a centralized application portal with approval workflow capabilities — allowing users to browse/launch apps and approvers to manage pending approval tasks.

## User Scenarios & Testing

### User Story 1 - Browse and Search Applications (Priority: P1)

As a user, I want to browse all available applications in the portal and search by title, description, or owner so that I can quickly find and launch the app I need.

**Why this priority**: This is the primary landing function — without it users cannot discover or access applications.

**Independent Test**: Open the app, verify all apps from SPSH_APPsList display as cards with correct info; use search to filter results.

**Acceptance Scenarios**:

1. **Given** I open the portal, **When** the APP_LIST screen loads, **Then** I see a responsive gallery of application cards showing title, description, category badge, owner, and last update date.
2. **Given** I type in the search bar, **When** I enter a keyword, **Then** the gallery filters in real-time to matching apps.
3. **Given** I tap the "Open" button on an app card, **When** the action triggers, **Then** the app URL launches in a new browser tab.

---

### User Story 2 - Filter Applications by Department (Priority: P2)

As a user, I want to filter the app catalog by department so that I can narrow down apps relevant to my team.

**Why this priority**: Improves discoverability for large catalogs.

**Independent Test**: Tap the hamburger menu, select a department, verify only matching apps are shown.

**Acceptance Scenarios**:

1. **Given** I tap the hamburger icon, **When** the dropdown opens, **Then** I see a list of distinct departments plus "ALL".
2. **Given** I select a department, **When** the filter applies, **Then** only matching department apps are displayed.

---

### User Story 3 - View Pending Approval Tasks (Priority: P1)

As an approver, I want to see all my pending approval tasks on a dedicated screen so that I know what requires my attention.

**Why this priority**: Core approval workflow functionality.

**Independent Test**: Navigate to TASK_LIST screen, verify pending tasks from CL_ApprovalTasks assigned to current user display with status badges.

**Acceptance Scenarios**:

1. **Given** I navigate to the task list screen, **When** it loads, **Then** I see a summary count and task cards with title, status badge, applicant, date.
2. **Given** a task status is "Not Started", **When** it renders, **Then** the badge is blue; "In Progress" renders amber.

---

### User Story 4 - Approve or Reject a Task (Priority: P1)

As an approver, I want to select a pending task, review its details, and submit an approve/reject decision with a comment so that the workflow can proceed.

**Why this priority**: This completes the approval workflow loop.

**Independent Test**: Select a task from pending list, choose Approve/Reject, add comment, submit, verify success notification and task removal from pending list.

**Acceptance Scenarios**:

1. **Given** I am viewing pending tasks, **When** I tap "Open Application", **Then** I see task details and a decision form.
2. **Given** I select "Approve" or "Reject", **When** I tap Submit, **Then** a loading spinner appears and the button is disabled.
3. **Given** I reject without a comment, **When** I tap Submit, **Then** a validation error "Comment is required" appears.
4. **Given** submission succeeds, **When** it completes, **Then** a green success banner shows and the task list refreshes.

### Edge Cases

- What if SPSH_APPsList is empty? Gallery shows empty state.
- What if an app has no title/description? Coalesce fallbacks handle missing data.
- What if a task has no title/applicant? Coalesce handles null gracefully.
- What if a task's Approver.Claims has mixed case? Lower() + in handles case-insensitive matching.
- What if the user double-taps Submit? varIsSubmitting disables the button during submission.

## Requirements

### Functional Requirements

- **FR-001**: APP_LIST MUST display apps from SPSH_APPsList with responsive multi-column gallery (1/2/3 columns)
- **FR-002**: APP_LIST MUST support free-text search across Title, Description, and 'Modified By'.DisplayName
- **FR-003**: APP_LIST MUST support department filtering via a category dropdown
- **FR-004**: APP_LIST MUST launch app URLs in a new browser tab via Launch()
- **FR-005**: TASK_LIST MUST display pending tasks from CL_ApprovalTasks assigned to current user
- **FR-006**: TASK_LIST MUST use case-insensitive matching of User().Email against Approver.Claims
- **FR-007**: TASK_LIST MUST exclude completed tasks (TaskStatus = "Completed")
- **FR-008**: TASK_LIST MUST show color-coded status badges (blue/amber/gray)
- **FR-009**: APPROVER_DECISION MUST show task details and an approve/reject decision form
- **FR-010**: APPROVER_DECISION MUST validate that rejection includes a comment
- **FR-011**: APPROVER_DECISION MUST disable submit and show spinner during submission
- **FR-012**: APPROVER_DECISION MUST refresh tasks and show success after submission
- **FR-013**: All screens MUST support English and Traditional Chinese (Taiwan) localization

### Key Entities

- **SPSH_APPsList**: Dataverse table storing application catalog (title, description, department, weblink, owner, modified date)
- **CL_ApprovalTasks**: Dataverse table storing approval tasks (title, applicant, approver claims, task status, created date)
- **CL_Departments**: Local collection of distinct departments for category filter
- **varLang**: Language toggle (EN/ZH)
- **varCategory**: Active department filter
- **varSelectedTask**: Currently selected task record for decision
- **varDecision/varComment/varIsSubmitting/varSubmitError/varSuccessVisible**: Decision form state variables

## Success Criteria

### Measurable Outcomes

- **SC-001**: All SPSH_APPsList records display on APP_LIST on load
- **SC-002**: Search filters results in real-time as user types
- **SC-003**: Category dropdown opens/closes and filters correctly
- **SC-004**: TASK_LIST shows only non-completed tasks for the current user
- **SC-005**: Task status badges display correct colors
- **SC-006**: Approve/Reject decision submits successfully with loading state and success notification
- **SC-007**: Rejection without comment shows validation error
- **SC-008**: Task list refreshes after decision submission
- **SC-009**: Zero compile errors across all screens
- **SC-010**: All UI text displays correctly in both EN and ZH

## Assumptions

- SPSH_APPsList contains all necessary app catalog data
- CL_ApprovalTasks has TaskStatus option set with "Not Started", "In Progress", "Completed" values
- Approver lookup has a Claims property containing email claims
- Dataverse default 500-record row limit is acceptable
- Users have permission to read SPSH_APPsList and CL_ApprovalTables
