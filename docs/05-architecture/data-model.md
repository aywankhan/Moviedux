# Data Model

## 1. Purpose and Scope

This document defines the core data model required by the approved MovieDux business, functional, user story, use case, and architecture requirements. It is intentionally based on confirmed product behavior and does not prescribe a specific database technology or application implementation.

The approved product scope centers on:
- a movie catalog,
- user discovery/search,
- a personal watchlist,
- state persistence for saved movies,
- duplicate prevention,
- empty-state clarity,
- and repeat usage across sessions or revisits.

This model covers the minimum persistable domain needed to support those requirements while leaving open the exact user identity mechanism (authenticated user, session-based user, or future identity layer).

---

## 2. Data Model Principles

The data model is designed to support the following principles:
- each movie can exist in a catalog independently,
- a watchlist belongs to a user or user context,
- the same movie cannot be saved twice in the same watchlist,
- watchlist state must be maintainable and trustworthy,
- removed items may be soft-deleted for history and auditability,
- the model supports future growth without forcing an immediate decision on authentication strategy.

---

## 3. Entity Relationship Overview

Textual overview:

WatchlistOwner 1 --- 1 Watchlist
Watchlist 1 --- * WatchlistItem
Movie 1 --- * WatchlistItem

In plain terms:
- one watchlist owner has one active watchlist (conceptually),
- a watchlist contains many items,
- a movie can appear in many watchlists,
- a watchlist item links a specific movie to a specific watchlist.

---

## 4. Entity: WatchlistOwner

### Purpose
Represents the person or user context that owns a watchlist. This entity is intentionally abstract because the approved requirements do not yet mandate a specific authentication model.

### Fields

| Field | Data Type | Required | Description |
|---|---|---:|---|
| watchlist_owner_id | UUID / BIGINT | Yes | Primary key for the owner record. |
| owner_type | VARCHAR(30) | Yes | Type of ownership context, such as `authenticated_user` or `anonymous_session`. |
| external_user_id | VARCHAR(128) | No | Identifier for an authenticated user if the system later supports login. |
| session_id | VARCHAR(255) | No | Session identifier for anonymous or temporary user contexts. |
| created_at | TIMESTAMP | Yes | Date and time the owner record was created. |
| updated_at | TIMESTAMP | Yes | Date and time the owner record was last updated. |
| last_seen_at | TIMESTAMP | No | Last known activity time for repeat usage tracking. |
| deleted_at | TIMESTAMP | No | Soft-delete timestamp if the owner record is removed. |

### Primary Key
- watchlist_owner_id

### Foreign Keys
- None directly required.

### Relationships
- One WatchlistOwner can have one Watchlist.

### Constraints
- `owner_type` must be in a controlled list of acceptable values.
- At least one of `external_user_id` or `session_id` must be present depending on the owner type.
- If `owner_type = 'authenticated_user'`, then `external_user_id` must be present and unique.
- If `owner_type = 'anonymous_session'`, then `session_id` must be present and unique.
- `created_at` must be populated when the record is inserted.

### Validation
- `owner_type` must not be blank.
- `external_user_id` and `session_id` must be non-empty strings when provided.
- `last_seen_at` cannot be earlier than `created_at`.

### Indexes
- `idx_watchlist_owner_type`
- `idx_watchlist_owner_external_user_id` (unique when authenticated)
- `idx_watchlist_owner_session_id` (unique when session-based)
- `idx_watchlist_owner_last_seen_at`

### Example Record
```json
{
  "watchlist_owner_id": "4d8d7a30-0b0f-4a4c-a0ef-1ac3d6a8d8d7",
  "owner_type": "anonymous_session",
  "external_user_id": null,
  "session_id": "sess_7b32d886d9",
  "created_at": "2026-09-25T08:30:00Z",
  "updated_at": "2026-09-25T08:30:00Z",
  "last_seen_at": "2026-09-25T09:05:00Z",
  "deleted_at": null
}
```

---

## 5. Entity: Watchlist

