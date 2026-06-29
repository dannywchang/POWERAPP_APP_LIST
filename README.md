# PowerApp Portal App

A centralized application portal built with Power Apps Canvas App — featuring an app catalog browser with search/filter capabilities and an integrated approval workflow for task management.

## Screens

| Screen | Purpose |
|--------|---------|
| **APP_LIST** | Browse and search the application catalog from SPSH_APPsList. Filter by department, search by title/description/owner, and launch apps. |
| **TASK_LIST** | View pending approval tasks assigned to the current user from CL_ApprovalTasks. Color-coded status badges. |
| **APPROVER_DECISION** | Review task details and submit approve/reject decisions with comments. Includes validation and loading states. |

## Features

- **Multi-language**: English and Traditional Chinese (Taiwan) — toggle via `varLang`
- **Responsive gallery**: 1/2/3 column layout adapts to screen width
- **Case-insensitive matching**: `Lower(User().Email) in Lower(Approver.Claims)` for task assignment
- **Approval workflow**: Patch to Dataverse + refresh + success notification

## Tech Stack

- **Platform**: Microsoft Power Apps Canvas App
- **Data Sources**: SPSH_APPsList (app catalog), CL_ApprovalTasks (approval tasks)
- **Controls**: Classic + Modern controls (GroupContainer AutoLayout, Gallery, ModernButton, ModernRadio, ModernTextInput, Spinner)

## Project Structure

```
approval-workflow/            # App YAML files
├── App.pa.yaml               # App-level formulas + multi-language labels
├── APP_LIST.pa.yaml          # App catalog screen
├── TASK_LIST.pa.yaml         # Pending tasks screen
└── APPROVER_DECISION.pa.yaml # Approve/reject decision screen
specs/powerapp-portal-app/    # Specification documents
├── spec.md                   # Feature specification
├── plan.md                   # Implementation plan
└── tasks.md                  # Completed task list
```

## Development

This project uses the [Canvas Authoring MCP](https://learn.microsoft.com/en-us/power-apps/maker/canvas-apps/coauthoring-mcp) tools for coauthoring:

```powershell
# Connect to the coauthoring session
canvas-authoring_connect(environment_id="...", app_id="...", cluster_category="prod")

# Pull latest state from server
canvas-authoring_sync_canvas(directoryPath="approval-workflow")

# Validate changes
canvas-authoring_compile_canvas(directoryPath="approval-workflow")

# Check quality
canvas-authoring_get_appchecker_errors()

# Check accessibility
canvas-authoring_get_accessibility_errors()
```

## Design System

| Element | Value |
|---------|-------|
| Header fill | `RGBA(53, 74, 94, 1)` |
| Accent | `RGBA(0, 112, 242, 1)` |
| Typography | Segoe UI |
| Card radius | 12px |
| Gallery wrap | 1 col (<900px), 2 col (<1360px), 3 col (>=1360px) |
