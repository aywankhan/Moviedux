# Requirements Traceability Matrix

## 1. Purpose

This matrix maps the approved business and product requirements through the functional layer, user stories, UX design, implementation modules, and validation coverage. It provides end-to-end traceability for the MovieDux product scope and helps confirm the implementation is aligned to approved requirements.

---

## 2. Traceability Matrix

| Business Requirement ID | Business Requirement | Functional Requirement(s) | User Story ID(s) | Acceptance Criteria | Design Component | Implementation Module | Test Case |
|---|---|---|---|---|---|---|---|
| BR-001 | Save Movie to Watchlist | FR-003, FR-006, FR-010 | US-003, US-006, US-010 | Given a movie is not already saved, when the user chooses to add it, then the system saves it in the watchlist; when the user returns later, the saved content is still available. | Discover/Search Page, Movie Card, Watchlist Page | `src/components/MovieGrid.js`, `src/components/MovieCard.js`, `src/components/Watchlist.js`, `src/hooks/useWatchlist.js`, `src/store/watchlistStore/` | TST-06, TST-10, SYS-03, UI-03 |
| BR-002 | Remove Movie from Watchlist | FR-004, FR-006 | US-004, US-006 | Given a saved movie exists, when the user removes it, then the system removes it from the watchlist and no longer shows it as saved. | Watchlist Page, Movie Card | `src/components/Watchlist.js`, `src/components/MovieCard.js`, `src/services/watchlistService/` | TST-06, TST-07, SYS-04, UI-04 |
| BR-003 | Prevent Duplicate Watchlist Entries | FR-003, FR-009 | US-003, US-009 | Given the user tries to add a movie already in the watchlist, when the add action is attempted, then the system does not create a duplicate entry. | Movie Card, Status Indicator | `src/components/MovieCard.js`, `src/hooks/useWatchlist.js`, `src/services/watchlistService/` | TST-06, API-004, SYS-03 |
| BR-004 | Show Watchlist Status | FR-009 | US-009 | Given the user is viewing a movie entry, when the watchlist status is evaluated, then the system clearly indicates whether it is already saved. | Saved/Unsaved Status Indicator | `src/components/MovieCard.js`, `src/utils/state/`, `src/components/MovieGrid.js` | TST-05, SYS-02, UI-02 |
| BR-005 | Clear No-Results State | FR-001, FR-007, FR-008 | US-001, US-007, US-008 | Given there are no search results, when a search is executed, then the system displays a clear no-results message and offers a next step. | Search Empty State, Recovery Prompt | `src/components/EmptyState.js`, `src/components/SearchRecovery.js`, `src/components/MovieGrid.js` | TST-04, TST-08, TST-09, SYS-05 |
| BR-006 | Clear Empty Watchlist State | FR-005, FR-007 | US-005, US-007 | Given the watchlist is empty, when the watchlist page is opened, then the system displays a clear empty-watchlist message with a path forward. | Watchlist Empty State, Watchlist Page | `src/components/Watchlist.js`, `src/components/WatchlistEmptyState.js`, `src/styles.css` | TST-07, TST-08, SYS-06 |
| BR-007 | Recovery from Search Failure | FR-008 | US-008 | Given a search returns no results, when the user sees the empty state, then the system offers a practical next step to refine the search or browse broader results. | Search Recovery Control, Discover Page | `src/components/SearchRecovery.js`, `src/components/FilterResetControl.js`, `src/hooks/useSearchRecovery.js` | TST-09, SYS-05, UI-05 |
| BR-008 | State Accuracy | FR-006, FR-010 | US-006, US-010 | Given state changes occur, when the user revisits the watchlist, then the system reflects the user’s current decisions consistently. | Watchlist Page, Saved-State Model | `src/store/watchlistStore/`, `src/hooks/usePersistentWatchlist.js`, `src/components/Watchlist.js` | TST-10, SYS-06, API-003 |
| BR-009 | Repeat Usage | FR-011 | US-011 | Given the user has previously interacted with the product, when the user returns later, then the user can continue discovery and watchlist activity. | App Shell, Return-Visit State, Home Page | `src/hooks/useReturnVisitorState.js`, `src/components/AppShell.js`, `src/pages/HomePage/` | TST-11, SYS-07, UAT-02 |
| BR-010 | Clear Guidance | FR-007, FR-008, FR-012 | US-007, US-008, US-012 | Given the user encounters a meaningful state, when the system presents guidance, then it provides clear and actionable instructions without ambiguity. | Empty-State Messaging, Guidance Banner, Search Recovery | `src/components/EmptyState.js`, `src/components/SearchRecovery.js`, `src/styles.css` | TST-08, TST-09, UI-06, UAT-03 |

---

## 3. Relationship Notes

### Business Requirement to Functional Requirement
The business requirements are represented as the product-level policies and outcomes the client expects. Each business requirement maps to one or more functional requirements that describe the user-visible behavior and business value.

### Functional Requirement to User Story
Each functional requirement is translated into one or more user stories that express the requirement from a user perspective and define tangible acceptance criteria.

### User Story to Design Component
The UX/UI specification translates the user story into a component or page design. This includes the page layout, status indicators, empty states, search flow, and watchlist views.

### Design Component to Implementation Module
The approved development plan maps the design into engineering modules and likely files or logical module groupings. This provides traceability from design intent to source structure.

### Implementation Module to Test Case
The test plan defines the validation case coverage for the resulting modules and user flows. Each module is covered by unit, integration, API, UI, or system tests depending on risk and scope.

---

## 4. High-Priority Traceability Summary

| Priority Area | Business Requirement(s) | Key User Story IDs | Primary Validation |
|---|---|---|---|
| Watchlist save/remove lifecycle | BR-001, BR-002, BR-003, BR-008 | US-003, US-004, US-006, US-010 | TST-06, TST-10, SYS-03, SYS-04 |
| Search and no-result handling | BR-005, BR-007 | US-001, US-007, US-008 | TST-04, TST-08, TST-09 |
| Empty-state guidance | BR-005, BR-006, BR-010 | US-005, US-007, US-008 | TST-08, UI-05, UAT-03 |
| Returning user behavior | BR-009 | US-011 | TST-11, UAT-02 |

---

## 5. Traceability Coverage Status

The matrix confirms that the approved business requirements are traceable to:

- functional requirements,
- user stories,
- UX/UI design components,
- implementation module groups,
- and validation cases.

This gives the product a complete read-through from business intent to implementation and test execution, while maintaining alignment to the approved documentation set.
