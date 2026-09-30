# Use Cases

This document identifies the major user and system use cases derived from the confirmed functional requirements and user personas. It focuses on what the user and system do, without prescribing implementation details.

---

## UC-001: Search for a Movie
- Use Case ID: UC-001
- Name: Search for a Movie
- Primary Actor: Casual Movie Viewer
- Supporting Actors: Genre-Focused Movie Explorer
- Goal: Find relevant movie titles quickly and clearly.
- Preconditions:
  - A movie catalog is available.
  - The user is able to access the product.
- Trigger: The user enters a title or keyword in a search field.
- Main Success Flow:
  1. The user enters a title or keyword.
  2. The system evaluates the movie catalog against the entered value.
  3. The system displays matching movie results.
  4. The user reviews the result list.
- Alternative Flows:
  1. The user enters a partial title or partial keyword.
  2. The system displays any matching partial results.
  3. The user continues reviewing the results.
- Exception Flows:
  1. No matches are found.
  2. The system displays a clear no-results message.
  3. The user chooses to refine the search or clear filters.
- Postconditions:
  - The user has a result set to review or a clear explanation of why no results were found.
- Related Functional Requirements:
  - FR-001
  - FR-002
  - FR-007
  - FR-008
- Related User Stories:
  - US-001
  - US-002
  - US-007
  - US-008

---

## UC-002: Browse Movie Results
- Use Case ID: UC-002
- Name: Browse Movie Results
- Primary Actor: Casual Movie Viewer
- Supporting Actors: Genre-Focused Movie Explorer
- Goal: Review available movie results in a readable and understandable format.
- Preconditions:
  - Matching results are available or the catalog is visible.
- Trigger: The user opens the catalog or after a search is executed.
- Main Success Flow:
  1. The system displays the movie results.
  2. The user reviews the available titles.
  3. The user decides whether to save a title or continue browsing.
- Alternative Flows:
  1. The user narrows the results set.
  2. The system refreshes the visible items.
  3. The user continues reviewing the narrowed results.
- Exception Flows:
  1. The result list becomes empty.
  2. The system displays a clear empty-state message.
  3. The user can recover by changing the criteria.
- Postconditions:
  - The user has reviewed the movie list or can continue with a recovery path.
- Related Functional Requirements:
  - FR-002
  - FR-007
  - FR-008
- Related User Stories:
  - US-002
  - US-007
  - US-008

---

## UC-003: Add Movie to Watchlist
- Use Case ID: UC-003
- Name: Add Movie to Watchlist
- Primary Actor: Casual Movie Viewer
- Supporting Actors: Frequent Movie Watcher, Genre-Focused Movie Explorer
- Goal: Save a movie so it can be reviewed later.
- Preconditions:
  - The movie is visible and available for selection.
  - The movie is not already in the user’s watchlist.
- Trigger: The user chooses to save a movie.
- Main Success Flow:
  1. The user selects the save action for a movie.
  2. The system checks whether the movie is already in the watchlist.
  3. The system adds the movie to the watchlist.
  4. The system updates the movie’s saved status in the interface.
- Alternative Flows:
  1. The user saves a second movie.
  2. The system adds it without altering existing saved items.
- Exception Flows:
  1. The user attempts to add a movie already in the watchlist.
  2. The system prevents the duplicate and keeps the watchlist unchanged.
  3. The system shows the movie as already saved.
- Postconditions:
  - The movie is retained in the watchlist and can be reviewed later.
- Related Functional Requirements:
  - FR-003
  - FR-006
  - FR-009
- Related User Stories:
  - US-003
  - US-006
  - US-009

---

## UC-004: Remove Movie from Watchlist
- Use Case ID: UC-004
- Name: Remove Movie from Watchlist
- Primary Actor: Frequent Movie Watcher
- Supporting Actors: Casual Movie Viewer
- Goal: Remove a movie that is no longer relevant or desired.
- Preconditions:
  - The movie exists in the user’s watchlist.
- Trigger: The user chooses to remove a saved movie.
- Main Success Flow:
  1. The user selects the remove action for a saved movie.
  2. The system removes the movie from the watchlist.
  3. The system updates the movie status or list display.
- Alternative Flows:
  1. The user removes multiple saved movies sequentially.
  2. The system updates the list after each valid removal.
- Exception Flows:
  1. The user attempts to remove a movie that is not in the watchlist.
  2. The system does not fail unexpectedly and leaves the list unchanged.
- Postconditions:
  - The movie is no longer present in the watchlist.
- Related Functional Requirements:
  - FR-004
  - FR-006
  - FR-009
- Related User Stories:
  - US-004
  - US-006
  - US-009

---

## UC-005: View Watchlist
- Use Case ID: UC-005
- Name: View Watchlist
- Primary Actor: Frequent Movie Watcher
- Supporting Actors: Casual Movie Viewer
- Goal: Review saved movies and decide on the next action.
- Preconditions:
  - The user has access to the watchlist view.
- Trigger: The user navigates to the watchlist.
- Main Success Flow:
  1. The user opens the watchlist view.
  2. The system displays saved movie entries.
  3. The user reviews the saved list.
- Alternative Flows:
  1. The user has no saved movies.
  2. The system presents an empty watchlist state.
