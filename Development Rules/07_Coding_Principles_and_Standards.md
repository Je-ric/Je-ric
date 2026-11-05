# Project Principles & Coding Standards

# Software Design Principles

## KISS (Keep It Simple, Stupid)
- Always implement the simplest solution that correctly solves today's problem.
- Prefer straightforward code over clever or highly abstract solutions.
- Only introduce additional layers (services, repositories, interfaces, factories, strategies, etc.) when they solve a real, existing problem.
- Avoid premature optimization and speculative architecture.
- Readability is more valuable than cleverness.

## YAGNI (You Aren't Gonna Need It)
- Build only for current requirements.
- Do not add extension points, interfaces, or configuration solely because they "might be useful later."
- Introduce abstractions only after a second legitimate use case exists.

## DRY (Don't Repeat Yourself)
- Eliminate duplicated business logic.
- Extract repeated validation, calculations, transformations, and queries into reusable methods or services.
- Do NOT extract code simply because two snippets look similar — only when they represent the same knowledge or business rule.
- Prefer small reusable methods over copy-paste.

## Rule of Three (when to actually refactor)
- First occurrence: write the code.
- Second occurrence: notice the similarity.
- Third occurrence: refactor into a reusable method, service, or component.
- Use this as a heuristic for new abstractions, not a reason to duplicate an existing component or shared domain rule. Model relationships belong on models from their first use; extract a query once actual reuse is established (see `05_Database_Standards.md`).

## Complexity and Prioritization

- **Occam's Razor:** among approaches that meet the same requirements, choose the one with fewer assumptions and moving parts; correctness and security remain requirements.
- **Tesler's Law:** automate necessary complexity when the system can do so reliably, such as deriving a value instead of making the user calculate it. Do not hide uncertainty or remove a necessary user decision.
- **Pareto Principle as a heuristic:** prioritize the affected workflow's frequent or costly problems using actual usage/error evidence. Do not assume an exact 80/20 split or neglect rare high-impact failures.

## SOLID
- Apply SOLID where it improves maintainability.
- Do not force every class behind an interface.
- Favor composition over inheritance.
- Single Responsibility Principle takes priority over unnecessary abstractions.

---

# Coding Standards

## Functions & Methods

### Create a new method when:
- A method exceeds ~50 lines.
- A logical block has a single responsibility.
- Logic is repeated.
- The block can be named clearly.
- The extracted method improves readability.
- The extracted logic can be independently tested.

### Do NOT create a new method when:
- It only wraps one line with no added meaning.
- It would require jumping through many tiny methods just to understand one workflow.
- The extraction makes the code harder to follow.

### Naming
- Method names should describe exactly what they do.
- Use verbs.
- Avoid vague names like: `process()`, `handle()`, `data()`, `execute()`, `helper()`, `utils()`.
- This applies to names we choose; preserve framework-required lifecycle/handler names and established resource controller actions. See `08_Naming_and_File_Organization.md` for relationship, scope, predicate, guard, and event naming.
- Prefer names like: `saveDraft()`, `synchronizeTeachingLoads()`, `calculateConsultationHours()`, `validateCourseOutcomes()`.

(See `08_Naming_and_File_Organization.md` for casing/format rules across the whole stack.)

---

## Classes

Create a new class when:
- It has its own responsibility.
- Multiple components need the same behavior.
- Business logic becomes too large for one component.
- It represents a real domain concept.

Avoid creating classes that only forward calls.

Bad:
```
Controller → Manager → Service → Repository → Helper → Model
```
Good:
```
Controller / Livewire → Service (optional) → Model
```

---

## Services

Create a service when:
- Logic is shared by multiple components.
- The logic represents business rules.
- External APIs are involved.
- Transactions span multiple models.
- The logic should be unit tested independently.

Do NOT create a service for simple CRUD.

---

## Interfaces

Create interfaces ONLY when:
- Multiple implementations exist today.
- Different implementations are actively expected.
- Dependency inversion provides immediate value.

