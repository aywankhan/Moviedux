# Existing System Analysis

## Scope
This repository is a small React application built as a movie browsing and watchlist demo. It is not a production-grade product and appears to be a learning project created to practice React fundamentals, data loading, route handling, and local component state.

---

## 1. Project purpose

- Observed from code:
  - The app is named "MovieDux" and the header copy says: "It's time for popcorn! Find your next movie here." ([src/components/Header.js](../src/components/Header.js))
  - The app loads a movie catalog from a static JSON file: `public/movies.json` ([public/movies.json](../public/movies.json))
  - There are only two primary views: a movie catalog and a watchlist ([src/App.js](../src/App.js))
- Inferred behavior:
  - The application is intended to help users browse a list of movies, filter them, and save favorites to a personal watchlist.
  - It behaves like a lightweight movie discovery UI rather than a full content platform.
- Assumption:
  - This was built as a course exercise to teach React state, props, filtering, and routing.

## 2. Current functionality

- Observed from code:
  - The app fetches movie data on mount with `fetch("movies.json")` and stores it in local state ([src/App.js](../src/App.js))
  - Users can search by title, filter by genre, and filter by rated quality category ([src/components/MovieGrid.js](../src/components/MovieGrid.js))
  - Users can toggle a movie as watchlisted; the movie ID is stored in an array ([src/App.js](../src/App.js))
  - The watchlist page displays only the selected movie cards ([src/components/Watchlist.js](../src/components/Watchlist.js))
  - The UI uses custom styling and a toggle switch to represent adding/removing from a watchlist ([src/styles.css](../src/styles.css))
- Inferred behavior:
  - Users can browse a small catalog of titles, narrow results through filters, and build a short-term personal list of movies they want to remember.
- Assumption:
  - The product lifecycle originally intended to be a front-end-only demo, not a connected movie database or authenticated user system.

## 3. All existing pages/routes

- Observed from code:
  - `BrowserRouter` is configured in [src/App.js](../src/App.js)
  - Route definitions:
    - `/` -> `MovieGrid`
    - `/watchlist` -> `Watchlist`
  - Navigation uses `<Link to="/">Home</Link>` and `<Link to="/watchlist">Watchlist</Link>` ([src/App.js](../src/App.js))
  - There is no 404 route or catch-all route defined.
- Inferred behavior:
  - The app behaves like a two-page front-end experience with navigation between the movie catalog and the watchlist.
- Assumption:
  - Because the project is a learning app, route handling is intentionally minimal and does not include protected routes, deep linking, or route guards.

## 4. All components

- Observed from code:
  - [src/App.js](../src/App.js): root application shell, data loading, state holder, router, and route composition
  - [src/components/Header.js](../src/components/Header.js): static header logo and tagline
  - [src/components/Footer.js](../src/components/Footer.js): footer with current year text
  - [src/components/MovieGrid.js](../src/components/MovieGrid.js): filtering UI and movie grid rendering
  - [src/components/MovieCard.js](../src/components/MovieCard.js): individual movie tile with title, genre, rating, and watchlist toggle control
  - [src/components/Watchlist.js](../src/components/Watchlist.js): list of selected movies based on IDs in state
  - [src/index.js](../src/index.js): React bootstrap and app mount point
  - [public/index.html](../public/index.html): HTML shell for CRA app
- Inferred behavior:
  - This is a component-driven architecture with prop drilling instead of centralized state management.
- Assumption:
  - Components were intentionally small to support teaching component composition, props, and lifecycle patterns.

## 5. User interactions

- Observed from code:
  - Search input updates local state on each keystroke ([src/components/MovieGrid.js](../src/components/MovieGrid.js))
  - Genre dropdown updates `genre` state on selection
  - Rating dropdown updates `rating` state on selection
  - Watchlist toggle changes the array of selected IDs via `toggleWatchlist` ([src/App.js](../src/App.js))
  - Clicking nav links changes routes without full-page reload