- Exception Flows:
  1. A saved entry is no longer valid or available.
  2. The system displays it predictably without breaking the list view.
- Postconditions:
  - The user has a clear view of current saved content or a valid empty state.
- Related Functional Requirements:
  - FR-005
  - FR-006
  - FR-007
- Related User Stories:
  - US-005
  - US-006
  - US-007

---

## UC-006: Maintain Watchlist State
- Use Case ID: UC-006
- Name: Maintain Watchlist State
- Primary Actor: Frequent Movie Watcher
- Supporting Actors: Casual Movie Viewer
- Goal: Keep the watchlist accurate and trustworthy over time.
- Preconditions:
  - The user has a valid watchlist context or persistence model.
- Trigger: The user adds or removes a movie or returns to the product after a delay.
- Main Success Flow:
  1. The user saves a movie.
  2. The system records the current state.
  3. The user later revisits the watchlist and sees the saved movie still present.
- Alternative Flows:
  1. The user removes a movie.
  2. The system updates the saved state to reflect the change.
- Exception Flows:
  1. The user context changes unexpectedly.
  2. The system handles the case according to the agreed persistence model or communicates the limitation.
- Postconditions:
  - The watchlist reflects the user’s most current decisions.
- Related Functional Requirements:
  - FR-006
  - FR-010
- Related User Stories:
  - US-006
  - US-010

---

## UC-007: Recover from No-Results Search
- Use Case ID: UC-007
- Name: Recover from No-Results Search
- Primary Actor: Casual Movie Viewer
- Supporting Actors: Genre-Focused Movie Explorer
- Goal: Continue product usage when a search has no results.
- Preconditions:
  - A search has been executed and produced no results.
- Trigger: The result set is empty.
- Main Success Flow:
  1. The system shows a clear no-results message.
  2. The system gives the user a practical next step.
  3. The user refines the search or clears the criteria.
- Alternative Flows:
  1. The user chooses to browse the broader catalog instead of retrying the same search.
- Exception Flows:
  1. The user enters an unusable or invalid search value.
  2. The system still provides guidance toward a valid next step.
- Postconditions:
  - The user is able to continue with discovery rather than being blocked by the empty state.
- Related Functional Requirements:
  - FR-001
  - FR-007
  - FR-008
- Related User Stories:
  - US-001
  - US-007
  - US-008

---

## UC-008: Return to the Product
- Use Case ID: UC-008
- Name: Return to the Product
- Primary Actor: Frequent Movie Watcher
- Supporting Actors: Casual Movie Viewer
- Goal: Continue using the product after a previous session or visit.
- Preconditions:
  - The user has previously interacted with the product.
- Trigger: The user returns to the product at a later time.
- Main Success Flow:
  1. The user returns to the product.
  2. The system allows the user to continue discovery and view saved content.
  3. The user revisits or manages their watchlist.
- Alternative Flows:
  1. The user has no saved items.
  2. The system provides a usable empty-state and allows continued browsing.
- Exception Flows:
  1. The user returns with a stale or unavailable context.
  2. The system handles the state according to the current product rules and provides clear guidance.
- Postconditions:
  - The user can continue using the product without losing the ability to browse or manage saved items.
- Related Functional Requirements:
  - FR-005
  - FR-010
  - FR-011
- Related User Stories:
  - US-005
  - US-010
  - US-011

---

## UC-009: Understand Watchlist Status
- Use Case ID: UC-009
- Name: Understand Watchlist Status
- Primary Actor: Frequent Movie Watcher
- Supporting Actors: Casual Movie Viewer
- Goal: Know whether a movie is already saved before taking action.
- Preconditions:
  - The user is viewing a movie record or result item.
- Trigger: The user reviews a movie entry.
- Main Success Flow:
  1. The system displays the watchlist status of the movie.
  2. The user understands whether it is already saved.
  3. The user decides whether to add or remove it.
- Alternative Flows:
  1. The user changes the saved state.
  2. The status indicator updates immediately.
- Exception Flows:
  1. The status cannot be determined.
  2. The system displays a neutral state rather than a misleading value.
- Postconditions:
  - The user has a clear understanding of the current watchlist state.
- Related Functional Requirements:
  - FR-003
  - FR-004
  - FR-009
- Related User Stories:
  - US-003
  - US-004
  - US-009

---

## UC-010: Understand Product Guidance
- Use Case ID: UC-010
- Name: Understand Product Guidance
- Primary Actor: Casual Movie Viewer
- Supporting Actors: Frequent Movie Watcher, Genre-Focused Movie Explorer
- Goal: Understand the current application state and next actions.
- Preconditions:
  - The user encounters a search result, empty state, or saved-state condition.
- Trigger: The system displays a meaningful state or response.
- Main Success Flow:
  1. The user encounters a relevant state or message.
  2. The system communicates the current condition clearly.
  3. The user understands the next action or continues browsing.
- Alternative Flows:
  1. The user is not in an error state.
  2. The system still maintains clear explanation and guidance.
- Exception Flows:
  1. The system cannot confidently determine the next step.
  2. The system provides a neutral and non-misleading explanation.
- Postconditions:
  - The user understands the current state and is not confused by the system.
- Related Functional Requirements:
  - FR-007
  - FR-008
  - FR-012
- Related User Stories:
  - US-007
  - US-008
  - US-012
