# Agent Rules Index

## Scope and Reading Sequence

Main rules follow a continuous 00–16 reading sequence. Filenames use numbered, descriptive words separated by underscores, with a dot only before the `.md` extension. The combined rule content is retained. Supplementary design/source/reference documents live in `.amazonq/references/`. Read the topics applicable to the task. Numbering makes the intended sequence visible; it does not guarantee automatic tool loading order or override priority.

1. Establish request mode, scope, and workspace safety: `01_Agent_Guidelines_and_Workflow.md`. Check `01_Agent_Guidelines_and_Workflow.md` before relying on package APIs.
2. Plan behavior: `02_Planning_Conditions_and_System_Flows.md`.
3. Define trust boundaries and data contracts: `03_Security_Guidelines.md`, `04_Input_Validation_and_Form_Submission.md`, `05_Database_Standards.md`, and `06_API_Standards.md`.
4. Choose the implementation approach: `07_Coding_Principles_and_Standards.md`, then naming/file organization in `08_Naming_and_File_Organization.md`.
5. Format code using `.editorconfig`, `09_Code_Formatting_Standards.md`. Formatting concerns spacing, indentation, braces, wrapping, and presentation; it does not decide architecture or rewrite behavior.
6. For UI work, consult `10_Frontend_UX_and_Layout_Standards.md`, `04_Input_Validation_and_Form_Submission.md`, `11_System_Design_Standards.md`, and `12_UI_Quality_Checklist.md`. Interaction policies and design-token guidance are unchanged; local file references follow the new names.
7. Review and verify against `13_Code_Review_Checklist.md` and the verification workflow. Use `14_Debugging_and_Troubleshooting.md` when diagnosing a problem.
8. Update authorized documentation using `15_Documentation_Standards.md`. Deploy only when authorized, following `16_Deployment_Checklist.md`.

## Topic Ownership

User instructions and host requirements take priority. Detailed topic rules own their respective concerns; the review checklist summarizes them rather than creating another implementation standard. A higher filename number does not override an earlier rule.

| Concern | Detailed rule |
|---|---|
| Agent role, request mode, workspace safety, verification | `01_Agent_Guidelines_and_Workflow.md` |
| Planning, flows, diagnostic versus audit logging | `02_Planning_Conditions_and_System_Flows.md` |
| Branches, states, failure coverage | `02_Planning_Conditions_and_System_Flows.md` |
| Security boundaries, encoding, access control, CSRF | `03_Security_Guidelines.md` |
| Input validation, field errors, form submission, recovery, and duplicate-request safeguards | `04_Input_Validation_and_Form_Submission.md` |
| Queries, constraints, transactions, side effects | `05_Database_Standards.md` |
| HTTP and application JSON response contracts | `06_API_Standards.md` |
| How code is designed: responsibilities, reuse, extraction, control flow | `07_Coding_Principles_and_Standards.md` |
| Names, paths, imports, and class references | `08_Naming_and_File_Organization.md` |
| PHP and Blade spacing, indentation, wrapping, and code layout | `09_Code_Formatting_Standards.md` |
| Documentation scope and conventions | `15_Documentation_Standards.md` |
| Authorized deployment | `16_Deployment_Checklist.md` |
| Dependency/API compatibility | `01_Agent_Guidelines_and_Workflow.md` |

## Types of Guidance

- Standards prescribe behavior within the authorized task scope. Examples illustrate those standards and do not authorize executing commands.
- Optional patterns apply when their stated problem exists; they are not a requirement to add every feature to every page.
- `14_Debugging_and_Troubleshooting.md` records diagnostic examples; establish the current cause before applying a historical fix.
- `.amazonq/references/visual-design-principles.md`, `.amazonq/references/ux-laws.md`, and `.amazonq/references/ui-design-source-notes.txt` contain design background and advice. Their full source text is preserved; practical UX and visual principles are integrated into the topic rules below without changing the excluded interaction policies.
- `.amazonq/references/sanctum.md` retains its original guidance, provenance, and version labels. Only its filename and local file references were updated.
- Historical form completion claims/backlog are preserved in `.amazonq/history/form-submission-status-before-review.md`, outside the active rules folder. They are not verified completion evidence.
- If instructions still conflict outside the resolved topics, surface the specific conflict. This review does not select a new policy for confirmations, undo, disabled buttons, or action visibility.

