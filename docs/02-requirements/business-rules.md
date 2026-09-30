# Business Rules

This document identifies the business rules that govern the MovieDux system based on the confirmed requirements and derived analysis from the approved requirement documents 01 through 08.

The rules are separated into:
1. Confirmed business rules
2. Derived rules
3. Open questions

---

## 1. Confirmed Business Rules

### BR-001: A movie can be saved to the watchlist
- Rule ID: BR-001
- Rule Name: Save Movie to Watchlist
- Description: A user shall be able to save a movie to a personal watchlist so that the movie can be found later.
- Applies To: Movie, Watchlist, User interaction
- Trigger / Condition: A user chooses to save a movie from the catalog or results list.
- Expected Behavior: The selected movie is added to the user’s watchlist and remains available for future review.
- Related Requirements: BR-001 is aligned with BR-003, FR-003, FR-005, FR-006, US-003, US-005, US-006
- Priority: Critical

### BR-002: A movie can be removed from the watchlist
- Rule ID: BR-002
- Rule Name: Remove Movie from Watchlist
- Description: A user shall be able to remove a movie from the watchlist when the movie is no longer relevant or desired.
- Applies To: Watchlist, User interaction
- Trigger / Condition: A user selects a saved movie and chooses to remove it.
- Expected Behavior: The movie is removed from the watchlist and no longer appears as saved.
- Related Requirements: FR-004, FR-006, US-004, US-006
- Priority: High

### BR-003: A movie cannot appear more than once in the same watchlist
- Rule ID: BR-003
- Rule Name: Prevent Duplicate Watchlist Entries
- Description: The same movie shall not appear more than once in the same watchlist.
- Applies To: Watchlist
- Trigger / Condition: A user attempts to save a movie that already exists in the current watchlist.
- Expected Behavior: The system shall prevent duplicate entries and represent the movie as already saved.
- Related Requirements: FR-003, FR-009, US-003, US-009
- Priority: Critical

### BR-004: The system shall indicate whether a movie is saved
- Rule ID: BR-004
- Rule Name: Show Watchlist Status
- Description: The user shall be able to understand whether a movie is already in the watchlist before taking an action.
- Applies To: Movie item, Watchlist status
- Trigger / Condition: A user views a movie in the catalog or watchlist.
- Expected Behavior: The product clearly shows whether the movie is saved or unsaved.
- Related Requirements: FR-009, US-009, UC-009
- Priority: High

### BR-005: Empty search results must be clear and useful
- Rule ID: BR-005
- Rule Name: Clear No-Results State
- Description: When no movies match the user’s search or filters, the system shall present a clear empty state rather than a blank or confusing display.
- Applies To: Search results, empty-state behavior
- Trigger / Condition: A search returns zero matching results.
- Expected Behavior: The user is informed that no results were found and is given a path to continue.
- Related Requirements: FR-001, FR-007, FR-008, US-007, US-008, UC-001, UC-007
- Priority: High

### BR-006: Empty watchlists must be clear and useful
- Rule ID: BR-006
- Rule Name: Clear Empty Watchlist State
- Description: If the watchlist has no saved movies, the system shall explain the current state clearly and allow the user to continue browsing.
- Applies To: Watchlist empty state
- Trigger / Condition: The user opens the watchlist and there are no saved items.
- Expected Behavior: The system displays a meaningful empty watchlist state and offers the user a continuation path.
- Related Requirements: FR-005, FR-007, US-005, US-007, UC-005, UC-010
- Priority: High

### BR-007: The user must be able to continue after a failed search
- Rule ID: BR-007
- Rule Name: Recovery from Search Failure
- Description: A failed or empty search shall not trap the user in a dead end; the user must be able to refine or retry the search, or return to broader browsing.
- Applies To: Search flow
- Trigger / Condition: Search results are empty or unsatisfactory.
- Expected Behavior: The system offers recovery actions so the user can continue using the product.
- Related Requirements: FR-008, US-008, UC-007
- Priority: Medium

### BR-008: Watchlist state must reflect the user’s actual choices
- Rule ID: BR-008
- Rule Name: State Accuracy
- Description: The watchlist must reflect the user’s true current decisions, including saved and removed movies.
- Applies To: Watchlist state, user decisions
- Trigger / Condition: A user adds or removes a movie from the watchlist.
- Expected Behavior: The system shall update the watchlist to match the user’s latest valid action.
- Related Requirements: FR-006, FR-010, US-006, US-010, UC-006
- Priority: Critical

### BR-009: The product must support repeated user engagement
- Rule ID: BR-009
- Rule Name: Repeat Usage
- Description: The system shall support a user returning to the product and continuing their discovery and watchlist activity.
- Applies To: Returning user experience
- Trigger / Condition: A user revisits the system after an earlier interaction.
- Expected Behavior: The user can continue browsing and manage saved content without starting from scratch.
- Related Requirements: FR-011, US-011, UC-008
- Priority: Medium

### BR-010: The system must provide clear guidance in important states
- Rule ID: BR-010
- Rule Name: Clear Guidance
- Description: The product shall provide understandable guidance for meaningful states such as no results, empty watchlists, and saved states.
- Applies To: State transitions and feedback
- Trigger / Condition: The system moves into a meaningful state requiring user understanding.
- Expected Behavior: The user receives clear, relevant guidance and can proceed without confusion.
- Related Requirements: FR-012, US-012, UC-010
- Priority: High

---

## 2. Derived Rules

These rules are derived from the approved functional requirements and the business context, but were not explicitly stated as a single sentence in the client conversation.

