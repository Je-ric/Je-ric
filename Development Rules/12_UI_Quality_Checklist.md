# Avoiding the AI-Generated Look

Before writing any UI, run this file as a checklist. Generic-looking output is a bug, not a style preference.

---

## Step 0 — Read the Project First (Required)

Do this before writing a single class. Do not skip it.

- Read `resources/css/` — Tailwind v4 theme, CSS custom properties, brand tokens from `11_System_Design_Standards.md`.
- Inspect existing Blade components: `x-card-panel`, `x-divider`, `x-modal`, `x-status-indicator`, `x-offcanvas`, `x-tab-switcher`, page headers, tables, badges, buttons.
- Open two or three existing pages similar to what you are building and extract:
  - Exact color tokens and CSS variables in use (never hardcode hex values).
  - Surface patterns: backgrounds, borders, ring styles, shadows.
  - Gradients: copy direction, color stops, and placement exactly. Do not invent new gradients. Do not place gradients on elements where no existing component uses them.
  - Radius values from the scale in `11_System_Design_Standards.md` — `--radius-xs` through `--radius-full`.
  - Hover, focus, active, and transition styles as they exist.

If a needed element has no existing pattern, build it from the same tokens and proportions. Say so explicitly in your output. Never default to a generic SaaS look.

---

## Icons and Copy

- **Icons:** Boxicons only — `bx`, `bxs`, or `bxl` classes. Match the style (regular vs solid) used by the surrounding components.
- **No emojis** — not in UI, copy, buttons, empty states, toasts, comments, or documentation.
- Icon size matches the adjacent text line height. Icon-only buttons require an `aria-label` and a minimum 36px hit area (44px preferred).
- **Real copy only.** No "Supercharge your workflow," "Welcome back 👋," or placeholder adjectives. State what the element does, in plain words.

---

## AI Tells — Spot and Remove These

Each item below is a pattern that signals generated output. If any appear in your work, fix them before delivering.

| Tell | Fix |
|---|---|
| Decorative gradient blobs, glows, or hero backgrounds not used by any existing component | Remove. Use the surface and border tokens. |
| One random color per card (blue card, green card, purple card) | Color carries meaning only — status, primary action, or selection. Use the semantic palette from `11_System_Design_Standards.md`. |
| All cards identical in size, weight, and badge | Establish one primary element; secondary ones are visually quieter. The eye should land once. |
| Shadows on every element | Shadows only on floating layers (modals, menus, popovers) and where existing components already use `--shadow-card`. |
| Oversized or inconsistent rounded corners | Use the radius scale: `--radius-xs` (6px) through `--radius-xl` (20px). Inner radius = outer radius minus padding. |
| Two equal-weight buttons side by side | One primary (Flat style); the other is a secondary outline or text link. Never two Flat buttons competing. |
| Three-feature-card row with icon in a circle | Show real data or the real product UI instead. |
| Numbers without context | Always name the period, use tabular figures (`font-variant-numeric: tabular-nums`), right-align numbers, left-align text. |
| Status as a colored pill on every element | Use `x-status-indicator` — the project's established status pattern. |
| Button color fades on hover (opacity transition) | Opacity fade reads as "disabled," not "interactive." Hover must change the surface color (background shift using the 600-step token from `11_System_Design_Standards.md`), not opacity. Transition under 150ms, no fade. |
| Fade-in on scroll animations | No functional purpose in a data-heavy system. Every animation must earn its place — if it doesn't communicate state or guide attention, remove it. |
| Serif italic accents scattered for "personality" | Serifs and italics are valid for pull quotes or editorial callouts — not as a default heading style. Any decorative type choice must come from the established type scale in `11_System_Design_Standards.md` and have a stated reason. |
| Single font, single weight, single size everywhere | The tell is not the font — it's the absence of hierarchy. Establish a clear type scale: heading weight/size, body weight/size, caption weight/size. Every level must be visually distinct. See `11_System_Design_Standards.md` typography tokens. |
| Eyebrow badge above every section heading | A small label pill above a headline (`NEW` → `Hero Title`) is a valid pattern — once, for a genuine announcement. Using it on every card title or section header is the tell. Apply it at most once per page, only when the label carries real information. |
| Em dashes in UI labels, button text, or short copy | Em dashes belong in prose only. Never use them in labels, button text, empty-state messages, or toast copy. Plain words only in UI strings. |
| Color-bordered cards as decoration | A colored border on a card must mean something — selection state, error, warning, or brand emphasis. A border that is purely decorative with no semantic meaning is a tell. Use `--border-default` for neutral cards. |
| Elements not aligned to the grid | Align all elements to a 4px grid. Spacing between elements must come from the scale in `11_System_Design_Standards.md` — not arbitrary values. Misaligned elements break the sense of modularity even when individual components look correct. |

