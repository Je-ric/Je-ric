# Input Validation and Form Submission Standards

This guide covers both authoritative input validation and the browser form submission lifecycle. Validation applies to every untrusted entry point, including forms, APIs, jobs, commands, and independently callable services. Browser state, modal behavior, and submission controls apply where the feature uses forms.

These are target standards, not claims that every existing form already implements them. Inspect shared components and affected forms before reporting completion. Application JSON envelopes and HTTP codes belong to `06_API_Standards.md`; native Blade and Livewire keep their framework error handling.

## 1. Scope and Trust Boundaries

- **Client-side validation** is for UX only — immediate feedback, fewer round trips. It is never trusted as the source of truth.
- **Server-side validation is always authoritative.** Every request is validated server-side regardless of what the client already checked (see `03_Security_Guidelines.md` — never trust client input).
- Validate untrusted inputs at each entry point, including jobs, commands, and independently callable services. Enforce domain invariants where state changes; share validation rules and validated internal contracts rather than copying the same validator into every layer.

## 2. Input Flexibility and Useful Constraints

- **Postel's Law, bounded by the field contract:** support alternate formats only when their interpretation is explicit and unambiguous. Normalize allowed forms, then validate; reject ambiguous dates, unexpected types, and disallowed values. Do not relax authorization or injection defenses.
- **Parkinson's Law as a scope heuristic:** set length/range limits from actual domain and storage requirements, with matching hints when useful. Do not invent arbitrary limits or timers to force faster completion.

## 3. Field Contracts: What to Validate

For every input field, check all of the following that apply:

- **Presence** — is it required, and is it actually there?
- **Type** — string, number, boolean, date, etc. — matches what's expected.
- **Format** — email, URL, phone, date format, regex pattern.
- **Range / length** — min/max length for strings, min/max value for numbers, min/max date for dates.
- **Allowed values** — must be one of a fixed set (enum/status), not just "any string."
- **Uniqueness** — must not collide with an existing record, where relevant.
- **Referential integrity** — a referenced ID must actually exist and be accessible to this user.
- **Business-rule validity** — passes domain-specific rules beyond raw format (e.g. an end date must be after a start date; a quantity can't exceed available stock).
- **Cross-field consistency** — fields that must agree with each other (password + confirmation, date ranges, dependent dropdowns).

## 4. Null, Missing, and Wrong-Type Input

What the user sees for each case — decide this explicitly per field, don't leave it to default framework behavior:

| Input state | Required field | Optional field |
|---|---|---|
| Missing entirely | "X is required." | Treated as not provided — proceeds normally |
| Present but `null`/empty | "X is required." | Accept null only if nullable; normalize empty strings according to the field contract |
| Wrong type (e.g. text where a number is expected) | "X must be a number." | Same — type errors apply regardless of required/optional |
| Out of allowed range/length | "X must be between A and B." | Same |
| References something that doesn't exist (invalid foreign key/ID) | "Selected X does not exist." | Same, if a value was provided at all |
| Fails a cross-field rule | Message names both fields ("End date must be after start date.") | Same |

## 5. Validation Placement and Laravel Rule Reference

Prefer Form Requests for substantial or reused HTTP validation. Simple controller validation is acceptable; Livewire uses component validation. Share domain rules without forcing HTTP requests into components or services.

```
required         — field must be present and not empty
nullable         — field can be null
string           — must be a string
integer          — must be an integer
numeric          — must be a number
boolean          — must be true/false
email            — must be valid email format
max:255          — max character length
min:1            — min value or length
in:active,inactive — must be one of listed values
exists:table,col — must exist in DB
unique:table,col — must be unique in DB
mimes:jpg,png    — allowed file types
max:2048         — max file size in KB
```

## 6. Validation Errors and Response Contracts

For application JSON APIs, use the response envelope and example in `06_API_Standards.md`. Native Blade forms use redirects/error bags and Livewire uses its validation handling; preserve those response formats.

- Errors are keyed by field name so the frontend can map each message to its input.
- Messages are specific enough to act on ("Email must be a valid address") — never a bare "Invalid input."
- Messages never leak internal details (stack traces, table/column names, query fragments).
- A field can have more than one error; return all of them at once, not just the first one found — nothing is more frustrating than fixing one error only to be told about the next.

## 7. Frontend Validation Display

- **Inline, per-field errors** appear directly next to (or under) the field they belong to — never only in a toast or a top-of-page summary alone.
- **Timing:** validate on blur/change for immediate feedback where cheap to do so; always re-validate on submit regardless of earlier checks.
- **Submit button** is disabled (or clearly blocked) while the form is in an invalid state, but don't hide *why* — still show the errors, don't just silently disable.
- **Loading vs error vs empty are three different states** — a failed validation is not the same as "still loading" or "no data"; each needs its own visual treatment.
- **Summary/Toasts/alert** may explain that submission failed when inline errors alone are easy to miss, especially in long forms. A toast is optional, never a replacement for field errors; avoid duplicate feedback. See `10_Frontend_UX_and_Layout_Standards.md` for when to use each feedback type.

## 8. Form State, Payload, and Submission

- Use one submission state per form. Prevent repeated submits while pending and show a clear loading label.
- Keep Add/Assign prerequisites and Update change detection explicit. Explain why a submit button is disabled. Preserve `old()` values and field errors after validation redirects.
- Serialize PHP values using `@js()`; normalize ID types consistently before comparisons. Compare only meaningful editable fields using their field-specific normalization rules.
- Disabled native controls are omitted from browser form submissions. Use readonly text fields or synchronized hidden mirrors where needed; avoid duplicate successful controls with the same scalar name.
- Hidden inputs and UI locks are untrusted. Validate and authorize their values on the server.
- Use verified named routes, `@csrf` for state-changing native forms, and `@method` inside the form when needed.
- Alpine handlers must call `$event.preventDefault()` when blocking submission; do not rely on `return false`.

## 9. Modal Behavior, Feedback, and Request Recovery

- Use unique modal IDs consistently for the dialog, header, controls, and opener.
- Confirm destructive operations with a clear description of their effect. Ordinary Add/Update forms need no second confirmation unless the workflow requires one.
- If dismissal is locked while a request is pending, guard close buttons, backdrop clicks, and the native dialog `cancel` event conditionally. Allow Escape/cancel when idle.
- Restore usable controls on validation, authorization, network, and server failures. For asynchronous requests, reset pending state in a completion/finally path; preserve unsaved data.
- A timeout can occur after the server committed. Do not claim nothing was saved or blindly retry a non-idempotent action.
- Show inline field errors, success feedback, and an actionable general error when appropriate. Keep loading, validation, and empty states distinct.
- Preserve focus, keyboard access, and accessible labels. Use shared button/loading components and explicit labels such as Saving, Assigning, and Archiving.
- Match client length hints to server limits. Add unsaved-change warnings only when useful, remove listeners on teardown, and avoid warnings after successful saves.

## 10. Duplicate Requests, Concurrency, and Idempotency

- Enforce business uniqueness with database constraints; a preliminary existence check is insufficient under concurrency.
- Use transactions and an appropriate lock or conditional update for competing state changes.
- For non-idempotent side effects that may be retried, design a server-side idempotency key scoped to actor/action, with atomic storage, payload matching, and a defined replay result.
- Throttling limits request volume; it does not replace uniqueness or idempotency. Client confirmation flags are UX state only.


## 11. Progress, Drafts, and Continuity

- **Goal-gradient effect:** when a form is genuinely multi-step, show truthful progress and enough prior-step context to continue. Do not fabricate completion percentages.
- **Zeigarnik effect:** preserve recoverable drafts and show a clear resume path where the workflow supports drafts; do not add artificial unfinished work to pressure users.
- **Peak-end rule:** make completion and recovery messages unambiguous: what was saved, what failed, and what the user can do next. Keep existing confirmation, undo, and button-state policies unchanged.
- Keep entered values when validation fails. Validation messages follow section 6 of this guide, feedback delivery to `10_Frontend_UX_and_Layout_Standards.md`, and timeout/idempotency handling to the surrounding submission rules.

## 12. Verification Checklist

- [ ] Every field a form/endpoint accepts has been checked against the "What to Validate" list in §3.
- [ ] Server-side validation exists independent of whatever the client does.
- [ ] Errors use the applicable API envelope or native Blade/Livewire error handling and are keyed by field.
- [ ] Frontend shows inline field errors, not just a generic toast.
- [ ] The null/missing/wrong-type matrix has been worked through for every required field.
- [ ] No validation message exposes internal implementation detail.
- [ ] Rapid double-submit, validation failure, stale records, denied access, and failed-request recovery have been verified for the affected flow.
- [ ] Applicable payload serialization, disabled-control handling, modal state, timeout recovery, and server-side duplicate safeguards have been checked.

Security boundaries: `03_Security_Guidelines.md`. Database guarantees: `05_Database_Standards.md`. JSON/HTTP contracts: `06_API_Standards.md`. General state/failure coverage: `02_Planning_Conditions_and_System_Flows.md`. Feedback delivery: `10_Frontend_UX_and_Layout_Standards.md`.
