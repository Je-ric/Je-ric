# Planning, Conditions, and System Flows

# System Planning & Flow

## Foundation / General Principles

- Make it as simple as possible.
- Know the problem first before writing any code.
- For substantial features, document conditions, sequence, and approach before coding. Small fixes need a concise approach and affected documentation updates, not a separate planning document.
- User-friendly — design for the least technical user (idiot proof).

## Planning Reminders

- List all possible considerations and factors before starting (incidents, edge cases, tools, roles).
- Define who can do what before building any feature.
- Identify what needs to be logged, notified, and validated upfront.
- Sketch the system flow: login → dashboard → action → audit log → notification.

## New Feature Planning Template

Before building any feature, answer these:

1. Who can access this feature? (roles)
2. What are the conditions to use it? (prerequisites)
3. What does it create/update/delete in the DB?
4. What needs to be validated?
5. What needs to be logged?
6. Who gets notified?
7. What happens on failure?
8. What does the user see on success?

---

## Feature Planning Options (Use When Required)

- Index — default landing page
- Search
- Filter / Sort
- Pagination
- Auto-refresh (AJAX / Livewire / polling)
- Session checks — always verify who is logged in
- Logged-in user identifier visible in UI
- Notifications (in-app + email)
- Appropriate action feedback; use the decision table in `10_Frontend_UX_and_Layout_Standards.md`.
- Confirm destructive or consequential actions; see `04_Input_Validation_and_Form_Submission.md`.
- Bulk import / upload to avoid manual data entry

---

## Backend Logs & Audit Records

- Do not log every response, UI click, successful read, or ordinary validation failure. Choose logging independently of whether the user sees a toast or alert.
- **Diagnostic logs** explain operational problems. Report unexpected exceptions and failed dependencies once, using the framework exception handler or the responsible catch boundary. Use `error` for failed operations, `warning` for recoverable degradation or suspicious conditions, `info` for meaningful operational milestones, and `debug` for targeted diagnostics.
- **Audit records** track accountable events: login/logout, failed authentication, and meaningful create/update/delete/archive or approval/permission changes. Record actor (if known), action, target, time, outcome, and safe request context using the existing audit mechanism. Do not create success audit entries for rolled-back changes; distinguish failed attempts from completed actions.
- Routine validation, expected missing records, and ordinary authorization denials generally need no diagnostic error entry. Security-relevant attempts may require security/audit records; aggregate or rate-limit repetitive events where appropriate.
- Include useful safe context such as operation, record ID, and correlation ID. Never dump full requests, secrets, tokens, or sensitive personal data; follow `03_Security_Guidelines.md`.
- A diagnostic entry and an audit record may both be justified because they answer different questions. Avoid duplicate exception reports across controllers, services, and framework handlers.
- Background jobs and APIs need appropriate status/results and logging, not UI toasts. Alert operators only for actionable operational conditions through the configured monitoring policy; do not send an alert for every log entry.

---

## Standard Web App Flow

```
User visits site
  └── Public pages (no auth required)
  └── Protected pages → redirect to login if not authenticated

Login
  └── Validate credentials
  └── Check account status (active / pending / disabled / rejected)
  └── Start session
  └── Redirect to dashboard
  └── Audit log: login recorded

Dashboard
  └── Show summary / stats relevant to user role
  └── Show notifications

CRUD Action (Create / Update / Delete / Archive)
  └── User fills form
  └── Confirmation when required → user confirms
  └── Client-side validation (immediate feedback)
  └── Authenticate and authorize the specific action/record
  └── Server-side validation (always)
  └── Check current state and concurrency guarantees
  └── DB transaction begins when atomic writes are required
      └── Perform DB operation(s)
      └── Commit on success / Rollback on failure
  └── Required audit data persisted atomically, or delivered after commit with defined recovery
  └── Notification dispatched after commit (if applicable)
  └── Email dispatched after commit (if applicable)
  └── Appropriate feedback shown (toast, inline errors, or persistent alert)
  └── UI updated (redirect or refresh)

Logout
  └── Confirmation modal
  └── Session destroyed
  └── Audit log: logout recorded
  └── Redirect to login
```

