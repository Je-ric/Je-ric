# Debugging and Troubleshooting

# Debugging Guide

## Step 1 — Read the Error Carefully

- Read the full error message before doing anything
- Note the file name and line number
- Note the error type (TypeError, QueryException, 404, 500, etc.)

---

## Step 2 — Check the Common Minor Issues First

Check these common causes first:

- Typo in variable name (`$userId` vs `$userid`)
- Wrong parameter order in function call
- Missing `return` statement
- Wrong comparison operator (`=` instead of `==` or `===`)
- Null / undefined value being accessed
- Wrong data type (`"1"` vs `1`)
- Case sensitivity (`User` vs `user`, `GET` vs `get`)
- Missing comma or bracket in array/object
- Wrong array key name
- A listener firing the same event it's listening for (infinite update loop in reactive/auto-save components)

---

## Step 3 — Use the Right Tool

### PHP / Laravel

| Tool | How |
|---|---|
| `dd($var)` | Dump and die — stops execution and shows value |
| `dump($var)` | Dump without stopping |
| `Log::info('message', ['data' => $var])` | Write to log file |
| `storage/logs/laravel.log` | Check the log file |
| `php artisan tinker` | Run PHP interactively |

### JavaScript / Frontend

| Tool | How |
|---|---|
| `console.log(var)` | Print value |
| `console.table(array)` | Print array as table |
| `console.error(msg)` | Print error in red |
| `debugger;` | Pause execution at that line |

### Browser DevTools (F12)

| Tab | What to check |
|---|---|
| Elements | Actual rendered DOM — check if HTML is correct |
| Console | JS errors and logs |
| Network | Failed requests, status codes, request/response payloads |
| Sources | Set breakpoints, step through JS |
| Application | localStorage, sessionStorage, cookies |
| Performance | Slow scripts, rendering bottlenecks |
| Security | HTTPS / certificate issues |

---

## Step 4 — Check the Environment

- Is `.env` correct? (DB credentials, APP_URL, mail settings)
- Is the correct `.env` being used? (local vs production)
- Is configuration cached? Refresh only the relevant configuration through the intended environment/deployment process.

---

## Step 5 — Check Compatibility

- Framework version mismatch
- Package version conflict
- Node / npm version mismatch
- PHP version mismatch
- Browser compatibility issue
- Different database versions

---

## Step 6 — Check the Database

- Is the migration run? (`php artisan migrate:status`)
- Is the table/column name correct?
- Is there a null value where a value is expected?
- Is the relationship defined correctly?
- Is the query returning empty when it shouldn't?
- Are foreign key constraints blocking the operation?

---

## Step 7 — Check the Request / API

- Is the endpoint URL correct?
- Is the HTTP method correct? (GET vs POST vs PUT vs DELETE)
- Is the request payload correct? (check Network tab)
- Is the response what you expect? (check Network tab → Response)
- Is the CSRF token included?
- Is the auth token included?

---

## Common Error Messages

| Error | Likely Cause |
|---|---|
| `SQLSTATE[23000]: Integrity constraint violation` | Duplicate entry or FK constraint failed |
| `SQLSTATE[42S22]: Column not found` | Wrong column name or missing migration |
| `Class not found` | Missing import / wrong namespace |
| `419 Page Expired` | Missing `@csrf` or session expired |
| `403 Forbidden` | Missing role/permission |
| `404 Not Found` | Wrong route or missing record |
| `500 Server Error` | PHP exception — check `laravel.log` |
| `TokenMismatchException` | CSRF token mismatch |
| `Call to a member function on null` | Variable is null — check query result |
| `Undefined variable` | Variable not passed to view or not declared |
| `Mass assignment` | Field not in `$fillable` |

(For fixes to specific errors already encountered, see `14_Debugging_and_Troubleshooting.md`.)

---

## Checklist When Stuck

- [ ] Read the full error message
- [ ] Check the log file
- [ ] `dd()` the variable you suspect
- [ ] Check Network tab for the actual request/response
- [ ] Check if the DB has the expected data
- [ ] Check `.env` values
- [ ] Preserve evidence, inspect the relevant cache/configuration, and invalidate only what the diagnosis requires
- [ ] Check installed source and matching-version official documentation; redact sensitive values from searches
- [ ] Isolate — comment out code until the error disappears, then narrow down
## Diagnostic safety

- Use dumps, logs, browser payload inspection, and interactive PHP only in a suitable environment with sensitive values redacted.
- Verify the effective database connection before running interactive commands that could mutate data. A local or testing environment label alone does not prove isolation.
- Remove temporary diagnostics after verifying the fix. Do not clear caches or reset schemas as a substitute for finding the cause.

---

# Common Errors & Fixes

A diagnostic reference of historical symptoms and fixes, not a mandatory command list. Confirm the actual cause and intended environment before applying an example.
Add new entries at the top.

---

## Format

```
### [Error Title]
- **Context:** What were you doing when this happened?
- **Error:** The exact error message or symptom
- **Cause:** What caused it
- **Fix:** What resolved it
- **Prevention:** How to avoid it next time
```

---

## Laravel / PHP

### Syllabus wizard cannot call setStepDirty
- **Context:** Editing Course Components schedules or Course Evaluation weights.
- **Error:** `Unable to call component method. Public method [setStepDirty] not found on component`.
- **Cause:** The step views used `$wire.setStepDirty()`, which targets their child Livewire components. The method belongs to `SyllabusWizard`.
- **Fix:** Dispatch the bubbling `syllabus-step-dirty` event with `step` and `dirty`; the wizard listens with `#[On]` and updates state without rendering.
- **Prevention:** Keep wizard state on the parent and communicate from child steps through events. Deploy the wizard class and both step views together.