### Purpose
Represents the user’s saved collection of movies. The approved requirements indicate a single personalized watchlist with the ability to add and remove movies, and to maintain a clear empty state.

### Fields

| Field | Data Type | Required | Description |
|---|---|---:|---|
| watchlist_id | UUID / BIGINT | Yes | Primary key for the watchlist. |
| owner_id | UUID / BIGINT | Yes | Foreign key to the owning user context. |
| name | VARCHAR(100) | No | Optional display label for the watchlist; defaults to a standard name such as "My Watchlist". |
| created_at | TIMESTAMP | Yes | Date and time the watchlist was created. |
| updated_at | TIMESTAMP | Yes | Date and time the watchlist was last updated. |
| deleted_at | TIMESTAMP | No | Soft-deletion timestamp if the watchlist is later removed. |

### Primary Key
- watchlist_id

### Foreign Keys
- `owner_id` -> `WatchlistOwner.watchlist_owner_id`

### Relationships
- One WatchlistOwner has one Watchlist.
- One Watchlist contains many WatchlistItem records.

### Constraints
- Each owner has at most one active watchlist.
- `owner_id` must reference an existing owner record.
- `name` is optional but if supplied must be between 1 and 100 characters.

### Validation
- `name` may not be blank when present.
- `watchlist_id` and `owner_id` must not be null.
- `created_at` must be set at insert time.

### Indexes
- `idx_watchlist_owner_id` (unique on active row)
- `idx_watchlist_deleted_at`

### Example Record
```json
{
  "watchlist_id": "bc91b1df-4110-4db0-bb03-f3d6c2d37911",
  "owner_id": "4d8d7a30-0b0f-4a4c-a0ef-1ac3d6a8d8d7",
  "name": "My Watchlist",
  "created_at": "2026-09-25T08:40:00Z",
  "updated_at": "2026-09-25T09:10:00Z",
  "deleted_at": null
}
```

---

## 6. Entity: Movie

### Purpose
Represents a movie available in the product’s catalog for search and discovery. The approved requirements call for a searchable movie catalog and the ability to save a movie for later.

### Fields

| Field | Data Type | Required | Description |
|---|---|---:|---|
| movie_id | UUID / BIGINT | Yes | Primary key for the movie record. |
| title | VARCHAR(255) | Yes | Primary title shown to users. |
| original_title | VARCHAR(255) | No | Original title if different from the display title. |
| summary | TEXT | No | Basic description or synopsis for discovery and browsing. |
| release_year | SMALLINT | No | Release year, if known. |
| genre | VARCHAR(100) | No | Main genre or category for browsing or filtering. |
| poster_url | TEXT | No | Optional image URL for movie representation. |
| is_active | BOOLEAN | Yes | Indicates whether the movie is currently available in the catalog. |
| created_at | TIMESTAMP | Yes | Date and time the movie record was created. |
| updated_at | TIMESTAMP | Yes | Date and time the movie record was last updated. |
| deleted_at | TIMESTAMP | No | Soft-deletion timestamp when the movie is retired or hidden. |

### Primary Key
- movie_id

### Foreign Keys
- None directly required.

### Relationships
- One Movie can appear in many WatchlistItem entries across different watchlists.

### Constraints
- `title` cannot be empty or whitespace.
- `release_year` must be a valid year and cannot exceed a reasonable future range.
- `genre` must be a non-empty text value when supplied.
- `is_active` defaults to `true` for active catalog entries.

### Validation
- `movie_id` must be present.
- `title` must contain meaningful text.
- `poster_url` must be a valid URL when provided.
- `release_year` must be a four-digit year when present.

### Indexes
- `idx_movie_title`
- `idx_movie_release_year`
- `idx_movie_is_active`
- `idx_movie_genre`
- Optional full-text index on `title` and `summary` if the catalog is search-heavy.

### Example Record
```json
{
  "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb",
  "title": "The Midnight Run",
  "original_title": "The Midnight Run",
  "summary": "A weary traveler discovers a hidden trail of clues while chasing a missing midnight train.",
  "release_year": 2024,
  "genre": "Drama",
  "poster_url": "https://example.com/movies/the-midnight-run.jpg",
  "is_active": true,
  "created_at": "2026-09-25T08:00:00Z",
  "updated_at": "2026-09-25T08:00:00Z",
  "deleted_at": null
}
```

