# Documentation Updates

Ask before editing `MD/` documents when the current request does not already authorize documentation updates. An explicit request to update a document, or approval given in the current session, satisfies this requirement for the authorized scope; do not ask again for that same update. A review-only request does not authorize rewriting the reviewed document.

For every code change, feature update, bug fix, refactor, configuration change, or new module added to the project:

Search `MD/` for the affected module, files, routes, and behavior; read relevant documents and cross-references. Do not reread every module for an unrelated change.
Update existing Markdown files affected by the authorized scope. If an additional `MD/` edit needs approval under the opening rule, prepare the concrete proposed update and request approval before applying it.
If the change introduces a new module, subsystem, service, workflow, or major feature, create a new dedicated Markdown document in `MD/`.
Complete authorized documentation updates in the same task/session as the code change. If additional documentation approval is pending, identify the exact remaining update and do not claim it was completed.

---

## Document Format Conventions (Follow These)

### File Naming
- Numbered: `##_Short_Name.md` (e.g., `01_Authentication_OTP_Account_Status.md`)
- Supporting docs: `Name.md` (e.g., `Commands.md`, `Project_Version.md`)
- Multiword new names use underscores rather than spaces. Preserve existing filenames unless renaming is explicitly in scope; update all known cross-references if a rename is authorized.
- Keep proposals in `MD/To be developed/`, implementation guides in `MD/System/`, user instructions in `MD/Manual/`, and review reports in `MD/Reviews/`, following each directory's existing purpose.
- Choose an unused document number within the target directory; do not renumber unrelated documents to insert a new one.
- This `MD/` convention takes precedence over the general kebab-case Markdown rule in `08_Naming_and_File_Organization.md`.
- Proposals must label planned files and behavior separately from verified existing implementation. Do not describe unimplemented features as current system behavior.

### Structure (per document)
1. **Title** — `# Title` (H1), concise.
2. **Subtitle** — One-line description below the title, plain text.
3. **Design Rationale** (optional, for key architectural decisions) — `## Why X` with `### Why Y?` bullet lists.
4. **Files Used (Source of Truth)** — `## Files Used (Source of Truth)`. Categorized lists:
   ```markdown
   - Controllers
     - `app/Http/Controllers/FooController.php`
   - Services
     - `app/Services/FooService.php`
   - Models
     - `app/Models/Foo.php`
   - Views
     - `resources/views/foo/index.blade.php`
   - Routes
     - `routes/web.php` (description)
   ```
   Always list the **actual files** that implement the behavior. Omit files that no longer exist.
5. **Key Concepts** (optional) — `## Key Concepts` or `## What It Is`. Bullet or short paragraph.
6. **Conditions (If / Then)** — `## Conditions (If / Then)`. The main body. Use this format:
   ```markdown
   ### Subsection Title

   - If [condition]:
     - Then [behavior].
     - Then [another behavior].
   - If [different condition]:
     - Then [behavior].
   ```
   Indent `Then` with 2 extra spaces. Use plain English. Group related conditions under `###` subheadings.
7. **Sequences (Typical Flow)** — `## Sequences (Typical Flow)`. Numbered steps or bullet flows showing user-facing scenarios end-to-end.
8. **UI Notes** (optional) — `## UI Notes`. Describe relevant UI components, layouts, and user-facing behavior.
9. **Tables** — Use GFM tables for structured data (routes, statuses, role permissions, etc.).
10. **Cross-references** — Use inline code: `` `MD/XX_File_Name.md` ``. Never use wikilinks (`[[...]]`).

### Code Blocks
- Use ` ```php`, ` ```blade`, ` ```bash`, ` ```json` as appropriate.
- Keep code blocks minimal — docs should describe behavior, not duplicate code.

### Voice & Audience
- **Beginner-friendly but precise.** Write as if the reader knows Laravel but not this codebase.
- Use active voice. Be direct. No fluff.
- Every doc should stand alone — define terms on first use.

### What to Document in Conditions (If / Then)
- Validation rules with specific field names.
- Authorization guards (role checks, ownership checks).
- Side effects (audit logs, email sends, cascade deletes).
- Edge cases (empty states, duplicate prevention, blocked operations).
- UI behavior triggered by state changes.

### What NOT to Document
- Generic Laravel behaviors (route model binding, CSRF protection).
- Obvious CRUD (create/update/delete unless there are special rules).
- Internal implementation details that don't affect behavior.

### Update Checklist
When updating a doc:
1. Verify all referenced file paths still exist in the codebase.
2. Remove references to deleted/renamed files.
3. Add references to new files that implement related behavior.
4. Update cross-references to match current file numbering/names.
5. Ensure `Conditions (If / Then)` match current code logic.
6. Ensure `Sequences (Typical Flow)` match current user-facing behavior.