- Inferred behavior:
  - Users can explore and filter the catalog interactively and maintain a simple shortlist of movies.
- Assumption:
  - The intended UX is a simple, immediate, local browsing flow rather than a social or collaborative experience.

## 6. State management

- Observed from code:
  - `App` stores:
    - `movies` as `[]`
    - `watchlist` as `[]`
  - `MovieGrid` stores:
    - `searchTerm` as a string
    - `genre` as a string
    - `rating` as a string
  - There is no Redux, Context API, reducer, or external store implementation.
- Inferred behavior:
  - State is primitive and local to the relevant component, with app-level data passed via props.
- Assumption:
  - This is not a multi-user or persistent state architecture; it is local-only and session-scoped.

## 7. Data flow

- Observed from code:
  - `App.useEffect` calls `fetch("movies.json")` on first render ([src/App.js](../src/App.js))
  - The loaded data is assigned to `movies` via `setMovies(data)`
  - `movies` and `watchlist` are passed down to child components through props
  - `MovieGrid` filters `movies` locally and renders each result as a `MovieCard`
  - `MovieCard` calls `toggleWatchlist(movie.id)` when the toggle changes
  - The parent updates `watchlist` by adding or removing the ID from the array
- Inferred behavior:
  - Data moves from a static source file to the app state, then through props to the UI, then back to parent state through callbacks.
- Assumption:
  - The design was chosen to keep the app small and understandable for an introductory React course.

## 8. External dependencies

- Observed from code:
  - `react` and `react-dom` are used for the UI runtime ([package.json](../package.json))
  - `react-router-dom` is used for route navigation ([package.json](../package.json), [src/App.js](../src/App.js))
  - `react-scripts` is used as the build and dev tooling ([package.json](../package.json))
  - `@testing-library/*` is installed for tests ([package.json](../package.json))
  - `web-vitals` is included as a default CRA dependency ([package.json](../package.json))
- Inferred behavior:
  - The project is a standard CRA application, not a custom Webpack/Vite setup.
- Assumption:
  - The dependency set is intentionally minimal for learning, with no backend library or state manager.

## 9. API calls, if any

- Observed from code:
  - There is exactly one runtime data fetch: `fetch("movies.json")` in [src/App.js](../src/App.js)
  - This is a browser fetch to a local static JSON file, not a remote API
  - There are no HTTP POST/PUT/DELETE calls, no auth headers, no token handling, and no backend integration
- Inferred behavior:
  - The app is designed around local static data rather than live server-side content.
- Assumption:
  - The real intent was to demonstrate frontend state handling rather than production API integration.

## 10. Data models currently used

- Observed from code:
  - `public/movies.json` contains an array of movie objects with fields:
    - `id` (number)
    - `title` (string)
    - `image` (string, file name like `1.jpg`)
    - `genre` (string, lowercase values such as `drama`, `fantasy`, `horror`, `action`)
    - `rating` (string, e.g. `"8.3"`, `"9.8"`)
  - App state model:
    - `movies`: `Array<Movie>`
    - `watchlist`: `Array<number>` (IDs only)
  - Component state model:
    - `searchTerm`: `string`
    - `genre`: `string`
    - `rating`: `string`
- Inferred behavior:
  - The app models a simplified movie catalog with minimal metadata and no deeper entities like cast, description, release date, or trailer info.
- Assumption:
  - This is intentionally reduced for educational purposes and not a production-grade domain model.

## 11. Forms and validations

- Observed from code:
  - There are no real form submissions or submit buttons.
  - The app includes input/filter controls:
    - search text box ([src/components/MovieGrid.js](../src/components/MovieGrid.js))
    - genre select dropdown
    - rating select dropdown
  - These fields do not have labels associated with them beyond visible text wrappers.
  - There is no validation logic, error messaging, empty-state guidance, or form sanitization.
- Inferred behavior:
  - The application treats filtering as a lightweight UI interaction, not as structured data entry.
