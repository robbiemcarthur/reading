# HTTP Status Codes Reference

When to use which status code. Organised by the decisions you'll actually face when building APIs, not just a list of numbers.

---

## The Decision Framework

Before looking up a specific code, ask yourself:
1. **Did the request succeed?** → 2xx
2. **Is the resource somewhere else?** → 3xx
3. **Did the client do something wrong?** → 4xx
4. **Did the server fail?** → 5xx

---

## 2xx — Success

### 200 OK
**When:** The request succeeded and you're returning data. The default success response for GET, PUT, and PATCH requests.

### 201 Created
**When:** A new resource was successfully created. Use for POST requests that create something. Include a `Location` header pointing to the new resource.
```
POST /api/gigs
→ 201 Created
→ Location: /api/gigs/abc-123
→ Body: { the created gig }
```

### 202 Accepted
**When:** The request has been accepted but processing hasn't completed yet. Use for asynchronous operations — "I've received your request and I'll work on it." In GigLedger, generating a tax report could return 202 with a link to poll for status.
```
POST /api/reports/generate
→ 202 Accepted
→ Body: { "statusUrl": "/api/reports/abc-123/status" }
```

### 204 No Content
**When:** The request succeeded but there's nothing to return. Use for DELETE requests and PUT/PATCH requests where you don't need to return the updated resource. Don't return a body with 204.
```
DELETE /api/expenses/abc-123
→ 204 No Content
```

---

## 3xx — Redirection

### 301 Moved Permanently
**When:** The resource has permanently moved to a new URL. Clients and search engines should update their links. Use sparingly in APIs — more common for web pages.

### 302 Found / 307 Temporary Redirect
**When:** The resource is temporarily at a different URL. 307 preserves the HTTP method (POST stays POST), 302 historically allowed method changes. Prefer 307 for API redirects.

### 304 Not Modified
**When:** Used with conditional requests (If-None-Match / If-Modified-Since). The resource hasn't changed since the client last fetched it — use the cached version. Relevant for GigLedger's dashboard endpoint if you implement ETags.

---

## 4xx — Client Errors

### 400 Bad Request
**When:** The request body is malformed, fails validation, or is otherwise unparseable. This is your catch-all for "your request doesn't make sense." Return a clear error message explaining *what* is wrong.
```
POST /api/expenses { "amount": -50 }
→ 400 Bad Request
→ Body: { "errors": [{ "field": "amount", "message": "Amount must be positive" }] }
```

**Common mistake:** Using 400 when you mean 422. Use 400 for syntactically invalid requests (bad JSON, wrong types). Use 422 for semantically invalid requests (valid JSON but business rules violated).

### 401 Unauthorized
**When:** The request lacks valid authentication credentials. The client hasn't identified themselves. Despite the name, this is about *authentication* not *authorisation*.
```
GET /api/gigs (no auth token)
→ 401 Unauthorized
→ WWW-Authenticate: Bearer
```

### 403 Forbidden
**When:** The client is authenticated but doesn't have permission to access this resource. They've identified themselves but they're not allowed in. "I know who you are, and the answer is no."
```
GET /api/users/other-user/gigs (authenticated as different user)
→ 403 Forbidden
```

**The 401 vs 403 distinction:** 401 = "who are you?" 403 = "I know who you are, but you can't do this."

### 404 Not Found
**When:** The resource doesn't exist. Also use when you don't want to reveal that a resource exists but the user isn't authorised — returning 403 would confirm the resource exists, 404 hides that information.
```
GET /api/gigs/nonexistent-id
→ 404 Not Found
```

### 405 Method Not Allowed
**When:** The HTTP method isn't supported for this endpoint. Include an `Allow` header listing valid methods.
```
DELETE /api/dashboard
→ 405 Method Not Allowed
→ Allow: GET
```

### 409 Conflict
**When:** The request conflicts with the current state of the resource. Use for concurrency conflicts, duplicate creation attempts, or state machine violations.
```
POST /api/gigs (duplicate gig reference)
→ 409 Conflict
→ Body: { "message": "A gig with reference GIG-2026-001 already exists" }

PUT /api/gigs/abc-123 (stale ETag / optimistic lock failure)
→ 409 Conflict
→ Body: { "message": "This gig has been modified since you last fetched it" }
```

### 415 Unsupported Media Type
**When:** The request Content-Type isn't supported. Spring Boot returns this automatically if you send XML to an endpoint that only accepts JSON.

### 422 Unprocessable Entity
**When:** The request is syntactically valid (well-formed JSON, correct types) but violates business rules. The distinction from 400: the server *understood* the request but *refuses* to process it because it's semantically wrong.
```
POST /api/expenses { "amount": 500, "taxYear": "2019-2020" }
→ 422 Unprocessable Entity
→ Body: { "message": "Cannot add expenses to a closed tax year" }
```

**When to use 400 vs 422:**
- `{ "amount": "not-a-number" }` → **400** (syntactically invalid)
- `{ "amount": 500, "category": "INVALID_CATEGORY" }` → **400** (invalid enum value)
- `{ "amount": 500, "taxYear": "2019-2020" }` where 2019-2020 is closed → **422** (valid data, business rule violation)

### 429 Too Many Requests
**When:** The client has sent too many requests in a given time period. Include a `Retry-After` header. Important for any public-facing API.
```
→ 429 Too Many Requests
→ Retry-After: 60
```

---

## 5xx — Server Errors

### 500 Internal Server Error
**When:** Something unexpected went wrong on the server. This is the catch-all for unhandled exceptions. Never leak stack traces or implementation details to the client in production.
```
→ 500 Internal Server Error
→ Body: { "message": "An unexpected error occurred", "traceId": "abc-123" }
```

### 502 Bad Gateway
**When:** Your service received an invalid response from an upstream service. In GigLedger Phase 2, if the Ledger Service calls the Tax Service and gets garbage back, return 502 to the frontend.

### 503 Service Unavailable
**When:** The server is temporarily unable to handle the request. Use during maintenance, overload, or when a circuit breaker is open. Include `Retry-After` if you know when it'll be back.
```
→ 503 Service Unavailable
→ Retry-After: 30
```

### 504 Gateway Timeout
**When:** Your service timed out waiting for an upstream service. In GigLedger, if the Tax Service takes too long to generate a report, the Ledger Service returns 504.

---

## GigLedger Quick Reference

| Operation | Success | Common Errors |
|-----------|---------|---------------|
| Create gig | 201 + Location header | 400 (validation), 401 (not authed), 409 (duplicate) |
| Get gig | 200 | 401, 404 |
| Update gig | 200 (with body) or 204 (without) | 400, 401, 404, 409 (stale update) |
| Delete gig | 204 | 401, 403, 404 |
| List gigs | 200 (even if empty list) | 401 |
| Record expense | 201 | 400, 401, 422 (closed tax year) |
| Generate report | 202 (async) | 401, 422 (incomplete data) |
| Dashboard | 200 | 401 |

---

## Error Response Format

Use a consistent error format across all endpoints. RFC 9457 (Problem Details) is the standard:

```json
{
  "type": "https://gigledger.dev/errors/closed-tax-year",
  "title": "Tax year is closed",
  "status": 422,
  "detail": "Cannot add expenses to tax year 2019-2020 as it has been submitted",
  "instance": "/api/expenses",
  "traceId": "abc-123-def-456"
}
```

Spring Boot 3.x supports Problem Details natively — enable it with `spring.mvc.problemdetails.enabled=true`.

---

*Add your own entries below as you encounter edge cases.*

#practice #spring
