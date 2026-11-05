# Naming Conventions

## Variables & Functions

| Context | Convention | Example |
|---|---|---|
| PHP variables | camelCase | `$userId`, `$programName` |
| JS/TS variables | camelCase | `userId`, `isLoading` |
| PHP functions | camelCase | `getUserById()` |
| JS functions | camelCase | `handleSubmit()` |
| Constants | UPPER_SNAKE_CASE | `MAX_FILE_SIZE`, `DEFAULT_ROLE` |
| Boolean variables | prefix with is/has/can | `isActive`, `hasRole`, `canEdit` |

- Name methods for the operation and domain object: `grantShares()`, `updateSharePermission()`, `revokeShare()`.
- Use `is` / `has` for state checks and `can` for authorization predicates; name throwing guards `require...` or `authorize...`.
- Eloquent relationships use domain nouns (`shares()`, `recipient()`); reusable query scopes describe their filter (`sharedWith($userId)`). Relationship methods return relationship objects, not fetched arrays.
- Preserve framework-required names such as `mount()`, `render()`, `hydrate()`, `handle()`, and resource controller actions. Do not rename an established public method without updating and checking its callers.
- Distinguish syllabus-wide fields from access sharing. Existing `canEditShared()` refers to shared preparation fields; use explicit names such as `canManageShares()` for permission management.
- Follow `07_Coding_Principles_and_Standards.md` before extracting methods; descriptive naming does not justify one-line forwarding helpers.

---

## Database

| Item | Convention | Example |
|---|---|---|
| Tables | plural_snake_case | `program_volunteers`, `audit_logs` |
| Columns | snake_case | `first_name`, `created_at` |
| Primary key | `id` | `id` |
| Foreign key | `{table_singular}_id` | `user_id`, `program_id` |
| Pivot tables | alphabetical singular | `program_user`, `role_user` |
| Soft delete column | `deleted_at` | `deleted_at` |
| Boolean columns | prefix with `is_` | `is_active`, `is_verified` |

---

## Laravel (PHP)

| Item | Convention | Example |
|---|---|---|
| Models | SingularPascalCase | `User`, `ProgramVolunteer` |
| Controllers | PascalCase + Controller | `UserController` |
| Services | PascalCase + Service | `AuditLogService` |
| Requests | PascalCase + Request | `StoreUserRequest` |
| Events | PascalCase (past tense) | `UserRoleUpdated` |
| Listeners | PascalCase | `SendRoleUpdateNotification` |
| Notifications | PascalCase | `PaymentReminder` |
| Migrations | snake_case descriptive | `create_users_table`, `add_status_to_users_table` |
| Seeders | PascalCase + Seeder | `UserSeeder` |
| Factories | PascalCase + Factory | `UserFactory` |

---

## Routes

| Type | Convention | Example |
|---|---|---|
| Web routes | kebab-case | `/program-volunteers`, `/audit-logs` |
| Route names | dot.notation | `users.index`, `programs.store` |
| API routes | kebab-case, versioned | `/api/v1/users` |

---

## Frontend

| Item | Convention | Example |
|---|---|---|
| Vue/React components | PascalCase | `UserTable`, `ConfirmModal` |
| New Blade views | descriptive kebab-case | `sharing-modal.blade.php` |
| New page-specific Blade partials | nearby `partials/` folder, descriptive kebab-case filename | `partials/edit-modal.blade.php` |
| Shared Blade components | under `resources/views/components/`, grouped by purpose | `form/input.blade.php` used as `<x-form.input />` |
| CSS classes | kebab-case | `btn-primary`, `card-header` |
| JS files | kebab-case | `user-table.js` |
| Vue files | PascalCase | `UserTable.vue` |
| Livewire classes | PascalCase in the owning domain namespace | `App\Livewire\Syllabus\Sharing\SyllabusSharingModal` |
| Livewire root views | matching domain folder and explicit render path | `livewire/syllabus/sharing/syllabus-sharing-modal.blade.php` |
| Livewire events | kebab-case, domain-prefixed | `syllabus-shares-updated` |

---