- Assumption:
  - Since the app is demo-focused, input validation was considered unnecessary.

## 12. Existing business logic

- Observed from code:
  - `matchesGenre` checks if the selected genre is `All Genres` or a case-insensitive match of `movie.genre`
  - `matchesSearchTerm` checks whether the title includes the typed string, case-insensitive
  - `matchesRatring` maps rating buckets:
    - `Good` => `movie.rating >= 8`
    - `Ok` => `5 <= rating < 8`
    - `Bad` => `rating < 5`
    - `All` => no filtering
  - `toggleWatchlist` adds/removes a movie ID from the array using `includes` and `filter`
- Inferred behavior:
  - The business logic is designed around simple movie discovery and basic “favorites” management.
- Assumption:
  - The project tries to simulate a focused product workflow without requiring persistence or product rules beyond local selection.

## 13. Current UI/UX behavior

- Observed from code:
  - Dark-themed layout with white text on black background ([src/styles.css](../src/styles.css))
  - Large logo and subtitle at the top of the page ([src/components/Header.js](../src/components/Header.js))
  - Search bar and filter controls align to the right for the filter bar
  - Movie cards are arranged in a responsive grid (`grid-template-columns: repeat(auto-fill, 250px)`) ([src/styles.css](../src/styles.css))
  - Cards have hover scaling effects and a custom slider toggle for “Add to Watchlist” / “In Watchlist” ([src/styles.css](../src/styles.css))
  - Footer shows copyright with current year
- Inferred behavior:
  - The app presents a modern movie-listing experience with a strong dark theme and compact card layout.
- Assumption:
  - The design is intentionally stylized for a course demo rather than optimized for polished product UX.

## 14. Existing limitations

- Observed from code:
  - No backend or database; data is static and local
  - No persistence for watchlist beyond the current browser session
  - No movie details page or trailer/player experience
  - No sorting beyond the existing category filters
  - No pagination, infinite scroll, or lazy-loading for large catalogs
  - No user accounts or personalized profiles
- Inferred behavior:
  - The app is intentionally scoped to a single-screen catalog experience and does not support real-world product usage at scale.
- Assumption:
  - It was meant to teach UI concepts rather than product-scale requirements.

## 15. Technical debt

- Observed from code:
  - `App.js` imports `logo` but never uses it; the build reports an unused variable warning ([src/App.js](../src/App.js))
  - `MovieCard.js` defines `handleError` but it is never used because the `onError` attribute is set as a string literal instead of a function reference ([src/components/MovieCard.js](../src/components/MovieCard.js))
  - There are duplicate styling sources: `App.css` and `styles.css` are both imported, but only `styles.css` is used for app-level styling ([src/App.js](../src/App.js))
  - Default CRA boilerplate remains (`src/App.css`, `src/logo.svg`, `src/reportWebVitals.js`, generated test files), which is not product-specific
  - No tests cover the application logic
- Inferred behavior:
  - The codebase retains learning scaffolding, not cleaned-up production structure.
- Assumption:
  - The repository reflects a course outcome rather than a client-ready implementation.

## 16. Potential bugs

- Observed from code:
  - In `MovieCard`, the `alt` attribute is written as `alt="{movie.title}"` instead of `alt={movie.title}`. This renders the literal string `{movie.title}` to the DOM instead of the actual title.
  - In `MovieCard`, `onError="{handleError}"` is also a string literal, so the fallback logic never fires. This means broken images will not be replaced with the default image.
  - `Watchlist` assumes every selected ID exists in `movies`; if a watchlist entry points to a missing movie, `movie` may be `undefined` when rendering `MovieCard`.
  - `fetch("movies.json")` has no `.catch()` block; network/file load errors are unhandled.
  - There is no error/loading state while the fetch is in progress.
  - No route fallback means unknown paths show no content.
  - `App.js` wraps the app with `Router` inside the main layout, but the `Header` and `Footer` are outside the router; this is valid, but navigation and page content are not separated in a semantically ideal structure.