---

## 7. Entity: WatchlistItem

### Purpose
Links a movie to a watchlist and records the user’s decision to save that movie for later. This is the key transactional entity because it enforces a single movie-to-watchlist relationship and supports duplicate prevention.

### Fields

| Field | Data Type | Required | Description |
|---|---|---:|---|
| watchlist_item_id | UUID / BIGINT | Yes | Primary key for the join record. |
| watchlist_id | UUID / BIGINT | Yes | Foreign key to the watchlist. |
| movie_id | UUID / BIGINT | Yes | Foreign key to the movie. |
| added_at | TIMESTAMP | Yes | Date and time the movie was added to the watchlist. |
| removed_at | TIMESTAMP | No | Date and time the item was removed if the product uses soft deletion. |
| created_at | TIMESTAMP | Yes | Audit field for initial record creation. |
| updated_at | TIMESTAMP | Yes | Audit field for changes. |
| deleted_at | TIMESTAMP | No | Soft-delete timestamp to hide an item from active watchlists while preserving history. |

### Primary Key
- watchlist_item_id

### Foreign Keys
- `watchlist_id` -> `Watchlist.watchlist_id`
- `movie_id` -> `Movie.movie_id`

### Relationships
- Many WatchlistItem records belong to one Watchlist.
- Many WatchlistItem records can reference one Movie.

### Constraints
- A movie can appear only once in an active watchlist.
- `watchlist_id` and `movie_id` cannot be null.
- The system must prevent duplicate active entries through a unique constraint.
- If `removed_at` is populated, the item should be treated as inactive in the standard watchlist view.

### Validation
- `added_at` must be set and cannot be null.
- `removed_at` cannot be earlier than `added_at` when provided.
- `deleted_at` cannot be earlier than `created_at`.

### Indexes
- `idx_watchlist_item_watchlist_id`
- `idx_watchlist_item_movie_id`
- `idx_watchlist_item_active_unique` on `(watchlist_id, movie_id)` with active-only filtering or equivalent unique constraint
- `idx_watchlist_item_removed_at`

### Example Record
```json
{
  "watchlist_item_id": "9d7d0e23-a8af-4e0d-a93d-4bcc89a355e9",
  "watchlist_id": "bc91b1df-4110-4db0-bb03-f3d6c2d37911",
  "movie_id": "1f292bbd-3af8-4c38-8b99-c44a8cc8e9eb",
  "added_at": "2026-09-25T08:45:00Z",
  "removed_at": null,
  "created_at": "2026-09-25T08:45:00Z",
  "updated_at": "2026-09-25T08:45:00Z",
  "deleted_at": null
}
```

---

## 8. Entity Relationship Explanation

### WatchlistOwner to Watchlist
A WatchlistOwner represents the actor or context that owns a watchlist. The relationship is one-to-one because the approved business model describes a single watchlist as the user’s saved collection. This is a clear and simple model that supports future growth into authenticated identity or session-based persistence without forcing an immediate implementation decision.

### Watchlist to WatchlistItem
A Watchlist has many items. Each item records a movie saved to that watchlist. This supports the business requirement that the user can add multiple movies and revisit them later.

### Movie to WatchlistItem
A Movie may appear in multiple watchlists, but each watchlist must not include duplicate active entries for the same movie. The join table records the association and keeps the system capable of adding or removing items without losing the underlying movie catalog record.

---

## 9. Data Integrity Rules

The following integrity rules are required to match the approved business and functional requirements:

1. Duplicate watchlist prevention
   - The same movie cannot be saved twice in the same active watchlist.
   - The system must reject or ignore duplicate add actions.

2. Referential integrity
   - Every WatchlistItem must reference a valid Watchlist and a valid Movie.
   - The system must prevent orphaned records.

3. Valid user context
   - The watchlist owner must map to a valid owning context, whether authenticated or session-based.
   - A watchlist cannot exist without a valid owner.

