# Tasks: PowerApp Portal App

**Input**: Design documents from `/specs/powerapp-portal-app/`

**Organization**: Tasks are grouped by screen. All tasks are completed.

## Phase 1: App Foundation

**Purpose**: Project initialization and shared infrastructure

- [x] T001 Scaffold Canvas App project with PowerAppsTheme
- [x] T002 Set up multi-language framework (varLang EN/ZH)
- [x] T003 Add shared color constants and design tokens
- [x] T004 Define base screen template (white fill, blue loading spinner)

### Multi-Language Labels (App.pa.yaml)

- [x] T005 Add AppTitle, SearchHint, LblAll, LblOwner, LblLastUpdate, LblOpenApp
- [x] T006 Add TaskTitle, LblApplicant, LblCreatedDate
- [x] T007 Add LblBack, LblApprovalTitle, LblDecision, LblComment, LblSubmit
- [x] T008 Add LblCommentRequired, LblSuccess, LblOpenApplication
- [x] T009 Add LblSubmitted, LblStatus, LblPending, LblCompleted, LblRejected

---

## Phase 2: APP_LIST — App Catalog Screen

**Purpose**: Main landing screen for browsing and launching applications

### Screen Shell & Header
- [x] T010 Create APP_LIST screen with CON_LIST main container
- [x] T011 Add dark slate CON_HEADER with LBL_PAGE_TITLE and BTN_LANGUAGE

### Toolbar
- [x] T012 Create CON_TOOL_BAR with category filter and search
- [x] T013 Add BTN_HAMBURGER (toggles varCategoryOpen), LBL_SELECTED_CATEGORY
- [x] T014 Add TXT_SEARCH with hint text and responsive width (300px)

### App Gallery
- [x] T015 Add GAL_APP_LIST with responsive WrapCount (1/2/3 columns)
- [x] T016 Wire Items formula: `With(searchTerm + dataSource, Filter(...))` — matches Title, Description, 'Modified By'.DisplayName
- [x] T017 Create app card template (CardBackground, CategoryBadge, AppNameLabel, DescriptionLabel, OwnerCaption/Value, LastUpdateCaption/Value)
- [x] T018 Add OpenAppButton — launches ThisItem.WebLink via Launch()

### Category Dropdown
- [x] T019 Create CON_CATEGORY_DROPDOWN overlay with GAL_DEPT_MENU
- [x] T020 Wire BTN_DEPT_ITEM.OnSelect — Set(varCategory, ThisItem.Value); close dropdown

### Screen Logic
- [x] T021 Wire OnVisible: ClearCollect distinct departments, set defaults

### Validation
- [x] T022 Run compile_canvas — 0 errors

---

## Phase 3: TASK_LIST — Pending Tasks Screen

**Purpose**: Display pending approval tasks for the current user

### Screen Shell & Header
- [x] T023 Create TASK_LIST screen with CON_LIST_1, CON_HEADER_1 (dark slate)
- [x] T024 Add LBL_PAGE_TITLE_1 (TaskTitle) and BTN_LANGUAGE_1

### Task Gallery
- [x] T025 Add LBL_SUMMARY — pending task count
- [x] T026 Add GAL_TASK_LIST_1 with case-insensitive claim filter
- [x] T027 Create task card (TASK_CARD_BG, TASK_TITLE, TASK_STATUS_BADGE, TASK_APPLICANT, TASK_DATE)
- [x] T028 Wire status badge colors: blue (Not Started), amber (In Progress), gray (other)

### Screen Logic
- [x] T029 Wire OnVisible: Set defaults

### Case-Insensitive Update
- [x] T030 Update filter from `=` to `Lower(User().Email) in Lower(Approver.Claims)`

### Validation
- [x] T031 Run compile_canvas — 0 errors

---

## Phase 4: APPROVER_DECISION — Approve/Reject Screen

**Purpose**: Decision form for approve/reject workflow

### Screen Shell & Header
- [x] T032 Create APPROVER_DECISION screen
- [x] T033 Add CON_HEADER with BTN_BACK (set varViewingTask=false), LBL_PAGE_TITLE_2 (LblApprovalTitle), BTN_LANGUAGE_2
- [x] T034 CON_GALLERY: LBL_TASK_SUMMARY (pending count via AllItemsCount), GAL_PENDING_TASKS (filtered tasks)

### Task Detail (CON_DECISION, visible when varViewingTask=true)
- [x] T035 CON_TASK_DETAIL_CARD: LBL_DETAIL_TITLE, APPLICANT, DATE, STATUS
- [x] T036 CON_APPROVAL_FORM: LBL_SECTION_DECISION, RAD_DECISION (Approve/Reject radio with color tinting)
- [x] T037 TXT_COMMENT — multiline input with placeholder, validation state
- [x] T038 LBL_ERROR — conditional red error message
- [x] T039 BTN_SUBMIT — disabled when no decision or submitting; green for Approve, red for Reject
- [x] T040 SPN_LOADING — spinner visible during submission

### Success Notification
- [x] T041 CON_SUCCESS — green banner with LBL_SUCCESS, auto-visible after submit

### Variable Wiring
- [x] T042 Implement varViewingTask, varSelectedTask, varDecision, varComment
- [x] T043 Implement varIsSubmitting, varSubmitError, varSuccessVisible
- [x] T044 Wire BTN_SUBMIT.OnSelect: validate → Patch → refresh → reset → show success
- [x] T045 Handle rejection comment validation

### Fixes & Polish
- [x] T046 Fix SpinnerSize enum: `'Spinner.SpinnerSize'.Medium`
- [x] T047 Fix YAML colon-space parsing issues with `|-` block scalar
- [x] T048 Fix Patch argument issues (unknown column names)
- [x] T049 Replace `CountRows(Gallery.AllItems)` with `Gallery.AllItemsCount`
- [x] T050 Add AccessibleLabel to 8 interactive controls

### Validation
- [x] T051 Run compile_canvas — 0 errors
- [x] T052 Run get_appchecker_errors — 0 medium/high issues
- [x] T053 Run get_accessibility_errors — interactive controls fixed

---

## Phase 5: Documentation

**Purpose**: Create specification documents

- [x] T054 Create `specs/powerapp-portal-app/spec.md`
- [x] T055 Create `specs/powerapp-portal-app/plan.md`
- [x] T056 Create `specs/powerapp-portal-app/tasks.md`
- [x] T057 Remove old per-screen speckit directories
- [x] T058 Update AGENTS.md SPECKIT section to point at unified plan