---

## States and Behavior

Every list, table, and card must have all three states designed — not just the populated happy path:

- **Empty state:** intentional message or prompt, never a blank box.
- **Loading state:** skeleton that mirrors the real layout shape.
- **Error state:** clear message with a recovery path.

**Buttons:**
- Disable on click; show a spinner inside with locked width (no layout shift).
- Re-enable only when the request resolves.
- Use `wire:loading` and `wire:target` scoped to the specific action — not a page-wide loading flag.

**Forms:**
- Validate on blur; switch that field to live validation once it is wrong.
- Show a valid state when corrected.
- Never use a silently disabled submit button — validate on click and highlight the blocking fields.

**Destructive actions:**
- Prefer undo over a confirm dialog.
- Typed confirmation only for truly irreversible actions (e.g. permanent deletion).

**Hover:**
- Never hide primary actions behind hover.
- Guard with `@media (hover: hover) and (pointer: fine)`.
- Touch targets: minimum 36px, 44px preferred.

**Tables on mobile:** restructure the row layout — do not shrink or horizontally scroll.

**Long text:** `min-w-0` on shrinking flex children, `truncate` for single-line overflow, middle truncation for filenames and emails, `break-words` for URLs.

**Transitions:** under 150ms, no bounce or overshoot.

**State ownership:** Alpine handles client UI state only. Business logic stays in Livewire/Laravel.

**Accessibility:** semantic markup, visible focus rings, labels on every input.

**Mobile input focus:** when a form field receives focus on mobile, the keyboard must not cover it. Use `scrollIntoView()` or ensure the layout accounts for `env(keyboard-inset-height)` / `env(safe-area-inset-bottom)`. Test every form on a real mobile viewport.

**Error messages:** never expose raw technical errors to the user — no stack traces, no `undefined is not an object`, no HTML-encoded strings like `&quot;`. Every error state shows a plain-language message with a recovery action. Raw errors are logged server-side only (see `02_Planning_Conditions_and_System_Flows.md`).

**Dark mode contrast:** dark mode is not a color inversion. Every dark-mode surface must pass WCAG AA contrast (4.5:1 for text, 3:1 for UI components). Check against the token values in `11_System_Design_Standards.md` — do not assume a darkened palette is automatically accessible.

---

## Numeric Data

- Use tabular figures (`font-variant-numeric: tabular-nums`) so stacked digits align in a column.
- Right-align numeric columns; left-align text columns.
- Abbreviate large numbers (1k, 1m) by default; show full precision on hover.
- Use relative time for recent events ("2 hours ago") and absolute dates for older ones.
- Keep currency symbols out of the aligned number column — align on the decimal, not the symbol.
- Always name the time period next to a metric. A bare "+12.5%" communicates nothing.

---

## Corner Radius

- Derive inner radius from outer radius minus padding (e.g. 12 outside, 8 padding → 4 inside) so corners stay concentric. Never reuse the outer radius for an inset element.
- Map every element to the existing scale from `11_System_Design_Standards.md` (`--radius-xs` through `--radius-full`). Do not invent one-off values.
- Use `--radius-full` only for single-line elements (chips, avatars, toggles). Multi-line cards get a real numeric radius.
- An element touching the container boundary on one side has no corner on that side.
- For selection rings, add the ring gap to the base radius (e.g. 8 base + 2 gap → 10 ring) instead of reusing the same value.
- When an inset image sits inside a rounded card, either let the card clip it or subtract the card's padding from the card radius to get the image's own radius.

---

## Avatars and Fallbacks

- Fall back from photo → initials → generic icon, in that order. A broken image icon is never an acceptable default.
- Derive the background color deterministically from the person's name so it is identical everywhere, every time.
- Use two initials (first + last) by default; drop to one letter only at the smallest sizes.
- For groups, overlap avatars and cap the visible row with a "+N" overflow indicator.
- Show online/status via a ring around the avatar — never as a second badge stacked on top.
- Standardize on a small fixed set of sizes (e.g. 24 in lists, 32 in headers, 40 in profiles).

---

## Data Tables

**Desktop**
- Right-align numbers with tabular figures; left-align text. Centered numbers break comparison.
- Replace zebra stripes with a single hairline between rows plus a hover state. Reserve stripes for genuinely wide rows.
- Offer 2–3 explicit row-height modes (Compact / Default / Comfortable) via a toggle when density matters.
- Pin the first column and the header row so context survives scroll.
- Use a dash for genuinely empty cells — never leave ambiguity between "missing" and "zero."
- Truncate long text with a hover tooltip; never truncate numbers.
- Reveal row actions on hover/focus (desktop) or a kebab menu (touch), not as a permanent column.
- Show sort arrows only on the active column.