### DR-001: A saved movie should be treated as a meaningful user decision
- Rule ID: DR-001
- Rule Name: Meaningful Watchlist Decision
- Description: Saving a movie to the watchlist represents a deliberate user choice and should be treated as a meaningful action rather than a transient display toggle.
- Applies To: Watchlist activity
- Trigger / Condition: The user saves a movie.
- Expected Behavior: The system preserves the saved decision and reflects it consistently across the product.
- Related Requirements: FR-003, FR-006, FR-010, US-003, US-006, US-010
- Priority: High

### DR-002: A user should not be forced into a dead end during search flow
- Rule ID: DR-002
- Rule Name: Search Continuity
- Description: Search failure shall not prematurely end the user journey; the user must be able to continue exploring the product.
- Applies To: Search and recovery flow
- Trigger / Condition: Search results are empty or unsatisfactory.
- Expected Behavior: The user sees a recovery path and can continue browsing or refining the search.
- Related Requirements: FR-008, US-008, UC-007
- Priority: Medium

### DR-003: Watchlist decisions should remain visible to the user after a state change
- Rule ID: DR-003
- Rule Name: Visible Saved State
- Description: Once a movie is saved or removed, the product should reflect the current state clearly to avoid confusion or false assumptions.
- Applies To: Movie records and watchlist view
- Trigger / Condition: The user changes the saved state of a movie.
- Expected Behavior: The current saved status is immediately reflected in the relevant views.
- Related Requirements: FR-003, FR-004, FR-009, US-003, US-004, US-009
- Priority: High

### DR-004: Product trust depends on predictable behavior
- Rule ID: DR-004
- Rule Name: Consistent User Experience
- Description: User interactions related to search, save, remove, and empty states must behave predictably so that the product feels trustworthy and reliable.
- Applies To: General user experience
- Trigger / Condition: The user interacts with core product flows.
- Expected Behavior: Actions follow the same user rules and produce understandable, consistent outcomes.
- Related Requirements: FR-012, NFR-011, US-012
- Priority: High

### DR-005: Saved content should remain available in the relevant user context
- Rule ID: DR-005
- Rule Name: Persistence in User Context
- Description: Watchlist content should remain associated with the correct user context so that the user can trust the product over time.
- Applies To: Watchlist persistence, returning users
- Trigger / Condition: A user returns to the product after previously saving items.
- Expected Behavior: The saved content remains available in the correct context until the user removes it.
- Related Requirements: FR-010, FR-011, US-010, US-011
- Priority: High

---

## 3. Open Questions

These are not confirmed business rules because the product decision is still open.

### OQ-001: What is the ownership model for the watchlist?
- Rule ID: OQ-001
- Rule Name: Watchlist Ownership Model
- Description: The business has not yet confirmed whether watchlists are anonymous, tied to a user account, or another model.
- Applies To: Watchlist ownership and persistence
- Trigger / Condition: Product definition for user identity and persistence is decided.
- Expected Behavior: The selected ownership model determines how watchlist data is stored and retrieved.
- Related Requirements: FR-010, NFR-003, NFR-021, BR-008, BR-009
- Priority: Open Question

### OQ-002: What persistence duration is required for a watchlist?
- Rule ID: OQ-002
- Rule Name: Watchlist Retention Duration
- Description: The business has not yet defined how long watchlist records should be retained and whether they persist beyond a session.
- Applies To: Watchlist retention policy
- Trigger / Condition: Product decisions around persistence and retention are finalized.
- Expected Behavior: The product aligns with the retention model established by business policy.
- Related Requirements: FR-010, NFR-022, NFR-023
- Priority: Open Question

### OQ-003: Should users be able to organize watchlist items by category or priority?
- Rule ID: OQ-003
- Rule Name: Watchlist Organization
- Description: The business has not yet confirmed whether users need to organize or classify saved movies beyond simple retention.
- Applies To: Watchlist management capability
- Trigger / Condition: Product scope expands beyond basic save/remove behavior.
- Expected Behavior: The product either supports organization features or remains limited to simple storage and retrieval.
- Related Requirements: BR-002, BR-008, FR-005, US-005
- Priority: Open Question

### OQ-004: Should a removed movie remain visible in any history or recent activity view?
- Rule ID: OQ-004
- Rule Name: History of Removed Items
- Description: The business has not yet confirmed whether removed movies should remain visible in a history or recent activity feature.
- Applies To: User history or activity tracking
- Trigger / Condition: A movie is removed from the watchlist.
- Expected Behavior: The product follows the defined history policy for removed items.
- Related Requirements: FR-004, BR-002, US-004
- Priority: Open Question

### OQ-005: What standard of user accessibility and usability is required?
- Rule ID: OQ-005
- Rule Name: Accessibility Standard
- Description: The business requirements reference usability and accessibility, but specific standards or testing expectations have not yet been set.
- Applies To: Product accessibility and usability validation
- Trigger / Condition: Product readiness and compliance standards are defined.
- Expected Behavior: The product conforms to the selected accessibility and usability standard.
- Related Requirements: NFR-013, NFR-014, NFR-011
- Priority: Open Question

---

## Business Rule Summary

Confirmed rules center on the product’s core value proposition:
- users can search for movies,
- save titles to a watchlist,
- keep the list accurate,
- prevent duplicates,
- and continue browsing even when nothing matches.

Derived rules reinforce trust, continuity, and user confidence.

Open questions remain primarily around watchlist ownership, persistence, and advanced management or history behavior.