4. Data consistency for state changes
   - When a movie is removed from a watchlist, it should no longer appear in the active watchlist view.
   - The current state must reflect the user’s last valid action.

5. Catalog integrity
   - Movie records must be valid and active before they are made available for saving.
   - The system should not allow adding non-existent or inactive movies to the watchlist.

6. Time integrity
   - `added_at` must be set for every item; `removed_at` or `deleted_at` cannot precede it.
   - `updated_at` must not be earlier than `created_at`.

7. Search and display integrity
   - Search results must not show records that have been logically removed from the active catalog.
   - Empty states must be triggered only by valid empty conditions.

---

## 10. Audit Fields

The approved system depends on user trust, predictable watchlist state, and accurate record management. Audit fields should be present in each user-modifiable or stateful entity.

### Standard audit fields

| Field | Applies To | Purpose |
|---|---|---|
| created_at | WatchlistOwner, Watchlist, Movie, WatchlistItem | Captures when the record entered the system. |
| updated_at | WatchlistOwner, Watchlist, Movie, WatchlistItem | Captures the last known modification. |
| deleted_at | WatchlistOwner, Watchlist, Movie, WatchlistItem | Supports soft deletion and preservation of historical context. |
| last_seen_at | WatchlistOwner | Tracks the last known interaction or revisit. |
| added_at | WatchlistItem | Captures when a movie entered the watchlist. |
| removed_at | WatchlistItem | Captures when the movie was removed, if soft deletion is used. |

### Notes
- `created_at` and `updated_at` are required for maintaining accurate state and operational consistency.
- `deleted_at` and `removed_at` are especially important where the business wants to preserve record history while hiding items from active users.

---

## 11. Soft Deletion Requirements

### Applicable Entities
- WatchlistItem
- WatchlistOwner
- Watchlist
- Movie (optional, if catalog retirement is required)

### Required Behavior
If soft deletion is used, the system should:
- keep the row for historical or audit purposes,
- exclude the row from active user views,
- preserve the fact that the item was once saved or once active,
- avoid deleting records in a way that breaks referential integrity or user trust.

### Recommended rules
- A soft-deleted watchlist item should not appear in the active watchlist list.
- A soft-deleted watchlist item should be preserved to support investigation and future auditing.
- A soft-deleted movie should be hidden from active search and discovery output unless the business explicitly requires historical visibility.
- A soft-deleted watchlist or owner record should not be used in normal user interactions.

### Open decision
The approved requirements do not state whether soft deletion is required for all entities or only for the watchlist item itself. Therefore, the recommended stance is:
- implement soft deletion for watchlist items by default,
- keep soft deletion optional for owner/watchlist/movie records until the product’s retention policy is confirmed.

---

## 12. Recommended Minimum Data Model

The minimum required data model for the approved product is:
- WatchlistOwner
- Watchlist
- Movie
- WatchlistItem

This is sufficient to support:
- discovery of movies,
- retention of a user’s saved interests,
- duplicate prevention,
- active/inactive watchlist state,
- return visits and repeat usage,
- clear empty-state and saved-state behavior.

---

## 13. Open Data Model Decisions

The following questions remain open and should be clarified before finalizing a production schema:

1. Is the watchlist tied to a fully authenticated user or an anonymous session-based user context?
2. Is a single watchlist per owner required, or are multiple lists contemplated in a future release?
3. Is the movie catalog fully static, externally sourced, or maintained by the product team?
4. Are removed watchlist items retained for history, or is a hard delete acceptable?
5. Are there business requirements for movie metadata beyond title, summary, year, and genre?

These are not contradictions to the approved requirements; they are simply unresolved product and data-policy decisions.

---

## 14. Summary

The approved product’s data model is centered on the movie catalog and the user’s personal watchlist. The essential entities are movie, watchlist owner, watchlist, and watchlist item. This model supports the confirmed business behavior: discover movies, save them to a watchlist, avoid duplicates, manage saved state, and revisit the product later without losing the user’s current decisions.
