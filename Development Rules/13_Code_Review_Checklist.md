# Code Review Checklist

Apply this to every piece of code reviewed or written — treat it as going into production. Never assume code is correct by default.

## 1. Correctness & Business Logic
- All conditions, validations, and business rules are complete — no missing branches.
- No conflicting logic or unreachable code.
- Edge cases identified: empty states, null/zero values, first-item/last-item, concurrent access.
- Race conditions checked wherever async events, queued jobs, or parallel Livewire calls can overlap.
- Reactive frontend state (Alpine stores, auto-save, event listeners) checked for self-triggered loops — does a listener ever fire the same event it's listening for?
- Deletes/updates on records that might already be gone (double-click, rapid actions) fail gracefully instead of throwing (e.g. use a null-guarded lookup, not a hard `findOrFail()`, where a missing record is an expected possibility rather than an error).

## 2. Security
- Authorization checked (policies/gates), not just authentication.
- Mass assignment guarded (`$fillable`/`$guarded` correct).
- User input validated; query values bound and output encoded for its context. Paths and URL destinations constrained (see `03_Security_Guidelines.md`).
- No sensitive data leaked in logs, error messages, or API responses.

(Full detail in `03_Security_Guidelines.md`.)

## 3. Error Handling
- Try-catch used where failure is plausible (external calls, file I/O, third-party APIs).
- Failures are handled, logged, or rethrown with context — never swallowed silently.
- Unexpected exceptions reported once; diagnostic logs and audit records follow `02_Planning_Conditions_and_System_Flows.md` without logging every output or ordinary validation failure.

## 4. Database & Queries
- No N+1 queries — eager load relationships.
- Reusable queries/filters extracted to model scopes, not repeated across controllers/services.
- Eloquent relationships defined on their owning models and reused; services coordinate workflows rather than duplicate model queries.
- Queries are indexed appropriately for their access pattern.
- Destructive queries (delete/update) are scoped precisely — no accidental mass updates.

(Full detail in `05_Database_Standards.md`.)

## 5. Clean Code
- DRY: no duplicated logic, queries, or blade blocks.
- Deep nesting avoided via guard clauses/early returns.
- Large methods/controllers/components broken into smaller, single-responsibility units.
- Existing UI components reused for inputs, tables, cards, modals, and other shared UI; substantial page-specific sections extracted to `partials/` when useful, preserving state and triggers.
- Reusable logic extracted into helpers, services, traits, or model methods as appropriate — not speculatively, only where duplication already exists or is clearly imminent.

## 6. UX Safety
- Destructive actions (delete, remove, detach, unassign) have a confirmation dialog/modal.
- User flows reviewed for accidental-action risk (e.g. adjacent destructive and safe buttons).
- Every user action gives appropriate feedback, including visible state changes; toasts are not required for navigation, filtering, or opening panels. Inline errors and persistent alerts follow `10_Frontend_UX_and_Layout_Standards.md`.

## 7. Performance
- No unnecessary re-renders or excessive Livewire round-trips.
- Images optimized; no unbounded DOM growth from list rendering.
- No layout shift or blocking animations on load.

## 8. Readability & Naming
- Descriptive, consistent naming throughout — a new reader shouldn't need to guess intent.
- Implementation approach follows `07_Coding_Principles_and_Standards.md`; spacing, indentation, braces, and wrapping follow `.editorconfig`, `09_Code_Formatting_Standards.md`, and `09_Code_Formatting_Standards.md`.
- Comments explain *why*, not *what*, and only where the code itself can't.

## Outcome Bar
The result should be easier to read, less repetitive, less nested, and consistent with the rest of the codebase — without being over-engineered. **More checklist items covered is not automatically better: prioritize the fixes that actually reduce risk or confusion, over changes that just add process.**
