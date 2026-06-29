# Implementation Plan: PowerApp Portal App

**Branch**: `powerapp-portal-app` | **Date**: 2026-06-29 | **Spec**: `specs/powerapp-portal-app/spec.md`

## Summary

The PowerApp Portal App is a centralized application portal with built-in approval workflow. It comprises 3 screens: **APP_LIST** (app catalog with search/filter/launch), **TASK_LIST** (pending approval tasks), and **APPROVER_DECISION** (approve/reject decision form). All screens share a consistent dark-slate header, card-based layouts, and multi-language EN/ZH support.

## Technical Context

**Platform**: Microsoft Power Apps Canvas App

**Primary Dependencies**: Classic controls (Label, Button, TextInput, Gallery) + Modern controls (ModernButton, ModernRadio, ModernTextInput, ModernText, Spinner, GroupContainer AutoLayout)

**Storage**: Dataverse — SPSH_APPsList (app catalog), CL_ApprovalTasks (approval tasks)

**Testing**: Power Apps Canvas App validation via `compile_canvas` MCP tool, `get_appchecker_errors`, `get_accessibility_errors`

**Target Platform**: Power Apps player (mobile/web/Teams)

**Project Type**: Canvas App (Power Apps)

**Constraints**: Delegation limits of Dataverse (500-record default); `Lower()` + `in` operator not fully delegable

## Source Code

```text
approval-workflow/
├── App.pa.yaml                  # App-level formulas + multi-language labels (EN/ZH)
├── APP_LIST.pa.yaml             # Screen 1: App catalog gallery (486 lines)
├── TASK_LIST.pa.yaml            # Screen 2: Pending tasks list (205 lines)
└── APPROVER_DECISION.pa.yaml   # Screen 3: Approve/reject form (413 lines)
```

## Screen Architecture

### APP_LIST — App Catalog Browser

```
┌──────────────────────────────────────────────┐
│  App Title                     [ 中文 ]       │ ← Dark slate header
├──────────────────────────────────────────────┤
│  [☰]  ALL Departments         [ Search...  ] │ ← Toolbar
├──────────────────────────────────────────────┤
│  ┌──────────┐  ┌──────────┐  ┌──────────┐    │
│  │ Category │  │ Category │  │ Category │    │ ← Responsive gallery
│  │ Title    │  │ Title    │  │ Title    │    │   (1/2/3 cols)
│  │ Desc...  │  │ Desc...  │  │ Desc...  │    │
│  │ [ Open ] │  │ [ Open ] │  │ [ Open ] │    │
│  └──────────┘  └──────────┘  └──────────┘    │
└──────────────────────────────────────────────┘
```

**Key logic**: `With()` wrapper computes searchTerm + filtered dataSource; `Filter()` matches across Title, Description, 'Modified By'.DisplayName using Lower() + in; responsive WrapCount adapts to screen width.

### TASK_LIST — Pending Approval Tasks

```
┌──────────────────────────────────────┐
│  Task Manager              [ 中文 ]   │ ← Dark slate header
├──────────────────────────────────────┤
│  5 pending task(s)                   │ ← Summary count
├──────────────────────────────────────┤
│  ┌──────────────────────────────────┐│
│  │ Task Title              [Status] ││ ← Pending tasks gallery
│  │ Applicant: Name                  ││
│  │ Created: 2026/06/29             ││
│  └──────────────────────────────────┘│
└──────────────────────────────────────┘
```

**Key logic**: `Filter(CL_ApprovalTasks, Lower(User().Email) in Lower(Approver.Claims) && TaskStatus.Value <> "Completed")` for case-insensitive claim matching.

### APPROVER_DECISION — Approve/Reject Form

```
┌──────────────────────────────────────┐
│ [ ← ]  Approval Title     [ 中文 ]   │ ← Dark slate header
├──────────────────────────────────────┤
│ Pending tasks: N                     │
│ ┌─ Task Card ──────────────────────┐ │
│ │ Title | Applicant | Date | Status│ │ ← Gallery (when no task selected)
│ │ [     Open Application      ]    │ │
│ └──────────────────────────────────┘ │
├─ Decision Form (selected task) ──────┤
│ ┌─ Task Detail ────────────────────┐ │
│ │ Title, Applicant, Date, Status   │ │ ← Detail card
│ └──────────────────────────────────┘ │
│ ┌─ Decision ───────────────────────┐ │
│ │ ○ Approve  ○ Reject             │ │ ← Radio buttons
│ │ Comment: [..................]   │ │ ← Multiline text input
│ │ [Error message]                  │ │ ← Conditional error
│ │ [     Submit     ]  (◌ spinner)  │ │ ← Submit + loading
│ └──────────────────────────────────┘ │
│ ┌─ ✅ Successfully submitted ──────┐ │ ← Green banner
│ └──────────────────────────────────┘ │
└──────────────────────────────────────┘
```

**Key logic**: `varViewingTask` toggles between gallery view (pending list) and detail view (decision form); BTN_OPEN_APP sets `varSelectedTask`; BTN_SUBMIT validates, patches CL_ApprovalTasks, refreshes, and shows success.

## Variable Flow

```
APP_LIST.OnVisible → Set(varCategory, "ALL"), ClearCollect(CL_Departments, ...)
TASK_LIST.OnVisible → Set(varCategory, "ALL")

GAL_PENDING_TASKS.OnSelect (via BTN_OPEN_APP)
  → Set(varSelectedTask, ThisItem)
  → Set(varViewingTask, true)
  → Set(varDecision, "")
  → Set(varComment, "")
  → Set(varSubmitError, "")
  → Set(varSuccessVisible, false)

BTN_SUBMIT.OnSelect
  → If(Reject && blank comment, Set(varSubmitError, LblCommentRequired))
  → Set(varIsSubmitting, true)
  → Patch(CL_ApprovalTasks, varSelectedTask, {TaskStatus: {Value: "Completed"}})
  → Set(varIsSubmitting, false)
  → Set(varSuccessVisible, true)
  → Refresh(CL_ApprovalTasks)
  → Set(varViewingTask, false)
  → Reset all decision variables
```

## Design System

| Element | Value |
|---------|-------|
| Header fill | RGBA(53, 74, 94, 1) — dark slate |
| Page background | RGBA(255, 255, 255, 1) — white |
| Container background | RGBA(245, 246, 247, 1) — light gray |
| Accent color | RGBA(0, 112, 242, 1) — blue |
| Success green | RGBA(0, 140, 80, 1) |
| Error red | RGBA(200, 50, 50, 1) |
| Text dark | RGBA(32, 38, 44, 1) |
| Text secondary | RGBA(88, 96, 105, 1) |
| Text muted | RGBA(96, 103, 112, 1) |
| Typography | Segoe UI |
| Card radius | 12px |
| Card border | RGBA(217, 217, 217, 1), 1px |
| Drop shadow | Regular (on dropdown and success banner) |

## Design Decisions

| Decision | Choice | Rationale |
|----------|--------|-----------|
| Category filter | Hamburger + dropdown overlay | Space-efficient, keeps toolbar clean |
| Single screen for gallery+form | `varViewingTask` toggle | Avoids separate screen navigation; instant transition |
| Case-insensitive filter | `Lower() in Lower()` | Handles mixed-case email claims |
| Rejection comment required | Validation on Submit | Ensures approver provides reason for rejection |
| Responsive gallery | `If(Self.Width < 900, 1, If(...))` | Adapts to mobile/tablet/desktop widths |
| Modern controls (decision screen) | ModernButton, ModernRadio, ModernTextInput, Spinner | Better AutoLayout support, richer built-in styling |
