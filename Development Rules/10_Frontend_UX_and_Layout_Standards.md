# Frontend, UX, and Layout Standards

# Frontend & UI/UX Guidelines

Applies on top of `01_Agent_Guidelines_and_Workflow.md` and `13_Code_Review_Checklist.md`. Covers what's specific to UI work — general code-quality and safety checks live in the checklist, not repeated here.

## Stack
HTML5, CSS3, Tailwind CSS, JavaScript (ES2023+), Alpine.js, Laravel Blade, Livewire 4 (verify installed version using the compatibility guidance in `00_Index.md`), Vite.

## Layout
- Consistent spacing, alignment, margins, and container widths across pages.
- Clear visual hierarchy — the most important element on a screen should look like it.
- Deliberate use of white space, not leftover space.
- Maintain the selected color scheme / palette throughout the app.
- Use simple, low-vocabulary prompts and labels — design for the least technical user.

## Usability and Perception Principles

Apply these principles to the affected task; they do not require adding every suggested widget or interaction to every page.

| Principle | Practical rule |
|---|---|
| Jakob's Law; mental models | Reuse familiar navigation, labels, and component behavior. Explain necessary departures where the user encounters them. |
| Cognitive load; Miller's Law; working memory | Keep instructions and needed context visible; use summaries or carried-forward values instead of asking users to remember previous screens. Group complex choices without imposing a universal menu-item limit. |
| Fitts's Law | Keep frequent controls easy to reach and distinguish. Apply the existing target-size policy; do not change confirmation or action-visibility rules through this principle. |
| Paradox of the active user | Provide contextual help and actionable labels so a first-time user can proceed without having read a manual. Do not assume nobody reads documentation. |
| Contrast | Verify readable text, visible focus, and distinguishable controls against their actual backgrounds. Do not rely on color alone to convey meaning. |
| Typography | Use the existing heading/body/caption hierarchy and readable line lengths. Token values remain owned by `11_System_Design_Standards.md`. |
| Color and shape | Use consistent semantic cues and recognizable shapes/icons, supported by labels where meaning could be ambiguous. Do not invent or change palette values here. |

Content grouping, choice structure, and visual composition belong to `10_Frontend_UX_and_Layout_Standards.md`. Form recovery belongs to `04_Input_Validation_and_Form_Submission.md`; perceived-response and consistency review belongs to `12_UI_Quality_Checklist.md`.

## Forms
- Easy to scan: logical grouping, clear labels, required-field indicators.
- Validation errors are specific, field-level, and appear near the field they relate to — for null/missing/wrong input, show what's wrong and what's expected (see `04_Input_Validation_and_Form_Submission.md` for the full display rules and the null/missing/wrong-type matrix).
- Full keyboard navigation supported.
- Auto-compute derived fields where possible (e.g. age from birthdate input).
- Cascading address fields (province → city → barangay) driven by JSON data.

## Navigation
- Predictable placement, clear active states, minimal clicks to any destination.
- Breadcrumbs where the hierarchy is deep enough to need them.

## Components
- Always use the existing shared components for inputs, selects, tables, cards, buttons, modals, alerts, and other UI where they support the required behavior. Inspect `resources/views/components/` before adding markup.
- Extend a suitable component with focused props/slots when needed; create a shared component for a real reusable UI pattern that is missing. Use plain semantic markup for unique content; do not create a generic wrapper for every element.
- Every interactive component handles: hover, focus, active, disabled, loading, error, and empty states — not just the happy path.
- Disable buttons during processing to prevent double submit.
- Show clear status indicators: loading spinner, unsaved changes, pending changes.
- Choose feedback by the action's outcome and urgency using the guidance below.
- Confirm destructive or consequential actions before execution. Routine navigation and ordinary reversible edits do not need confirmation; follow the feature's submission requirements in `04_Input_Validation_and_Form_Submission.md`.

## Partials & Long Views

