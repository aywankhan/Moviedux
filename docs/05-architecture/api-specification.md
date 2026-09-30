# API Specification

## 1. Purpose

This document defines the required API surface for the approved MovieDux product scope. It is based strictly on the confirmed business, functional, use-case, business-rule, data-model, and architecture requirements.

The product supports:
- movie discovery and search,
- viewing movie results,
- watchlist creation and management,
- duplicate prevention,
- clear empty states,
- and repeat usage over time.

This specification is intentionally limited to the approved product behavior and does not prescribe any specific frontend or database implementation.

---

## 2. Scope

The API surface covers the following capabilities:
- catalog search and retrieval,
- retrieving watchlist contents,
- adding a movie to a watchlist,
- removing a movie from a watchlist,
- checking whether a movie is already saved,
- standardized API error behavior,
- pagination, filtering, sorting, and versioning.

Out of scope for this specification:
- social features,
- recommendation engines,
- user profiles beyond watchlist ownership context,
- payment or subscription APIs,
- advanced analytics endpoints,
- any features not confirmed by the approved requirements.

---

## 3. API Conventions

### Base URL
- `/api/v1`

### Response format
All JSON responses shall use the following conventions:
- success responses: `200`, `201`, or `204`
- error responses: `400`, `401`, `403`, `404`, `409`, `422`, `429`, `500`
- content type: `application/json`

### Common response envelope
Standard response envelope for all successful payloads:

```json
{
  "data": {},
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:00:00Z"
  }
}
```

Error envelope:

```json
{
  "error": {
    "code": "MOVIE_ALREADY_SAVED",
    "message": "The movie is already in the watchlist.",
    "details": [
      {
        "field": "movie_id",
        "issue": "Duplicate entry is not allowed in the same watchlist."
      }
    ]
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:00:00Z"
  }
}
```

---

## 4. Error Response Format

### Error structure
| Field | Type | Required | Description |
|---|---|---:|---|
| error.code | string | Yes | Machine-readable error code. |
| error.message | string | Yes | Human-readable summary. |
| error.details | array | No | Field-specific validation or business-rule details. |
| meta.request_id | string | Yes | Correlation identifier for troubleshooting. |
| meta.timestamp | string | Yes | ISO-8601 timestamp. |

### Standard error codes
- `VALIDATION_ERROR`
- `UNAUTHORIZED`
- `FORBIDDEN`
- `NOT_FOUND`
- `DUPLICATE_ENTRY`
- `EMPTY_RESULT`
- `RATE_LIMITED`
- `SERVER_ERROR`

### Error status mapping
- 400: validation or malformed request
- 401: authentication required or invalid credentials
- 403: authorized user but insufficient permission
- 404: item not found
- 409: duplicate or conflicting state
- 422: semantically invalid request
- 429: too many requests
- 500: unexpected server error

---

## 5. Authentication and Authorization

### Authentication mechanism
The approved requirements do not yet confirm whether the watchlist belongs to:
- an authenticated user,
- an anonymous session-based user, or
- a future identity model.

Therefore, the API contract supports both patterns without forcing a decision:

- Option A: authenticated user token (for example, bearer token)
- Option B: session identifier / anonymous user context

This is an open decision at the product level, not a hard implementation requirement.

### Authorization model
- Catalog search is generally permitted for anonymous or guest users if the product is public.
- Watchlist operations require access to the current owner’s watchlist context.
- A user must only be able to read or modify their own watchlist.
- If the business later adds authentication, authorization must prevent cross-user access.

### Authorization rules
- Users may not modify another user’s watchlist.
- Watchlist access must be tied to the current owner or session context.
- Duplicate prevention is enforced within the same watchlist only.

---

## 6. Pagination, Filtering, Sorting, and Search

### Pagination
Pagination is required for catalog searches and watchlist retrieval where the result set may exceed one page.

Request parameters:
- `page`: integer, default `1`
- `limit`: integer, default `20`, maximum `100`

Response pagination object:

```json
"pagination": {
  "page": 1,
  "limit": 20,
  "total_items": 42,
  "total_pages": 3,
  "has_next": true,
  "has_previous": false
}
```

### Filtering
The product requires search and result filtering. Filtering supports the following patterns:
- `title` or `query`
- `genre`
- `release_year`
- `is_active`

### Sorting
Supported sorting fields:
- `title`
- `release_year`
- `created_at`
- `updated_at`

Sorting direction:
- `asc`
- `desc`

### Search behavior
Search must support:
- exact match,
- partial match,
- clear no-results states,
- and recovery after empty results.

Search request examples:
- `?query=midnight`
- `?genre=Drama&sort=title&order=asc`
- `?query=run&page=2&limit=10`

---

## 7. API Endpoints

## API-001: Search Movies