---

## Multi-Stage Approval Workflow Pattern

Use this shape for any feature where a record must pass through sequential reviewers before final approval (e.g. submission → peer review → supervisor review → final sign-off):

```
Record submitted
  └── Status: pending_review (stage 1)
  └── Assigned reviewer(s) for stage 1 notified

Each stage
  └── Reviewer views record + any prior stage recommendations
  └── Reviewer records: decision (approve / reject / request changes), timestamp, optional notes
  └── Audit log recorded
  └── On approve → advance to next stage, notify next reviewer(s)
  └── On reject / request changes → return to submitter, notify submitter, status reset accordingly
  └── On final stage approve → status: approved, notify submitter

Design notes
  └── Store each stage's decision as its own row (reviewer, role, timestamp, recommendation) — never overwrite a prior stage's decision
  └── Track current stage explicitly on the record (don't infer it from decision rows alone)
  └── Checklist-style review criteria, if any, are their own table keyed to the review stage
```

---

## Role-Based Access Flow

```
Request comes in
  └── Auth middleware → is user logged in?
      └── No → redirect to login
      └── Yes → role middleware → does user have required role?
          └── No → 403 Forbidden
          └── Yes → authorize record/action scope → proceed if allowed
```

---

## File Upload Flow

```
User selects file
  └── Client-side: check type and size before upload
  └── Server-side validation:
      └── Allowed MIME types
      └── Max file size
      └── Malicious content check
  └── Rename file (uuid or timestamp prefix)
  └── Store in correct storage directory
  └── Save path to database
  └── Return success + file URL
```

---

## Notification Flow

```
Action occurs (e.g., task assigned, status updated)
  └── Event dispatched after a successful commit
  └── Listener handles event
      └── Create in-app notification record
      └── Send email notification (queued)
  └── User sees notification badge
  └── User clicks → redirected to relevant page
```

---

## Standard API Request Flow

```
Client sends request
  └── Auth check (Sanctum / session)
  └── Authorize the specific action/record
  └── Request validation (Form Request where appropriate)
  └── Controller handles the action or calls a Service when justified
      └── Service performs business logic
      └── DB transaction if multi-table
  └── Return standard JSON response:
      {
        "success": true/false,
        "message": "...",
        "data": {...} or null,
        "errors": {...} or null
      }
```

(Full JSON response spec in `06_API_Standards.md`.)

---

# Condition Completeness & Error Coverage Guide

Purpose: before any conditional logic, state machine, or data-dependent feature ships, verify every branch is accounted for, no two conditions can silently contradict each other, and every way the data could be missing or wrong has a defined outcome. This is a thinking discipline, not framework-specific — apply it in any language or stack.

---

## 1. Condition Completeness

Every decision point needs a defined outcome for *every* case, not just the ones you thought of first.

- Every decision has a deliberate outcome. Guard clauses and intentional fall-through are valid; do not add empty else branches to satisfy a checklist.
- Handle unexpected values explicitly. Exhaustive enum matches may omit a default so a new unhandled case fails visibly; do not hide invalid states behind a success fallback.
- Use a final `else` or `default` where an otherwise unhandled case requires a response. Guard clauses, intentional fall-through, and exhaustive enum matches do not require artificial fallback branches.
- Every value a field/status/enum can hold has a defined behavior everywhere that field is used — if a new status is added later, grep for every place the old set of statuses was handled and update all of them, not just the one you were working on.
- Boolean logic is checked for the full truth table when more than one flag is involved — don't assume the two flags you tested are the only combination that occurs.
- Loops and recursive logic have a defined behavior for: zero items, one item, many items, and (for recursion) a guaranteed base case.

**Before shipping any conditional logic, ask:** *"If I fed this the one input I haven't thought of yet, what happens?"*