- Extract a coherent, page-specific section into a nearby `partials/` folder when it makes a long view easier to scan. Good candidates include triggered modals, off-canvas panels, drawers, and substantial secondary forms.
- Keep the main view focused on page structure and include named partials, passing their required data explicitly. Preserve existing module folder casing; use descriptive kebab-case names for new partials, such as `partials/edit-modal.blade.php`.
- A partial organizes page-specific markup; a component provides reusable UI through props/slots. A modal partial should use the shared modal component rather than duplicate its shell.
- Preserve Alpine/Livewire state ownership, trigger bindings, unique IDs/keys, validation targets, and keyboard/focus behavior when extracting. Do not query or mutate data inside partials.
- An include does not make rendering lazy: hidden content may still render and run queries. Add deferred loading only when needed and verify open, close, reopen, and submission behavior.
- Avoid splitting tiny fragments or deeply nesting includes solely to meet a line-count target.

## User Feedback: Toasts, Alerts & Inline States

Every action needs understandable feedback, but not every action needs a toast or alert. Backend logs serve a separate purpose; see `02_Planning_Conditions_and_System_Flows.md`.

| Situation | User feedback | Avoid |
|---|---|---|
| Explicit save, delete, submit, or completed upload | One brief success toast or visible success message after confirmed completion | Duplicate toast and alert for the same success; success before commit |
| Search, filter, pagination, opening/closing a modal, selecting a tab | Updated content, selection, loading indicator, or empty state | Routine success toasts |
| Autosave or polling | Quiet saving/saved status; persistent failure status with recovery if needed | A toast for every keystroke or poll |
| Field validation failure | Inline field errors; add a form summary when it helps locate errors | Toast-only validation or a mandatory toast for every invalid field |
| Short, recoverable action failure | One clear error toast when no persistent guidance is needed | Raw exceptions or duplicate messages |
| Blocking failure, outage, partial batch failure, or required next step | Persistent inline alert/banner with a clear explanation and recovery action | Auto-dismissing essential instructions |
| Destructive or consequential action requiring a decision | Confirmation dialog before execution; outcome feedback afterwards | Using a toast as confirmation |

- Reuse the established feedback components and delivery mechanism. Ensure messages survive redirects when required and are emitted once per action.
- Distinguish queued/accepted work from completed work; a queued job is not proof the operation succeeded.
- Make feedback accessible: announce status updates without stealing focus; keep essential errors available until resolved or deliberately dismissed. Avoid native browser `alert()` for routine feedback.

## Responsiveness
- Mobile-first, then tablet, then desktop.
- No horizontal scrolling or overflow at any breakpoint.
- Typography and spacing scale with viewport, not fixed pixel values.

## Accessibility (WCAG 2.2 AA)
- Semantic HTML first; ARIA only where semantics fall short.
- Visible focus indicators, sufficient color contrast, full keyboard operability.
- Screen-reader tested for any custom/interactive component (modals, dropdowns, tabs).

## Dashboards & Data
- Use clear, understandable charts and graphs — don't force data into a chart type that obscures it.
- Always handle empty states (no data found, empty list) — never show a blank area with no explanation.
- Avoid excessive animations — they cause lag on low-end devices.

## Visual Design
- Consistent typography scale, border radius, and shadow usage — no one-off values.
- Color hierarchy matches meaning (e.g. status colors mean the same thing everywhere in the app).
- Define the palette, typography scale, and shadow/elevation system as CSS custom properties (tokens) up front — new colors or spacing values should be added to the token set, not invented inline in a component.
- Minimum touch target size of 36px for interactive elements, especially where the primary users are non-technical or on shared/lower-spec devices.

## Rich Text / WYSIWYG Content
- Sanitize rich-text editor output before storing or rendering — editors (Quill, TipTap, etc.) often emit flat/non-semantic markup (e.g. a `data-list` attribute instead of real `<ul>`/`<ol>`) that needs converting to semantic HTML on save or render.
- Never render raw editor output with an unescaped/raw-HTML directive without passing it through a sanitizer first (see `03_Security_Guidelines.md` — Input Sanitization).

## Review Questions (ask these, don't assume the current UI passes)
- Would a first-time user understand this without explanation?
- Is the next action obvious?
- Can this be simplified — fewer steps, fewer fields, fewer clicks?
- Is anything here visually distracting or competing for attention?

## Priority Order When Trading Off
1. Usability
2. Clarity
3. Consistency
4. Accessibility
5. Responsiveness
6. Maintainability
7. Performance

---

# Layout & Information Architecture