- API ID: API-001
- HTTP method: GET
- Endpoint: `/api/v1/movies/search`
- Purpose: Search the movie catalog and return matching results.
- Actor: End user
- Authentication: Optional, depending on product ownership model.
- Authorization: Public read access or user-context-appropriate read access.
- Path parameters: None
- Query parameters:
  - `query` (string, optional): search text for title or keyword
  - `genre` (string, optional): filter by genre
  - `release_year` (integer, optional): filter by release year
  - `page` (integer, optional): page number
  - `limit` (integer, optional): page size
  - `sort` (string, optional): `title`, `release_year`, `created_at`
  - `order` (string, optional): `asc`, `desc`
- Request body: None
- Response body:

```json
{
  "data": [
    {
      "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb",
      "title": "The Midnight Run",
      "genre": "Drama",
      "release_year": 2024,
      "is_saved": false
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total_items": 1,
    "total_pages": 1,
    "has_next": false,
    "has_previous": false
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:00:00Z"
  }
}
```

- Success status codes: `200 OK`
- Error status codes: `400 Bad Request`, `401 Unauthorized` if auth is required, `500 Internal Server Error`
- Validation rules:
  - `query` must be a non-empty string when provided.
  - `limit` must be between `1` and `100`.
  - `page` must be `>= 1`.
  - `sort` must be a supported field.
  - `order` must be `asc` or `desc`.
- Business rules:
  - No blank or broken search result states should be returned.
  - Empty results must be handled with a clear empty-state response.
  - Duplicate entries are not relevant at this endpoint; only search results are returned.
- Related requirements: FR-001, FR-002, FR-007, FR-008, UC-001, UC-002, UC-007, BR-005, BR-007

---

## API-002: Get Movie by ID

- API ID: API-002
- HTTP method: GET
- Endpoint: `/api/v1/movies/{movieId}`
- Purpose: Retrieve the details of a single movie record.
- Actor: End user
- Authentication: Optional, depending on product ownership model.
- Authorization: Public read access or user-context-appropriate read access.
- Path parameters:
  - `movieId` (string, required): unique movie identifier
- Query parameters: None
- Request body: None
- Response body:

```json
{
  "data": {
    "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb",
    "title": "The Midnight Run",
    "original_title": "The Midnight Run",
    "summary": "A weary traveler discovers a hidden trail of clues while chasing a missing midnight train.",
    "genre": "Drama",
    "release_year": 2024,
    "poster_url": "https://example.com/movies/the-midnight-run.jpg",
    "is_active": true,
    "is_saved": false
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:00:00Z"
  }
}
```

- Success status codes: `200 OK`
- Error status codes: `400 Bad Request`, `404 Not Found`, `500 Internal Server Error`
- Validation rules:
  - `movieId` must be a valid identifier format.
  - If the movie does not exist, return `404`.
- Business rules:
  - The API shall not return inactive/inaccessible catalog entries in the normal active user flow.
  - The product must be able to display a saved state for each movie record when required.
- Related requirements: FR-002, FR-009, BR-004, US-009, UC-002, UC-003

---

## API-003: Get Watchlist

- API ID: API-003
- HTTP method: GET
- Endpoint: `/api/v1/watchlists/me`
- Purpose: Retrieve the current watchlist for the authenticated user or active watchlist context.
- Actor: End user
- Authentication: Required if the product uses authenticated user ownership; otherwise session-based ownership is required.
- Authorization: User must access only their own watchlist.
- Path parameters: None
- Query parameters:
  - `page` (integer, optional)
  - `limit` (integer, optional)
  - `sort` (string, optional): `added_at`, `title`, `updated_at`
  - `order` (string, optional): `asc`, `desc`
- Request body: None
- Response body:

```json
{
  "data": {
    "watchlist_id": "bc91b1df-4110-4db0-bb03-f3d6c2d37911",
    "owner_id": "4d8d7a30-0b0f-4a4c-a0ef-1ac3d6a8d8d7",
    "items": [
      {
        "watchlist_item_id": "9d7d0e23-a8af-4e0d-a93d-4bcc89a355e9",
        "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb",
        "title": "The Midnight Run",
        "genre": "Drama",
        "added_at": "2026-09-25T08:45:00Z"
      }
    ],
    "total_items": 1
  },
  "pagination": {
    "page": 1,
    "limit": 20,
    "total_items": 1,
    "total_pages": 1,
    "has_next": false,
    "has_previous": false
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:00:00Z"
  }
}
```