## Design Tokens & CSS

| Item | Convention | Example |
|---|---|---|
| CSS custom properties (tokens) | kebab-case, prefixed by purpose | `--color-primary`, `--shadow-elevated` |
| Utility/label text classes | consistent scale, not one-off values | `text-[10px] font-bold uppercase tracking-[0.14em]` for section labels |
| Component-scoped classes | kebab-case | `card-panel`, `stat-badge` |

---

## Files & Folders

Within `.amazonq/rules/`, use `NN_Descriptive_Title.md`: continuous two-digit prefixes, underscores between words, no spaces or hyphens, and no dots except the `.md` extension. This directory-specific convention takes precedence over the general Markdown naming convention below.

| Item | Convention | Example |
|---|---|---|
| Laravel controllers | PascalCase | `UserController.php` |
| Laravel services | PascalCase | `AuditLogService.php` |
| Vue components | PascalCase | `ConfirmModal.vue` |
| JSON data files | kebab-case | `provinces.json`, `status-options.json` |
| Markdown docs outside `MD/` | kebab-case unless the directory has an established convention | `deployment-checklist.md` |
| Module and planning docs inside `MD/` | follow `15_Documentation_Standards.md` | `25_Syllabus_Sharing_Proposal.md` |
| Uploaded files | uuid or timestamp prefix | `1720000000_profile.jpg` |

## Class References and Imports

- Prefer explicit `use` imports in PHP and supported `@use` imports in Blade when they improve readability. Preserve namespaces, class-string contracts, and references in comments.
- Do not introduce duplicate imports or a short name that resolves to the wrong class. Use an existing alias or retain the fully-qualified reference when names conflict.
- Insert new imports consistently without reordering unrelated imports. Remove imports made unused by the current implementation change; leave unrelated cleanup outside scope.
- Import extraction changes code references, so it is not a whitespace-only formatting operation. Verify that class resolution remains the same.

## File Organization and New Files

- Delete empty folders that serve no purpose. Group related files into subfolders only when a coherent set of files shares a clear responsibility — do not create a folder to hold a single file (create only if needed to folder it to be organized).
- Inspect related files and documentation before choosing a path. Use the existing domain folders and exact namespace/view-path casing; Windows can hide casing failures that appear on Linux.
- Keep models in `app/Models/`, controllers in `app/Http/Controllers/{Domain}/`, and services in `app/Services/{Domain}/`. Use an established subdomain folder when applicable; create a new one only for a coherent responsibility, not merely to hold each class.
- Keep Livewire classes under `app/Livewire/{Domain}/` and their views under `resources/views/livewire/{domain}/`. A Livewire component's root view belongs to that component; it is not a page partial.
- Keep presentation-only page sections in a nearby `partials/` directory. A feature used by two pages can have one owning Livewire component mounted by both; its internals do not become application-wide Blade components solely because of that reuse.
- Put application-wide presentation components in `resources/views/components/`, grouped by purpose. Reuse existing form, modal, button, and feedback components before adding equivalents.
- Keep feature JavaScript under the existing domain directory and load it through the existing entry point. Do not create a separate asset entry without a concrete need.
- Create executable schema changes in `database/migrations/` using unique `YYYY_MM_DD_HHMMSS_description.php` filenames. Do not edit historical or `database/cleaned-migrations/` copies to deploy a new schema change.
- Name feature tests by behavior and place them in the existing test domain directory. Distinguish planned files from existing source files in proposals; verify paths before listing them as implemented.
- Preserve existing names when reviewing or extending a feature. A naming rule is not authorization for a repository-wide rename; scoped renames must update namespaces, imports, view references, routes, tests, and documentation together.

---

## Do Not

- Never use single-letter variable names except loop counters (`$i`, `$j`)
- Never use abbreviations that aren't obvious (`$usrNm` → use `$userName`)
- Never name a variable `$data`, `$result`, `$temp` without context
- Do not introduce spaces in new code or asset filenames. Inside `MD/`, follow the documentation convention in `15_Documentation_Standards.md`; preserve existing filenames unless a rename is part of the authorized scope.