- Inferred behavior:
  - The movie card image and fallback experience are currently unreliable, and some edge-case UI states are not handled gracefully.
- Assumption:
  - These issues are likely the result of an early learning implementation rather than deliberate product decisions.

## 17. Missing functionality

- Observed from code:
  - No movie details page
  - No sorting by popularity, rating, title, or year
  - No pagination or search suggestions
  - No persistent watchlist storage
  - No user login, profile, or saved lists
  - No filtering by release year, runtime, or language
  - No API or data source beyond static JSON
- Inferred behavior:
  - The current app is intentionally minimal and does not try to provide a complete digital movie catalog or commerce experience.
- Assumption:
  - A real product would likely include deeper catalog data, personal accounts, and backend storage.

## 18. Security concerns

- Observed from code:
  - No authentication or authorization layer exists.
  - No backend endpoints, secrets, or environment variables are used.
  - Search, filter, and watchlist state are all client-side only.
  - React escaping reduces the risk of direct XSS from simple string rendering.
- Inferred behavior:
  - There is very little direct security risk in the current code because the app is static and local-only.
- Assumption:
  - Risks would increase significantly if this were upgraded to remote data loading, user accounts, or untrusted content ingestion.

## 19. Performance concerns

- Observed from code:
  - All movie records are loaded at once from a static JSON file and rendered in one pass.
  - The watchlist page does a nested array scan (`movies.find(...)`) for each watchlist item, which is okay at small scale but inefficient in larger data sets.
  - There is no lazy-loading of images.
  - No memoization is used for derived filtered data.
- Inferred behavior:
  - Performance is acceptable for a 20-item dataset but would degrade with a much larger catalog.
- Assumption:
  - Scalability was not a design requirement for the learning phase.

## 20. Accessibility concerns

- Observed from code:
  - The search input has a placeholder but no associated `<label>` element.
  - The custom watchlist switch is a `<label>` with an `<input type="checkbox">` and a styled `span`, but the label text is visually embedded in CSS rather than a true accessible name/relationship pattern.
  - `img` alt text is broken due to string interpolation error.
  - Some elements rely on color alone for interpretation, especially rating badges (`rating-good`, `rating-ok`, `rating-bad`) and the toggled slider state.
  - There are no `aria-*` attributes or focus-visible states defined.
- Inferred behavior:
  - The app is not fully accessible to screen reader or keyboard-only users.
- Assumption:
  - Accessibility was likely not a priority in the initial learning implementation.

---

## Additional observations

### Application architecture

- Observed from code:
  - This is a client-side React single-page application created with Create React App.
  - There is no backend, database, or API layer.
  - The app is stateless beyond browser session memory.

### Runtime validation

- Observed from command output:
  - `npm run build` completed successfully.
  - The build reported warnings about unused variables in [src/App.js](../src/App.js) and [src/components/MovieCard.js](../src/components/MovieCard.js), but the project still compiles.
  - No automated tests were found in the repository for application behavior.

### Overall assessment

- Observed from code:
  - The application is a functioning movie catalog demo with filtering and watchlist functionality.
  - It demonstrates core React patterns: state, props, conditional rendering, events, and browser routing.
- Inferred behavior:
  - It is best understood as a learning-project prototype, not a product with production-grade quality controls.
- Assumption:
  - If handed to a client, it would need redesign, testing, data persistence, and product/UX refinement before being accepted as a production-ready system.

---

## Concise summary of what the application currently does

This application is a small movie browsing app that loads a static catalog of film entries from a local JSON file, lets users search and filter the list by title, genre, and rating category, and enables them to add/remove titles to a local watchlist. It uses React component state and routing to move between a home/movie catalog page and a watchlist page. The app is visually styled as a dark-themed movie showcase and demonstrates foundational React concepts, but it is intentionally limited: no backend, no persistent storage, no user accounts, no rich details pages, and limited validation/accessibility considerations.