Do NOT create interfaces "just because SOLID."

Bad: `UserServiceInterface` → `UserService`
Good: `UserService`

---

## Helpers

Use helpers only for: pure utility functions, formatting, parsing, date conversions, string transformations.

Helpers must:
- Have no side effects.
- Never perform database writes.
- Never contain business logic.

---

## Livewire

Livewire components coordinate UI actions; business workflows belong there or in dedicated services. Reusable queries, filters, and Eloquent relationships belong on the owning models (see `05_Database_Standards.md`).

Blade templates should only:
- Display data.
- Loop.
- Render conditionals.
- Trigger actions.

Never:
- Query the database.
- Mutate data.
- Perform calculations that belong in PHP.

---

## Frontend State Management (Alpine.js / reactive components)

- Prefer local component state (`x-data`) by default; reach for a global store only when multiple independent components genuinely need to read or react to the same state.
- When several components must all complete an action before a parent step can proceed (e.g. "save all before continuing"), use an explicit register/run pattern (each component registers a handler, the parent runs them via `Promise.all()`) rather than ad-hoc event chains — ad-hoc chains are where race conditions creep in.
- Watch for self-triggered update loops: an auto-save that listens for the same event it fires after saving will echo forever. If a component both listens for and emits the same signal, break the loop with a guard flag or restructure to a one-directional (deferred push) pattern.
- Debounce or defer rapid-fire saves (typing, drag, autosave) rather than firing a request per keystroke/event.

---

## Error Handling

Never swallow exceptions.

Every catch block must:
- Handle it,
- Log it,
- Or rethrow it with context.

Never leave an empty catch block.

Report unexpected failures once at the responsible boundary; do not log and rethrow the same exception for the framework to report again. Expected validation failures do not require error logs. See `02_Planning_Conditions_and_System_Flows.md` for logging decisions and `10_Frontend_UX_and_Layout_Standards.md` for user feedback.

---

## Performance

Prefer solving performance issues with, in order:
1. Better queries.
2. Eager loading.
3. Caching.
4. Computed properties.
5. Batch operations.

Only optimize after identifying a real bottleneck.

---

## Readability

### Implementation Approach

- Use guard clauses and early returns to reduce nesting; keep conditional expressions easy to follow.
- Keep queries, mutations, calculations, and domain decisions in PHP rather than Blade. Presentation templates render data and trigger actions.
- JavaScript uses `const` by default and `let` when reassignment is required.
- This guide owns implementation choices: responsibilities, extraction, reuse, control flow, state ownership, and error handling.
- Spacing, indentation, brace placement, line wrapping, and visual code layout belong to `09_Code_Formatting_Standards.md` and `09_Code_Formatting_Standards.md`, following `.editorconfig`.

### Usage Documentation

For **Models**, **Helpers**, **Policies**, **Enums**, and **Data classes** (in `app/Data`), document external callers directly above each externally called method/function. Do not also require a duplicate file-level usage block. Update affected usage lists when callers change.

**Format:**
```php
// Used in:
// methodName() - ClassName.php (Service/Controller/Livewire/Blade);
// path/to/file.blade.php (Blade);
```

```
Example:

// Used in:
// reviewStep() - ProgramOutcomeReviewService.php (Service);
// syllabus() - ReviewProgressService.php (Service);
// courseOutcomes() - ReviewProgressService.php (Service);
// resources/views/livewire/cqi/course-outcomes/partials/feedback.blade.php (Blade);
// resources/views/livewire/cqi/outcome-log-sheet.blade.php (Blade);
// resources/views/livewire/cqi/program-outcomes/change-review.blade.php (Blade);
```

Rules:

- Place the `// Used in:` block directly above each method/function that is called from outside the file, not as a single block at the top of the file.
- List all files that import or use that specific method (excluding test files).
- Include the calling method/function name when applicable.
- Specify the file type (Service, Controller, Livewire, Blade, etc.).
- Keep the list updated when new usages are added or removed.
- Use relative paths for Blade files from the `resources/` directory.