**Mobile**
- Restructure rows — do not shrink or horizontally scroll. Stack each row as two lines: name left / primary value right on line one; status and date on line two.
- Drop low-value columns (raw IDs, secondary metadata) first.
- Give every card a single fixed slot for the primary value (top right), using tabular figures.
- Label ambiguous values (a bare date) but never label self-evident ones (a dollar amount).
- Move sorting into a dedicated control instead of relying on tappable column headers.
- Reveal full detail via a bottom sheet on row tap.
- Use a container query keyed to the table's own width rather than the viewport breakpoint.

**Bulk selection**
- A header checkbox has three real states: checked, empty, and indeterminate. Implement all three.
- "Select all on this page" and "select all matching records" are different actions — label them differently.
- Selection state survives pagination and filtering; it belongs in app state, not just the rendered rows.

---

## Visual Hierarchy (per page)

Before delivering any page or section, answer these:

- What is the single most important action or piece of information? Is it the most visually dominant element?
- Does every CTA stand out from surrounding content by color, size, or position — not just by being a button shape?
- Are secondary and tertiary elements visually quieter and physically further from the primary flow?
- Does the page have one focal point, or are multiple elements competing at the same visual weight?

---

## Usability and Visual Review

The detailed rules live with their topic owners; use this list to verify the affected UI rather than copying the full theory into each component.

- [ ] **Von Restorff effect:** the important information is distinguishable; emphasis is selective rather than applied to everything. Check layout hierarchy in `10_Frontend_UX_and_Layout_Standards.md`.
- [ ] **Aesthetic-usability effect:** verify task completion and error recovery, not visual polish alone.
- [ ] **Doherty threshold:** provide prompt, truthful feedback while work runs. Measure actual latency where performance is at issue; a universal millisecond promise is not a requirement.
- [ ] **Flow:** preserve useful task context during updates and avoid unnecessary interruptions, while retaining existing confirmation and authentication requirements.
- [ ] **Cognitive bias:** labels, defaults, and outcome messages describe the actual choice or result; they do not imply an operation completed before it did.
- [ ] **Similarity and repetition:** equivalent controls and information use consistent treatment across affected screens, following the existing design system.
- [ ] **Rhythm:** spacing, alignment, and repeated groups form a coherent reading pattern rather than arbitrary gaps.
- [ ] **Variety:** differences in visual treatment have a task-related purpose rather than adding decoration to every item.
- [ ] **Unity:** type, spacing, color meaning, and component treatment belong to the same established system. Token values remain unchanged.
- [ ] Grouping/composition follows `10_Frontend_UX_and_Layout_Standards.md`; readable contrast/type and understandable controls follow `10_Frontend_UX_and_Layout_Standards.md`; progress and draft recovery follow `04_Input_Validation_and_Form_Submission.md`.

## Self-Check — All Must Pass Before Delivering

- [ ] Every color, gradient, radius, and shadow comes from an existing token or component in `11_System_Design_Standards.md`.
- [ ] All icons are Boxicons. Zero emojis anywhere.
- [ ] There is one clear focal point and a readable hierarchy.
- [ ] Empty, loading, and error states are all handled.
- [ ] Numbers use tabular figures, name their period, and align correctly.
- [ ] The layout holds on mobile without hiding or shrinking data.
- [ ] No decoration exists without meaning.
- [ ] No two primary buttons compete side by side.
- [ ] Status uses `x-status-indicator`, not a one-off colored pill.
- [ ] No button uses an opacity fade on hover — hover changes background color only.
- [ ] No scroll-triggered animations exist without a functional reason.
- [ ] Type hierarchy is visible — at least two distinct levels (heading vs body) are present.
- [ ] No em dashes appear in labels, button text, or short UI copy.
- [ ] Every error state shows a plain-language message, not a raw technical string.
- [ ] Mobile: form fields are not covered by the keyboard when focused.
- [ ] Dark mode surfaces (if present) pass WCAG AA contrast — verified against `11_System_Design_Standards.md` tokens.
- [ ] No broken links. All `href`, `route()`, and named routes resolve.
- [ ] Copyright year (if present) is dynamic, not hardcoded.
- [ ] No inner radius reuses the outer radius without subtracting padding.
- [ ] Avatar fallback chain is photo → initials → icon. No broken image state.
- [ ] Table number columns are right-aligned; text columns are left-aligned.
- [ ] Empty table cells use a dash, not a blank.
- [ ] Row actions are revealed on hover/kebab, not as a permanent column.
- [ ] Mobile tables restructure rows — no horizontal scroll.
- [ ] Bulk selection header checkbox implements the indeterminate state.

---

## Output Format

Deliver the code, then a short report:
1. Which existing components and tokens were reused.
2. Any new pattern created and why no existing component covered it.
3. Which AI tells were found and removed.

If the design direction is unclear, ask before writing markup.
