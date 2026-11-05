# Database Guide

## Schema Rules

- Every relationship has a foreign key constraint.
- Never auto-delete data — no unguarded cascade deletes on tables with historical/audit significance. Prefer soft deletes for those.
- Schema changes go through migrations only — never manual DB edits.
- Normalize appropriately; flag denormalization explicitly when chosen for performance, with a comment explaining why.
- Add unique constraints where needed — including composite unique constraints wherever a "duplicate row" would be a data integrity error, not just a UX inconvenience.
- Always include `created_at` and `updated_at` timestamps.
- Display data with a counter or generated reference — never expose raw sequential IDs to the user where avoidable.

(See `08_Naming_and_File_Organization.md` for table/column/foreign-key naming.)

---

## Queries

- Keep simple, one-off queries near their caller. When a query or filter is reused, put it on the owning model as a named local scope or model query method; callers compose it instead of copying conditions.
- Define Eloquent relationships on the owning models and reuse those relationship methods for querying and eager loading. Return relationship objects, not fetched collections, from relationship methods.
- Keep scopes composable: apply query constraints without prematurely calling `get()`, `first()`, or `paginate()`. Let callers choose retrieval, ordering, and pagination unless a named retrieval method explicitly owns that contract.
- Model methods own model-specific data rules; services coordinate workflows across models, transactions, or external systems. Do not move reusable model queries into a service merely to share them.
- Pass required context explicitly; avoid coupling reusable queries to the current HTTP request or UI state. Preserve ownership/access constraints and authorize actions at their entry points; a scope alone is not authorization.
- Avoid N+1 queries — always eager load relationships when appropriate.
- Enforce uniqueness with database constraints. Existence checks and `firstOrCreate()` / `updateOrCreate()` do not replace constraints under concurrency; handle conflicts deliberately.

---

## Transactions

Wrap writes that must succeed together in a transaction. Prefer the closure form, which rolls back and propagates exceptions:

```php
DB::transaction(function () {
    // Perform related database writes.
});
```

---

## Common Pitfalls to Check For in a Schema Review

- Missing composite unique constraints where a combination of columns (not a single column) determines uniqueness.
- Cascade deletes on ownership/authorship foreign keys (e.g. `user_id` on content someone authored) — deleting the user shouldn't silently delete their historical work. Prefer `restrict`/`set null` + soft delete instead.
- Nullable foreign key or identifying columns that quietly undermine a unique index (a unique constraint with a nullable column allows multiple "duplicate" rows because `NULL != NULL` in most databases).
- Schema duplication — the same piece of information stored in two tables that can drift out of sync instead of being derived or referenced.

---

## Checklist Before Merging a Migration/Query Change

- [ ] Foreign keys defined and types match on both sides (e.g. both `unsignedBigInteger`)
- [ ] Soft deletes used for anything with audit/historical significance
- [ ] Unique constraints cover every case where a duplicate is a data error
- [ ] No unbounded queries — pagination used for list endpoints
- [ ] Relationships eager loaded, not N+1
- [ ] Destructive queries scoped precisely (no accidental mass update/delete)

## Side Effects and Recovery

- Dispatch jobs, notifications, and external side effects after a successful commit. Configure the relevant after-commit mechanism instead of assuming a queued job waits for commit.
- Database rollback cannot undo an email, external API call, or filesystem write. Define cleanup, retry, and duplicate-delivery behavior; use durable delivery tracking when required by the workflow.
- Record required audit data atomically with the writes it describes, or define reliable after-commit delivery/recovery. Never record rolled-back changes as successful.
- Use appropriate locks or conditional updates for competing transitions. Keep business uniqueness enforced by constraints; see `04_Input_Validation_and_Form_Submission.md` for idempotency.
- Test migrations and reset behavior only on a verified disposable database using `01_Agent_Guidelines_and_Workflow.md`.