---

## 2. Conflicting / Contradictory Conditions

- Two conditions that are supposed to be mutually exclusive (e.g. a record can't be both `archived` and `active`) are enforced at the data layer (constraint, validation rule), not just assumed true by the UI.
- When two independent rules could both apply to the same case, define which one wins explicitly — don't let it be decided by whichever code path happens to run first.
- Where business rules changed over time, check for now-stale conditions elsewhere in the code that still assume the old rule — a rule change in one place is a search-and-verify task across the codebase, not a one-line edit.
- Time/date-based conditions (deadlines, expiry, "current" period) are checked for boundary conflicts: what happens exactly at the boundary instant, and what happens if the server and client clocks disagree?

---

## 3. All Possibilities of Error

For every action that can fail, walk through this list and decide what happens for each — don't just handle the one failure mode you happened to trigger in testing:

- **Network / connectivity failure** — request never reaches the server, or the response never comes back.
- **Timeout** — the operation is slow, not dead; don't treat it identically to a hard failure.
- **Permission denied** — the user is authenticated but not authorized for this specific action.
- **Not found** — the target record was deleted or never existed.
- **Validation failure** — input didn't meet requirements (see the input validation guide).
- **Conflict / concurrent modification** — someone else changed the record between when it was loaded and when it was saved.
- **Server error** — an unexpected exception on the backend.
- **Rate limit / throttling** — the action was blocked for being too frequent.
- **Partial failure in a batch operation** — 8 of 10 records succeeded, 2 failed; the response must say which is which, not just "some failed."
- **Third-party/external service failure** — the service you depend on is down, slow, or returns something unexpected.

Define a useful response for each applicable failure. Let framework handlers process expected validation/authorization failures; log unexpected failures with safe context. Do not log every ordinary invalid input or report the same exception twice.

---

## 4. Missing / Null / Empty Data

"No data" is not one condition — it's several, and they usually need different handling:

| State | Example | Typical handling |
|---|---|---|
| Field is `null` | Optional field never filled in | Treat as "not provided" — don't crash, don't treat as `0`/`false`/empty string |
| Field is empty string `""` | User submitted a form with a blank text field | Decide explicitly if this counts as "missing" or a legitimate empty value — don't let this be accidental |
| Field is `0` / `false` | A real, valid value that is falsy | Never treat falsy-but-present as if it were missing — a common bug source in loose truthiness checks |
| Array/collection is empty `[]` | No related records exist yet | Distinguish zero results, missing records, and unloaded relationships; check loading state explicitly |
| Related record doesn't exist | Foreign key points to nothing (or is null) | Guard before accessing nested properties; decide what the UI shows instead of the missing data |
| Field is present but the wrong type | API/client sent a string where a number was expected | Reject explicitly with a clear validation error — don't silently coerce and hope |
| Whole request/payload is missing expected keys | Frontend/backend contract drifted | Fail loudly and specifically, not with a generic 500 |

**Rule of thumb:** never assume a value is present, non-null, correctly typed, and non-empty at the same time unless it was just validated. Validate untrusted entry points and enforce domain invariants where state changes. Reuse validated internal contracts rather than repeating identical validation in every method.

---

## 5. Pre-Ship Checklist

Before marking any feature with conditional logic as done:

- [ ] Every conditional branch has a defined outcome — no implicit "falls through and does nothing."
- [ ] Any two conditions that shouldn't both be true are enforced, not assumed.
- [ ] Every failure mode in section 3 has been considered for this specific action, even if some are marked "won't happen because X" — that reasoning is written down, not left implicit.
- [ ] Every input this feature reads has been checked against the missing/null/empty table above.
- [ ] The "one input I haven't thought of yet" question has actually been asked and answered.

Cross-reference: `13_Code_Review_Checklist.md` (§1 Correctness & Business Logic, §3 Error Handling) and `04_Input_Validation_and_Form_Submission.md` for validation-specific detail.
