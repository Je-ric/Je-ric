# API Standards

## Standard JSON Response Structure

For application JSON APIs, preserve the established response contract. Use the shape below for new endpoints unless an integration specifies another contract. Do not wrap Livewire responses, HTML redirects, streams, or bodyless 204 responses in this envelope.

```json
{
  "success": true,
  "message": "User created successfully.",
  "data": { "id": 123 },
  "errors": null
}
```

```json
{
  "success": false,
  "message": "Validation failed.",
  "data": null,
  "errors": {
    "email": ["The email field is required."],
    "name": ["The name field must not exceed 255 characters."]
  }
}
```

---

## HTTP Status Codes

| Code | Meaning | When to use |
|---|---|---|
| 200 | OK | Successful GET, PUT, PATCH |
| 201 | Created | Successful POST (resource created) |
| 204 | No Content | Successful DELETE (nothing to return) |
| 400 | Bad Request | Malformed request syntax |
| 409 | Conflict | Conflicting resource state or concurrent update |
| 401 | Unauthorized | Not logged in |
| 403 | Forbidden | Logged in but no permission |
| 404 | Not Found | Resource does not exist |
| 422 | Unprocessable Entity | Validation errors |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Server Error | Unexpected server-side failure |

---

## HTTP Methods

| Method | Purpose | Example |
|---|---|---|
| GET | Read / fetch | `GET /api/users` |
| POST | Create | `POST /api/users` |
| PUT | Full update | `PUT /api/users/1` |
| PATCH | Partial update | `PATCH /api/users/1` |
| DELETE | Delete | `DELETE /api/users/1` |

---

## Route Naming (REST)

| Action | Method | Route | Route Name |
|---|---|---|---|
| List all | GET | `/api/users` | `users.index` |
| Show one | GET | `/api/users/{id}` | `users.show` |
| Create | POST | `/api/users` | `users.store` |
| Update | PUT/PATCH | `/api/users/{id}` | `users.update` |
| Delete | DELETE | `/api/users/{id}` | `users.destroy` |

---

## Request Validation

Use `04_Input_Validation_and_Form_Submission.md` for field contracts, Laravel rule syntax, and validation placement. Preserve native Blade/Livewire validation responses; this guide owns application JSON API contracts.

---

## Rules

- Always validate on the server — never trust client input
- Follow the response contract and exceptions defined above
- Never expose raw DB errors to the client
- Never expose IDs in public-facing responses if not needed
- Use pagination for list endpoints — never return all records unbounded
- Use queues for emails and heavy operations — never block the response
- Rate limit sensitive endpoints (login, OTP, password reset)
- For endpoints accepting image/file uploads: enforce the max size limit server-side, create the destination folder if it doesn't already exist, then store the upload and rename it based on the upload context (e.g. {context}_{uuid}.{ext}) rather than the original filename (see 03_Security_Guidelines.md for upload safety and 08_Naming_and_File_Organization.md for the rename pattern).