## Main Rules Inventory

| File | Covers |
|---|---|
| `01_Agent_Guidelines_and_Workflow.md` | Agent Guidelines and Workflow |
| `02_Planning_Conditions_and_System_Flows.md` | Planning, Conditions, and System Flows |
| `03_Security_Guidelines.md` | Security Guidelines |
| `04_Input_Validation_and_Form_Submission.md` | Input Validation and Form Submission Standards |
| `05_Database_Standards.md` | Database Standards |
| `06_API_Standards.md` | API Standards |
| `07_Coding_Principles_and_Standards.md` | Coding Principles and Standards |
| `08_Naming_and_File_Organization.md` | Naming and File Organization |
| `09_Code_Formatting_Standards.md` | Code Formatting Standards |
| `10_Frontend_UX_and_Layout_Standards.md` | Frontend, UX, and Layout Standards |
| `11_System_Design_Standards.md` | System Design Standards |
| `12_UI_Quality_Checklist.md` | UI Quality Checklist |
| `13_Code_Review_Checklist.md` | Code Review Checklist |
| `14_Debugging_and_Troubleshooting.md` | Debugging and Troubleshooting |
| `15_Documentation_Standards.md` | Documentation Standards |
| `16_Deployment_Checklist.md` | Deployment Checklist |

## Supplementary References

These documents are preserved outside the main rules folder; the excluded UI interaction, design-token, and Sanctum guidance was not rewritten.

| File | Covers |
|---|---|
| `.amazonq/references/visual-design-principles.md` | Visual Design Principles |
| `.amazonq/references/ux-laws.md` | UX Laws |
| `.amazonq/references/ui-design-source-notes.txt` | UI/UX Design Guide — Avoiding the "AI-Made" Look |
| `.amazonq/references/sanctum.md` | Laravel Sanctum |

## Scope of This Reorganization

Coding standards own how code is designed and implemented. Formatting guides own spacing, indentation, braces, wrapping, and presentation. Naming/import rules own names and class references.

The three excluded changes remain excluded: no unified confirmation/undo/disabled-button/action-visibility policy was introduced; design-token guidance was not reconciled with CSS; Sanctum guidance was not relabeled or updated to another framework version.

The filename migration is recorded in `.amazonq/history/rules-filename-migration.md`. Original files are preserved in `.amazonq/backups/`.

## Integrated UX and Visual Principles

| Detailed owner | Applied guidance |
|---|---|
| `10_Frontend_UX_and_Layout_Standards.md` | Familiar patterns, memory demands, contextual help, usable targets, contrast, typography, semantic color/shape |
| `10_Frontend_UX_and_Layout_Standards.md` | Choice structure, grouping, reading order, balance, emphasis, proportion, movement, framing |
| `04_Input_Validation_and_Form_Submission.md` | Field contracts, normalization, submission lifecycle, truthful progress, draft/resume continuity, completion, and recovery |
| `07_Coding_Principles_and_Standards.md` | Simple solutions, appropriate automation, evidence-based prioritization |
| `12_UI_Quality_Checklist.md` | Task completion, responsiveness, consistent repetition/rhythm, purposeful variety, unity |

Full theory stays in references. Principle names explain the rationale; they do not impose fixed option counts, universal timing guarantees, or relaxed validation. Existing confirmation, undo, disabled-button, and action-visibility policies remain as they were.


Combination record: `.amazonq/history/four-pair-combination-2026-11-05.md`. Both source texts are retained in each combined guide; repeated guidance has not been removed or reconciled.


Current filename map: `.amazonq/history/Rule_Filename_Map_2026_11_05.md`. Earlier migration reports preserve the filenames used at those historical steps.