Applies on top of `10_Frontend_UX_and_Layout_Standards.md`. Governs WHERE content goes and WHY — grouping, hierarchy, placement, and responsive behavior. Token and component decisions live in `11_System_Design_Standards.md` and `12_UI_Quality_Checklist.md`.

---

## Before Placing Anything

Classify every piece of content by role first:

| Role | Examples |
|---|---|
| Primary subject | Record name, course title, syllabus status |
| Supporting detail | Metadata, timestamps, secondary counts |
| Action | Buttons, links, form triggers |
| Status / state | Approval stage, account status, review result |
| Navigation / filter | Tabs, search, sort controls |

Then identify:
- **Clusters** — items that lose meaning when separated (a label and its value, a stat and its trend, a title and its primary action). Keep these together.
- **Independent units** — content that can be removed or hidden without breaking the rest. These are candidates for their own card, section, or collapsible region.
- **Variable content** — lists, optional fields, user-generated text. The layout must not break when these are empty, short, or very long.

---

## Grouping

- Group by **relationship**, not by visual convenience. Ask: if this element moved away from its neighbor, would the user lose context? If yes — grouped. If no — separate.
- Each group answers **one question** for the user: "who is this," "what's the status," "what can I do here." If a group answers two unrelated questions, split it.
- Use the lightest boundary that still reads clearly: whitespace first, then `<x-divider />`, then a card/panel. Do not add a border or box if spacing alone communicates the separation.
- Order groups by what the user needs to decide or act on **first** — not by source order or database column order.

---

## Hierarchy & Focal Point

- Every screen and every major section has **one focal point** — the thing the eye lands on first. Everything else supports it; nothing competes with it.
- Choose the scan pattern that matches the content:

| Content type | Pattern |
|---|---|
| Comparable items (stats, cards) | Grid — left-right, top-bottom |
| Sequential / process steps | Vertical flow or stepped path |
| Dashboard / status overview | F-pattern — key info top-left, detail below |
| Deep or nested content | Progressive disclosure — summary first, detail on demand |

- Secondary and tertiary content must be **both visually quieter and physically further** from the primary flow — not just smaller or greyed out in place. Move it to the edge, below the fold, or behind a toggle.

---

## Placement

- Place actions **next to what they act on** — inline edit, contextual menu, row-level delete. Centralize actions in a toolbar only when they apply to the whole page or a bulk selection.
- Alignment implies relationship without containers: items on the same edge or baseline read as related; indented items read as subordinate.
- **Anchor prominent elements** — the most visually dominant element in a section (a headline, illustration, or key stat) should sit at a corner or the visual center of its container, not float arbitrarily in the middle. If you're unsure where to start composing a section, place the anchor element first; everything else arranges around it.
- Sticky/fixed positioning (page header, action bar) is for elements the user needs **continuously across scroll**. Do not make things sticky just because they feel important.
- When multiple groups share a screen, define a mental reading order (1, 2, 3…) and verify the physical placement follows it.

---

## States — Design All of Them

Never design only the happy path. For every group or section, define:

| State | Requirement |
|---|---|
| **Empty** | Intentional message or prompt — never a blank box. Use the project's empty-state pattern. |
| **Loading** | Skeleton that mirrors the real layout shape so content doesn't reflow on arrival. |
| **Variable length** | Short text, long text, 1 item, 50 items — define truncation, wrapping, scroll, or pagination per content type. |
| **Conditional** | Which groups appear or disappear by role, permission, or data state. Design each meaningful combination, not just the default. |
| **Interaction-driven** | What expands, collapses, or reveals on click/hover/selection. Specify what stays anchored vs. what shifts. |

---

## Responsive — Reorganize, Don't Just Resize

Collapsing columns is not a responsive strategy. Define how groups **reorganize**:

- Do side-by-side groups stack, or does one move into a drawer/modal?
- Does a multi-column comparison become an accordion or a prioritized single list?
- Does secondary content relocate (sidebar → bottom, inline → behind a toggle) rather than just shrink?

Re-evaluate the focal point per breakpoint — the primary content on desktop may need to move higher on mobile.

Be explicit about the difference:
- **Hidden** — gone entirely at this breakpoint.
- **Collapsed** — accessible via a tap/toggle.
- **Relocated** — still visible, moved to a different position.

