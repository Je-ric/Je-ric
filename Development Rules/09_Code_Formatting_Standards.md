# Code Formatting Standards

This guide owns spacing, indentation, braces, wrapping, and visual code layout. `07_Coding_Principles_and_Standards.md` owns implementation approach; `08_Naming_and_File_Organization.md` owns names and imports.

## Shared Baseline

- Follow `.editorconfig`: UTF-8, LF, spaces, and a final newline. Use four spaces for PHP, Blade/HTML, JavaScript, and CSS; YAML uses two except the existing docker-compose exception.
- Format only requested/touched sections; avoid unrelated whole-file churn.
- Remove trailing whitespace. End files with one newline. Use one blank line between methods and major logical/markup sections; avoid repeated blank lines.
- Aim for approximately 120 characters per line. Wrap syntax at argument, array-item, operator, or attribute boundaries; never split string values or whitespace-sensitive rendered text just to meet a width.
- Separate statements and block-level elements/directives onto their own lines. Short inline markup may stay inline when needed to preserve text spacing.

## PHP

- Classes and methods use opening braces on a new line; control structures use braces on the same line.
- Long arrays/argument lists use one item per line with a trailing comma where allowed.
- Avoid alignment padding: use `'key' => value` rather than `'key'   => value`.

### Method Chain Formatting

- **Break long method chains onto separate lines**: Each `->call()` should be on its own line, indented one level under the first line.
- **Example**:
  ```php
  // Before
  $users = User::where('active', true)->orderBy('name')->get();

  // After
  $users = User::where('active', true)
      ->orderBy('name')
      ->get();
  ```
- **Single-line chains are fine**: If the chain is short and fits comfortably on one line (~120 characters or less), keep it on one line.

### Named Argument Calls (including dispatch)

- **Single-line when it fits**: Keep named-argument calls on one line if they stay within ~120 characters:
  ```php
  $this->dispatch('lw-toast', type: 'success', message: 'Faculty response marked as verified.');
  ```
- **Break when long**: If the call exceeds ~120 characters, put each named argument on its own line:
  ```php
  $this->dispatch(
      'lw-toast',
      type: 'error',
      message: 'A very long message that explains what went wrong and what the user should do next.',
  );
  ```

### File-Level Structure Order

New PHP class files follow this order (preserve any `declare(strict_types=1);` before the namespace):

1. `<?php` opening tag
2. `namespace` declaration
3. Blank line
4. `use` import block; separate it from the namespace and class with blank lines
5. Blank line
6. Class declaration
7. Class body: constants → properties → `__construct` → public methods → protected methods → private methods

Do not mix `use` imports with class body content. Preserve existing method and import order during formatting-only work.

### `match` Expression Formatting

- Each arm on its own line, indented one level inside the `match`.
- Trailing comma on the last arm.
- Closing `}` aligned with the opening `match`:
  ```php
  $label = match ($status) {
      'draft' => 'Draft',
      'submitted' => 'Submitted',
      'approved' => 'Approved',
      default => 'Unknown',
  };
  ```
- Short single-arm `match` may stay on one line if it fits within ~120 characters.

### Multiline `if` Conditions

- When a condition exceeds ~120 characters, break at each `&&` or `||` operator, placing the operator at the **start** of the continuation line:
  ```php
  if ($syllabus->isSubmitted()
      && $user->hasRole('dean')
      && $syllabus->college_id === $user->college_id
  ) {
      // ...
  }
  ```
- The closing `) {` goes on its own line, aligned with the opening `if`.
- Do not break conditions that fit on one line.

## Blade

### Indentation and Structure

- **One element or directive per line**: Each block-level element, Blade directive, and component starts on its own line.
- **Nesting**: Indent children one level under their parent. Closing tags and `@end...` directives align with their opening line.
- **Directive bodies**: Content inside `@if`, `@foreach`, `@forelse`, `@isset`, `@can`, `@auth`, `@section`, `@push`, etc. is indented one level. `@else`, `@elseif`, `@empty`, and `@end...` align with the opening directive.
- **Directive spacing**: One space between the directive name and its parenthesis: `@if ($user)`, `@foreach ($items as $item)`.
- **Echo spacing**: One space inside the braces: `{{ $name }}`, `{!! $html !!}`.
- **Slots**: `<x-slot:name>` and `<x-slot name="...">` are indented one level under the component.
- **Inline elements stay inline**: Short inline elements (`<span>`, `<a>`, `<strong>`, `<em>`, icons) stay on the same line as their text when it fits. Do not move inline content onto separate lines if that would add whitespace to the rendered output.

