# Security Guide

## Validation and output

- Validate type, length, format, allowed values, and business rules on the server. Client checks provide feedback only.
- For fields whose contract forbids HTML, reject invalid input with a field error; do not silently strip tags and save altered content.
- Normalize only where the field contract allows it. Do not trim passwords or opaque tokens.
- Do not use SQL/script keyword blacklists as an injection defense. They miss attacks and reject legitimate prose or course content. Existing `SecurityValidator` / `NoInjectionRule` helpers are not proof that an input is safe.
- Escape for the output context: Blade `{{ }}` for HTML text, `@js()` for PHP values in JavaScript, and `textContent` / `x-text` for DOM text. HTML escaping alone does not make a URL or JavaScript expression safe.
- Render rich HTML only after a maintained server-side sanitizer with an explicit allowlist. Do not add a sanitizer dependency without checking existing packages.
- Never pass untrusted strings to `innerHTML`, `x-html`, `eval`, or dynamically constructed event-handler code. Validate URL schemes and destinations separately.

## Queries and access control

- Bind query values. Eloquent and Query Builder raw expressions are not automatically safe; never concatenate user input into SQL.
- Allowlist dynamic column names and sort directions; placeholders cannot bind SQL identifiers.
- Authenticate protected requests and authorize the specific record and action, including department/program ownership. Route roles and hidden buttons are insufficient alone.
- Recheck authorization in every callable Livewire action; `mount()` checks alone do not protect subsequent actions. Treat public properties, action arguments, and hidden inputs as user-controlled.
- Validate accepted attributes and explicitly select what may be persisted. Mass-assignment configuration is additional protection, not authorization.
- Hash passwords using Laravel's hashing facilities. Regenerate the session after authentication; invalidate it and regenerate the CSRF token on logout.

## CSRF and authentication modes

- Include `@csrf` in ordinary state-changing Blade forms and use the framework's CSRF handling for Livewire/AJAX.
- Cookie-authenticated requests require CSRF protection, including Sanctum SPA sessions. Bearer-token-only APIs have a different threat model; do not exempt a route simply because it uses Sanctum.
- Never mutate records through GET requests.

## Uploads, secrets, and external input

- Validate server-detected file type, size, and allowed extension against the feature's requirements. Generate storage names; never use a supplied filename as a storage path.
- Store sensitive uploads privately, authorize downloads, and prevent uploaded content from executing. Apply malware scanning when required by the upload workflow.
- Do not expose secrets, credentials, OTPs, session cookies, or personal data in logs, responses, command output, or commits.
- Use HTTPS in production. Restrict redirects and server-side URL fetching to approved destinations where applicable.
- Avoid shell execution with user input; prefer APIs and structured argument lists. Never treat escaping or a blacklist as permission to execute arbitrary input.

## Abuse and browser defenses

- Rate limit login, OTP/reset, email, public submission, and expensive actions with limits appropriate to their cost. Use named limiters with explicit user/IP and action keys; enforce Livewire limits on the action path.
- Rate limiting, disabled buttons, and client confirmation flags do not guarantee idempotency. See `04_Input_Validation_and_Form_Submission.md` and `05_Database_Standards.md`.
- Introduce CSP through compatibility testing with the installed Alpine/Livewire stack; test a report-only policy before enforcement. Do not break existing interactions by copying a generic policy.

## Review evidence

Verify denied access, tampered record IDs, invalid payloads, and relevant upload/rendering boundaries. Record observed results; do not label existing helpers or components secure without reviewing them.

References: [OWASP input validation](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html), [Livewire 4 security](https://livewire.laravel.com/docs/4.x/security), [Laravel 12 Sanctum](https://laravel.com/docs/12.x/sanctum).