---

## Consistency

- Reuse the same grouping pattern for the same content type everywhere. All "record card with title + meta + action" patterns must be structured identically.
- Use the spacing scale from `11_System_Design_Standards.md` — do not invent one-off gap or padding values.

---

## Layout Type Selection

Choose the layout structure from the content's requirements, not from what looks impressive:

| Content type | Layout to consider |
|---|---|
| Focused landing page, single task | One-column — removes distraction, works mobile-first |
| Content + supporting navigation | Two-column — main area + sidebar |
| Two equally prominent options | Split-screen — equal weight, clear choice |
| Browsable collection (courses, cards) | Card/block — scannable grid |
| Mixed editorial content | Magazine — tamed with clear hierarchy, not a free-for-all |
| Long pages with persistent links | Fixed navigation — stays in place as the user scrolls |

- Never default to a layout because it's popular. Ask: does this structure match how users will consume this content?
- F-pattern and Z-pattern are scanning tendencies, not guaranteed reading paths. Clear headings and visual hierarchy are more reliable than forcing content into a letter shape.
- Set responsive breakpoints where the content breaks, not at arbitrary device widths. Start narrow and add columns when space genuinely allows it.

---

## Navigation Placement

- Keep primary navigation visible and above the fold. It should not require scrolling to find.
- Sticky navigation is justified only when the page is long enough that losing the nav creates real friction. Do not make it sticky just because it feels important.
- Collapsible (hamburger) menus conserve space on mobile but add an interaction step and reduce discoverability — use them on small screens, not as a default on desktop.
- Always include a footer menu, anchor links on long pages, and a "Back to Top" control when the page scrolls significantly.
- Navigation should lead users from entry point to goal in as few steps as possible. Every extra click is a reason to leave.

---

## Whitespace

- Whitespace is not empty space — it is a layout tool. It creates separation, guides reading flow, and reduces cognitive overload.
- Use whitespace to separate unrelated groups and to pull related items together. Tight spacing implies relationship; generous spacing implies independence.
- Strike a balance: too little whitespace creates clutter; too much creates disconnection and makes the page feel unfinished.
- Space out content into bite-sized sections. Walls of text with no breathing room drive users away before they read a word.
- Never use whitespace as a substitute for actual hierarchy — spacing alone does not make an element important.

---

## Principles for Content Structure and Composition

Use these to choose and explain a layout. They are not rigid laws or reasons to hide required information.

| Principle | Practical rule |
|---|---|
| Hick's Law; choice overload | Prioritize relevant choices and use grouping, filtering, or progressive disclosure when a large set is necessary. Preserve a discoverable path to the full set. |
| Chunking; proximity; common region | Place related labels, values, fields, and actions together. Use the lightest boundary that makes a meaningful group clear. |
| Uniform connectedness | Use connectors when they communicate an actual relationship or process; avoid lines that imply a relationship that does not exist. |
| Law of Pragnanz | Prefer a simple, recognizable structure that preserves the information needed for the task. |
| Serial position; selective attention | Put essential guidance in the task's reading/action path, near the relevant decision. Do not rely on list position alone to ensure it is noticed. |
| Balance | Distribute visual weight deliberately; check whether a large surface, image, or block of text overwhelms its neighbors. |
| Emphasis; proportion | Make the main subject or action more prominent than supporting metadata through existing size, weight, position, and contrast patterns. |
| Movement | Arrange headings, groups, and actions in a useful reading sequence. Scan patterns are tendencies, not a guaranteed eye path. |
| Framing | Keep the relevant subject and context together; choose boundaries and cropping that do not conceal required information. |

Full theory remains in `.amazonq/references/ux-laws.md` and `.amazonq/references/visual-design-principles.md`; the actionable requirements above are the main-rule counterpart.

## What Not to Do

- Do not default to a 3-column card grid because it fills the space — derive the pattern from the content's actual relationships.
- Do not treat spacing and sizing as the primary design decision; they support hierarchy and grouping, not the other way around.
- Do not leave any state (empty, loading, error, conditional) undesigned.
- If content relationships or priority are ambiguous, ask before assuming — grouping mistakes are expensive to undo.