- Success status codes: `200 OK`
- Error status codes: `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `500 Internal Server Error`
- Validation rules:
  - `page` and `limit` must be valid positive integers.
  - `sort` must be a supported field.
- Business rules:
  - Empty watchlist must yield a valid empty-state response rather than a blank or broken experience.
  - The current watchlist must reflect the user’s actual saved decisions.
- Related requirements: FR-005, FR-006, FR-010, US-005, US-006, US-010, UC-005, UC-006, BR-006, BR-008

---

## API-004: Add Movie to Watchlist

- API ID: API-004
- HTTP method: POST
- Endpoint: `/api/v1/watchlists/me/items`
- Purpose: Save a movie to the user’s watchlist.
- Actor: End user
- Authentication: Required if user identity is in use; session-based context otherwise.
- Authorization: Only the current owner may add to their watchlist.
- Path parameters: None
- Query parameters: None
- Request body:

```json
{
  "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb"
}
```

- Response body:

```json
{
  "data": {
    "watchlist_item_id": "9d7d0e23-a8af-4e0d-a93d-4bcc89a355e9",
    "watchlist_id": "bc91b1df-4110-4db0-bb03-f3d6c2d37911",
    "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb",
    "added_at": "2026-09-25T08:45:00Z",
    "is_saved": true
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:45:00Z"
  }
}
```

- Success status codes: `201 Created`
- Error status codes: `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `409 Conflict`, `422 Unprocessable Entity`, `500 Internal Server Error`
- Validation rules:
  - `movie_id` must be present and valid.
  - The movie must exist and be active.
  - The same active movie cannot be added twice to the same watchlist.
- Business rules:
  - Duplicate watchlist entries must be prevented.
  - The action must be treated as a meaningful user decision.
  - The API must return a clear state indicating the movie is saved.
- Related requirements: FR-003, FR-006, FR-009, US-003, US-006, US-009, UC-003, UC-006, BR-001, BR-003, BR-004, BR-008, DR-001

---

## API-005: Remove Movie from Watchlist

- API ID: API-005
- HTTP method: DELETE
- Endpoint: `/api/v1/watchlists/me/items/{movieId}`
- Purpose: Remove a movie from the current watchlist.
- Actor: End user
- Authentication: Required if user identity is in use; otherwise session-based context.
- Authorization: User must be allowed to modify only their own watchlist.
- Path parameters:
  - `movieId` (string, required): unique movie identifier to remove
- Query parameters: None
- Request body: None
- Response body:

```json
{
  "data": {
    "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb",
    "removed": true,
    "watchlist_id": "bc91b1df-4110-4db0-bb03-f3d6c2d37911"
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:47:00Z"
  }
}
```

- Success status codes: `200 OK`, `204 No Content`
- Error status codes: `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `500 Internal Server Error`
- Validation rules:
  - `movieId` must be valid.
  - If the movie is not in the watchlist, return `404` or a no-op success depending on business preference.
  - The system must not fail unexpectedly when the watchlist item is absent.
- Business rules:
  - Removal must update the saved state for the current user context.
  - The watchlist must continue to be trustworthy after a removal.
- Related requirements: FR-004, FR-006, US-004, US-006, UC-004, UC-006, BR-002, BR-008, DR-003

---

## API-006: Check Watchlist Status for Movie

- API ID: API-006
- HTTP method: GET
- Endpoint: `/api/v1/watchlists/me/items/{movieId}/status`
- Purpose: Determine whether a specific movie is already saved in the current watchlist.
- Actor: End user
- Authentication: Required if user identity is in use; otherwise session-based context.
- Authorization: User must access only their own watchlist state.
- Path parameters:
  - `movieId` (string, required): unique movie identifier
- Query parameters: None
- Request body: None
- Response body:

```json
{
  "data": {
    "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb",
    "is_saved": true,
    "watchlist_id": "bc91b1df-4110-4db0-bb03-f3d6c2d37911"
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:48:00Z"
  }
}
```

- Success status codes: `200 OK`
- Error status codes: `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `500 Internal Server Error`
- Validation rules:
  - `movieId` must be valid.
  - If the movie does not exist, return `404` or a valid `is_saved=false` depending on product preference.
- Business rules:
  - The user must be able to know whether a movie is already saved before taking an action.
  - The saved state indicator must reflect the current state consistently.
- Related requirements: FR-009, US-009, UC-003, UC-004, BR-004, DR-003

---

## API-007: Get Empty Watchlist State

- API ID: API-007
- HTTP method: GET
- Endpoint: `/api/v1/watchlists/me/empty`
- Purpose: Provide a standardized empty-state payload when the watchlist has no saved movies.
- Actor: End user
- Authentication: Required if user identity is in use; otherwise session-based context.
- Authorization: User must access only their own watchlist state.
- Path parameters: None
- Query parameters: None
- Request body: None
- Response body:

```json
{
  "data": {
    "watchlist_id": "bc91b1df-4110-4db0-bb03-f3d6c2d37911",
    "items": [],
    "total_items": 0,
    "empty_state": {
      "title": "Your watchlist is empty",
      "message": "Save movies you want to watch later."
    }
  },
  "meta": {
    "request_id": "uuid",
    "timestamp": "2026-09-25T08:49:00Z"
  }
}
```

- Success status codes: `200 OK`
- Error status codes: `401 Unauthorized`, `403 Forbidden`, `500 Internal Server Error`
- Validation rules:
  - No special validations beyond valid current watchlist context.
- Business rules:
  - Empty watchlists must be clear and useful.
  - The empty-state response must not appear blank or broken.
- Related requirements: FR-005, FR-007, US-005, US-007, UC-005, UC-007, BR-006, BR-010

---

## 8. Authentication and Session Behavior

### Required capability
The API must support a current user context for watchlist operations. The exact mechanism is not yet fixed.

### Recommended contract
- Any endpoint that reads or modifies a watchlist should require a user or session identifier in the request context.
- The server must determine the current watchlist owner from the authenticated identity or session context.
- If the implementation later uses a session-based anonymous watchlist, the same API contract remains valid with a different authentication strategy.

### Open technical decision
The business requirements do not confirm whether the product will use:
- anonymous watchlist persistence,
- authenticated accounts,
- or a future hybrid approach.

Therefore, the API contract is designed to allow either pattern without changing the endpoint structure.

---

## 9. Pagination

### Standard behavior
All list endpoints returning multiple results should support pagination.

Required pagination fields:
- `page`
- `limit`
- `total_items`
- `total_pages`
- `has_next`
- `has_previous`

### Validation
- `page` must be >= 1
- `limit` must be >= 1 and <= 100

---

## 10. Filtering and Sorting

### Filtering rules
- Search query uses string matching on title or keyword.
- Genre filtering is optional but supported if the catalog has a genre attribute.
- Release-year filtering is optional when the catalog includes that field.
- Active records only should be included in active product experiences.

### Sorting rules
- Default sort should be stable and predictable.
- If no sort is provided, include a business-defined default such as `title` or `created_at`.
- `asc` and `desc` are accepted directions only.

---

## 11. Search Semantics

The API search behavior must support:
- partial match,
- empty-result recovery,
- and a non-blocking user flow.

Search response design rules:
- If no results are found, return a `200 OK` plus an empty array for the main payload, rather than an error, unless the product explicitly chooses a different contract.
- Include a clear user-facing empty-state message from the frontend or backend contract if relevant.
- Do not trap the user in a dead end after a zero-result search.

---

## 12. API Versioning Strategy

### Recommended strategy
Use URL versioning:
- `/api/v1/...`

### Why this is suitable
- It is explicit and easy to understand.
- It supports future product evolution.
- It preserves backward compatibility during business and product changes.

### Versioning policy
- New breaking API changes require a new major version.
- Non-breaking changes may be handled within the same version if they remain backward compatible.
- If business requirements expand, a new `/v2` route may be introduced.

---

## 13. API Security Notes

The following are required capabilities, not implementation details:
- secure transport for all API calls,
- prevention of unauthorized watchlist access,
- validation of input before processing,
- rate limiting for abusive request patterns,
- protection against duplicate watchlist creation or conflicting writes,
- logging of request errors and business-rule violations.

---

## 14. Related Requirements Traceability

| API ID | Related Requirements |
|---|---|
| API-001 | FR-001, FR-002, FR-007, FR-008, BR-005, BR-007 |
| API-002 | FR-002, FR-009, BR-004 |
| API-003 | FR-005, FR-006, FR-010, BR-006, BR-008 |
| API-004 | FR-003, FR-006, FR-009, BR-001, BR-003, BR-004, BR-008 |
| API-005 | FR-004, FR-006, BR-002, BR-008 |
| API-006 | FR-009, BR-004 |
| API-007 | FR-005, FR-007, BR-006, BR-010 |

---

## 15. Open Questions

The following product-level decisions are still unresolved and must be clarified before finalizing an implementation-level API contract:

1. Is the watchlist tied to a logged-in user or a session-scoped anonymous user?
2. Will removed watchlist items be physically deleted or soft-deleted?
3. Should search endpoints return structured empty results or rely on client-side empty states?
4. Are there any product-specific filters beyond `genre` and `release_year`?
5. What is the exact retention and privacy policy for watchlist data?

These are product decisions, not technical defaults, and they directly affect the final API design but do not change the approved business logic.

---

## 16. Summary

The API design for MovieDux is driven by a small but important domain: search and browse a movie catalog, save/remove movies in a watchlist, and maintain clear watchlist state across repeated visits. The required API surface is intentionally small, predictable, and consistent with the approved business, functional, and data-model requirements. It supports user trust, state consistency, and meaningful empty-state handling without overengineering the product.