### 419 Page Expired on Form Submit
- **Context:** Submitting a form
- **Error:** `419 | Page Expired`
- **Cause:** Missing `@csrf` directive in the form
- **Fix:** Add `@csrf` inside the `<form>` tag
- **Prevention:** Always include `@csrf` in every form

---

### Call to a member function on null
- **Context:** Accessing a relationship or method on a model
- **Error:** `Call to a member function X() on null`
- **Cause:** The query returned null — the record doesn't exist
- **Fix:** Add a null check before accessing: `if ($model) { ... }` or use `findOrFail()`
- **Prevention:** Use `findOrFail()` when a missing resource should return 404; use a scoped, null-guarded lookup when absence is an expected workflow outcome.

---

### Mass Assignment Exception
- **Context:** Using `Model::create([...])` or `$model->fill([...])`
- **Error:** `Add [field] to fillable property`
- **Cause:** The field is not listed in the model's `$fillable` array
- **Fix:** Verify the field is intended to be assignable and authorized, then update `$fillable` if appropriate.
- **Prevention:** Keep `$fillable` updated whenever adding new columns

---

### Class Not Found
- **Context:** Using a class in a controller or service
- **Error:** `Class "App\Services\SomeService" not found`
- **Cause:** Wrong namespace or missing `use` import
- **Fix:** Add the correct `use` statement at the top of the file
- **Prevention:** Use IDE autocompletion to import classes

---

### SQLSTATE[23000]: Integrity Constraint Violation
- **Context:** Inserting or updating a record
- **Error:** `Integrity constraint violation: 1062 Duplicate entry`
- **Cause:** Trying to insert a duplicate value in a unique column
- **Fix:** Inspect the violated constraint and choose the intended duplicate/conflict behavior. Retrieval/create helpers do not replace database uniqueness under concurrency.
- **Prevention:** Combine useful validation with unique constraints and deliberate conflict handling.

---

### Undefined Variable in Blade
- **Context:** Rendering a Blade view
- **Error:** `Undefined variable $variableName`
- **Cause:** Variable not passed from controller to view
- **Fix:** Add the variable to the `return view('...', ['variableName' => $value])`
- **Prevention:** Always pass all required variables explicitly

---

## JavaScript / Frontend

### Self-Triggered Auto-Save Loop
- **Context:** A component auto-saves a field and also listens for a "changed" event to trigger re-renders/side effects
- **Error:** No thrown error — symptom is repeated/runaway save requests or flickering UI
- **Cause:** The save action emits the same event the component is listening for, so saving triggers another save
- **Fix:** Break the loop with a guard flag during the save, or restructure to a one-directional (deferred push) pattern instead of a listen/emit cycle
- **Prevention:** When a component both listens for and emits a signal, trace the full cycle before shipping — ask "can this fire itself?"

---

### Rapid-Delete 404 / Record Already Gone
- **Context:** User double-clicks delete, or deletes the same row from two tabs
- **Error:** `404 Not Found` or `ModelNotFoundException` from `findOrFail()`
- **Cause:** The record was already deleted by the first request; the second request can't find it
- **Fix:** Use a null-guarded `find()` (or check existence first) instead of `findOrFail()` for actions where "already gone" is an expected outcome, not an error — treat it as a no-op success rather than throwing
- **Prevention:** A missing detail resource can legitimately return 404. For idempotent deletion, define already-gone behavior while preserving actor/ownership scoping and handling races atomically.

---

### Cannot read properties of undefined
- **Context:** Accessing a property on a JS object
- **Error:** `Cannot read properties of undefined (reading 'x')`
- **Cause:** The object is undefined — API returned null or data not loaded yet
- **Fix:** Add optional chaining: `obj?.property` or check before accessing
- **Prevention:** Always handle loading and empty states

---

### CORS Error on API Request
- **Context:** Frontend calling a backend API
- **Error:** `Access to fetch at '...' from origin '...' has been blocked by CORS policy`
- **Cause:** Backend not configured to allow the frontend origin
- **Fix:** Add the frontend URL to `config/cors.php` allowed origins in Laravel
- **Prevention:** Configure CORS properly before starting frontend-backend integration

---

## Database / MySQL

### Cannot Add Foreign Key Constraint
- **Context:** Running a migration with a foreign key
- **Error:** `SQLSTATE[HY000]: General error: 1215 Cannot add foreign key constraint`
- **Cause:** Referenced table doesn't exist yet, or column types don't match
- **Fix:** Ensure the referenced table is created first; ensure column types match exactly (both `unsignedBigInteger`)
- **Prevention:** Create migrations in dependency order; use `unsignedBigInteger` consistently

---

### Table Already Exists
- **Context:** Running `php artisan migrate`
- **Error:** `SQLSTATE[42S01]: Table already exists`
- **Cause:** Migration was partially run or table was created manually
- **Fix:** Inspect migration history and the existing schema. Reconcile through a reviewed migration/recovery plan. Reset commands are only for a verified disposable database, never a routine repair.
- **Prevention:** Never manually create tables that have migrations

---

## Deployment

### .env Changes Not Taking Effect
- **Context:** Updated `.env` on server
- **Cause:** Laravel caches config — old values still in cache
- **Fix:** Verify the intended environment and rebuild or clear configuration cache through the deployment process.
- **Prevention:** Refresh cached configuration where needed; do not blindly clear application data caches.

---

### Storage Files Not Accessible (404)
- **Context:** Uploaded files returning 404
- **Cause:** Storage symlink not created
- **Fix:** Check the configured disk and access policy. Create a missing storage link only for intentionally public files; keep private downloads authorized.
- **Prevention:** Verify required public links and private download access during deployment.