### Attribute Formatting

- **Break long tags**: If an opening tag or component exceeds ~120 characters, put each attribute on its own line, indented one level under the tag name.
- **Closing bracket**: Put `>` or `/>` on its own line, aligned with the opening `<`.
- **Short tags stay on one line**: If the tag fits comfortably within ~120 characters, keep it on one line.
- **Self-closing components**: Use a space before the slash: `<x-divider />`.
- **Attribute order for new markup**: Preserve existing attribute order when formatting. For new markup, use this order — native HTML attributes first, then bound props, then `wire:*`, then `x-*`, then event shorthands (`@click`, etc.):
  ```blade
  <x-card-panel
      title="Course Outcomes"
      class="mb-4"
      :collapsible="true"
      wire:key="co-panel-{{ $course->id }}"
      x-data="{ open: false }"
      @click="open = !open"
  >
  ```
- **`wire:model` modifier variants must not be changed**: `wire:model`, `wire:model.live`, and `wire:model.blur` have different behavior. Do NOT change the modifier or remove it during formatting.
- **Do NOT reformat attribute values**: Leave `class="..."`, `x-data="..."`, `x-on`/`@click` expressions, and `wire:model` values exactly as written, even when long. Do not split Tailwind class strings across lines.

### Embedded PHP, Script, and Style

- **`@php` blocks**: Put `@php` and `@endphp` on their own lines, with the PHP indented one level inside. Apply the PHP Code Formatting Standards to the code inside (method chains, array breaks, one statement per line).
- **Single-line `@php(...)`**: Leave as-is.
- **`<script>` and `<style>` blocks**: Indent the contents one level under the tag. Apply 4-space indentation inside. Do not change logic or reorder anything.
- **Long expressions in directives**: If a directive's expression is very long, break it onto multiple lines at an argument or array boundary, with the closing parenthesis on its own line.

### `@props` Formatting

- Keep component declarations such as `@props` in the preamble before markup, after any existing imports. Formatting-only work does not move declarations.
- Format as a multiline array when there are more than 2 props:
  ```blade
  @props([
      'title',
      'collapsible' => false,
      'class' => '',
  ])
  ```
- Single or two-prop declarations may stay on one line if they fit within ~120 characters.

### `@class` and `@style` Formatting

- When `@class([...])` has more than 2 conditions, break onto multiple lines — one condition per line, closing `])` on its own line:
  ```blade
  <div @class([
      'base-class',
      'is-active' => $active,
      'has-error' => $errors->has('field'),
  ])>
  ```
- Short `@class` with one or two conditions may stay on one line.
- Apply the same rule to `@style([...])`.

### `x-data` Formatting

- Do NOT reformat `x-data` attribute values, even when the object is long. Reformatting Alpine object syntax inside an HTML attribute can break parsing.
- If an `x-data` expression is unreadably long, note it after the code — do not change it.

### Do Not Touch

- Content inside `<pre>`, `<textarea>`, and `@verbatim` blocks.
- Whitespace-sensitive inline text (spaces between inline elements, text inside buttons and links).
- Blade comments `{{-- --}}` and HTML comments. Keep them where they are.
- `@json(...)`, `@js(...)`, and `@class([...])` / `@style([...])` contents, other than line breaks between array items when already multi-line.

## Formatting-Only Boundaries

These restrictions apply to formatting-only tasks; they do not prohibit separately authorized implementation changes.

- Preserve logic, behavior, rendered output, statement/method order, and array-key order.
- Do not rename variables, methods, classes, components, routes, keys, IDs, or strings. Do not rewrite imports as a whitespace operation.
- Preserve string contents and quote styles, comments, attribute order, directives, elements, slots, and event expressions.
- Do not change escaping mode, `wire:model` modifiers, `wire:key`, Alpine state, or bound values.
- Do not add types, docblocks, accessibility attributes, declarations, or other implementation changes while formatting.
- Preserve whitespace-sensitive content, especially inline text, `<pre>`, `<textarea>`, and `@verbatim`.
- Report an observed bug or refactor separately; do not silently include it in formatting work.
