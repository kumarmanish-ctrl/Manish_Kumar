# NovaArcade: Project Instruction Document

**Stack:** HTML, CSS, JavaScript (vanilla)
**Target:** Responsive web browser experience (desktop and mobile)

---

## Purpose of This Document

This document defines the complete requirements, behaviour, and expected deliverables for NovaArcade, a browser based online gaming portal. It is written for the development team and serves as the single source of truth for everything that must be built. Every page, component, interaction, and output described here must be implemented exactly as specified. Nothing in this document is optional unless explicitly stated.

---

## Table of Contents

1. [Project Summary](#1-project-summary)
2. [Technology Requirements](#2-technology-requirements)
3. [Site Structure](#3-site-structure)
4. [Home Page Specification](#4-home-page-specification)
5. [Game Library Page Specification](#5-game-library-page-specification)
6. [Game Detail Page Specification](#6-game-detail-page-specification)
7. [Embedded Game Player](#7-embedded-game-player)
8. [Search and Filter System](#8-search-and-filter-system)
9. [User Account Panel](#9-user-account-panel)
10. [Leaderboard System](#10-leaderboard-system)
11. [Theme and Visual Style](#11-theme-and-visual-style)
12. [Responsive Behaviour](#12-responsive-behaviour)
13. [Performance and Code Quality](#13-code-quality-and-performance)
14. [Scope Boundaries](#14-scope-boundaries)
15. [Deliverables Checklist](#15-deliverables-checklist)

---

## 1. Project Summary

NovaArcade is a multi page website that lets visitors browse a library of casual browser games, view details about each game, play the game directly inside the page through an embedded iframe, and track scores on a local leaderboard. The site does not require a backend server. All data must be stored using static JSON files and the browser's local storage. The site must work fully when opened from a local folder or hosted as static files.

---

## 2. Technology Requirements

### Core Technologies

The site must be built using plain HTML5, CSS3, and vanilla JavaScript (ES6 or later). No frontend frameworks such as React, Vue, or Angular may be used. No build tools, bundlers, or transpilers are required. The site must run by opening index.html directly in a browser, or through a simple static file server.

### Allowed Libraries

The following libraries may be loaded through a CDN link tag. No other external libraries are permitted.

| Library | Purpose |
|---------|---------|
| Google Fonts | Loading the Orbitron and Inter font families |
| Font Awesome | Icon set for navigation and buttons |

### Data Storage

All game metadata must be stored in a single JSON file named games.json located in the data folder. All user related data, including the local leaderboard and favourites list, must be stored using the browser's localStorage API under the key prefix novaarcade_.

---

## 3. Site Structure

The site uses four HTML pages. Each page has a specific role and must link to the others through the shared navigation bar.

### index.html

This is the home page. It displays the hero banner, a row of featured games, and a row of newly added games. It is the first page a visitor sees.

### library.html

This page displays the full game library as a grid of game cards. It includes the search bar and the category filter sidebar.

### game.html

This page displays the details of a single game, including its description, instructions, and the embedded game player. The specific game shown is determined by a query parameter in the URL, for example game.html?id=neon-runner.

### leaderboard.html

This page displays the local leaderboard table showing the top scores recorded across all games that support scoring.

### Shared Components

The following components must appear identically on every page.

| Component | Location | Contents |
|-----------|----------|----------|
| Navigation Bar | Top of every page | Site logo, links to Home, Library, Leaderboard, and a search icon |
| Footer | Bottom of every page | Site name, three text links labelled About, Contact, and Terms, and a copyright line |

---

## 4. Home Page Specification

The home page must contain the following sections in order from top to bottom.

### Hero Banner

A full width banner section with a background image, a large heading reading "Play Free Games Instantly", a subheading describing the site in one sentence, and a single button labelled "Browse Games" that links to library.html.

### Featured Games Row

A horizontal row labelled "Featured Games" containing exactly 4 game cards. Each card must display the game thumbnail image, the game title, the game category tag, and a "Play Now" button that links to the corresponding game.html page.

### New Releases Row

A horizontal row labelled "New Releases" containing exactly 4 game cards using the same card layout as the Featured Games row. The games shown in this row must be the 4 entries in games.json with the most recent dateAdded field.

### Category Quick Links

A row of clickable category icons or badges representing each category present in games.json. Clicking a category badge navigates to library.html with a query parameter that pre selects that category in the filter sidebar, for example library.html?category=puzzle.

---

## 5. Game Library Page Specification

### Layout

The library page uses a two column layout. The left column is the filter sidebar and the right column is the game grid. On screens narrower than 768 pixels, the sidebar collapses into a toggleable panel as described in section 12.

### Filter Sidebar

The sidebar must contain the following filter controls.

| Control | Type | Behaviour |
|---------|------|-----------|
| Category Filter | Checkbox list | One checkbox per category found in games.json. Checking a box adds that category to the active filter set |
| Sort Order | Dropdown select | Options are "Newest First", "Most Played", and "Alphabetical" |
| Clear Filters Button | Button | Resets all checkboxes to unchecked and the sort dropdown to "Newest First" |

### Game Grid

The grid displays one card per game that matches the active filters and search term. Each card must display the thumbnail image, title, category tag, and a play count formatted as "X plays" where X is the playCount field from games.json. Clicking anywhere on the card navigates to game.html with the corresponding game id in the query string. If no games match the active filters, the grid area must display the text "No games match your filters" centered in the grid area.

---

## 6. Game Detail Page Specification

The game detail page must read the id query parameter from the URL, locate the matching entry in games.json, and populate the page with that entry's data. If no entry matches the given id, the page must display the text "Game not found" and a button labelled "Back to Library" that links to library.html.

### Page Sections

The page must contain the following sections in order from top to bottom.

**Title Bar.** Displays the game title as a heading and the category tag beside it.

**Embedded Game Player.** As described in section 7.

**Description Panel.** Displays the full description text from the description field of the matching games.json entry.

**How to Play Panel.** Displays the instructions text from the instructions field of the matching games.json entry, rendered as a numbered list where each line of the instructions field becomes one list item.

**Related Games Row.** Displays up to 4 other games from games.json that share the same category as the current game, using the same card layout as the home page. If fewer than 4 other games share the category, only the available ones are shown.

---

## 7. Embedded Game Player

Each game in games.json includes an embedUrl field pointing to a publicly playable HTML5 game page. The game detail page must embed this URL inside an iframe with a fixed aspect ratio container of 16 to 9. The iframe must include a loading placeholder, consisting of the text "Loading game..." centered over a solid background colour, which is shown until the iframe's load event fires, after which the placeholder is hidden.

### Score Capture

When the embedded game posts a message to the parent window using the standard postMessage API with a data object containing a score field, the page must capture this value and store it in localStorage under the key novaarcade_score_<gameId>, where <gameId> is the id of the current game. If the newly received score is higher than the previously stored score for that game, the stored value is updated and the leaderboard data described in section 10 is updated as well. If the new score is not higher, no update occurs.

### Fullscreen Button

Below the iframe, a button labelled "Fullscreen" must be present. Clicking this button calls the requestFullscreen method on the iframe element.

---

## 8. Search and Filter System

### Search Bar

The search icon in the navigation bar, when clicked, expands into a text input field. Typing in this field and pressing Enter navigates to library.html with the entered text placed in a query parameter named q. On the library page, if the q query parameter is present, the game grid is filtered to only show games whose title field contains the search text, ignoring letter case.

### Combined Filtering Rule

When both a search term and one or more category checkboxes are active, a game must satisfy both conditions to appear in the grid. The title must contain the search term and the category must be one of the checked categories.

---

## 9. User Account Panel

### Local Profile

The site must include a simple local profile system that does not require any server. A button labelled "Sign In" appears in the navigation bar. Clicking it opens a modal dialog containing a single text input labelled "Display Name" and a button labelled "Save".

### Saving the Profile

Clicking "Save" stores the entered name in localStorage under the key novaarcade_username and closes the modal. After a name is saved, the "Sign In" button in the navigation bar is replaced with the saved name, and clicking it reopens the same modal pre filled with the current name, allowing the name to be changed.

### Favourites

On both the library page and the game detail page, each game card and the game detail title bar must include a heart shaped icon button. Clicking this icon toggles the game's id in a JSON array stored in localStorage under the key novaarcade_favourites. When a game id is present in this array, the heart icon must be displayed in a filled state. When absent, the heart icon must be displayed in an outline state.

---

## 10. Leaderboard System

### Data Source

The leaderboard page reads all keys in localStorage that begin with novaarcade_score_. For each such key, the page looks up the corresponding game title from games.json using the id portion of the key.

### Table Format

The leaderboard is displayed as a table with three columns in this order: "Game", "Your Best Score", and "Category". Each row corresponds to one game for which a score has been recorded. Rows are sorted in descending order by the score value.

### Empty State

If no keys beginning with novaarcade_score_ exist in localStorage, the leaderboard page displays the text "No scores yet. Go play some games!" along with a button labelled "Browse Games" linking to library.html, instead of the table.

---

## 11. Theme and Visual Style

### Colour Palette

The following colours must be used consistently across all pages.

| Name | Hex Code | Usage |
|------|----------|-------|
| Background Dark | #12121A | Page background |
| Surface | #1E1E2E | Card and panel backgrounds |
| Primary Accent | #7B5CFF | Buttons, active links, highlights |
| Secondary Accent | #00E0C7 | Hover states, badges |
| Text Primary | #F5F5FA | Main text colour |
| Text Muted | #9A9AB0 | Secondary text, captions |

### Typography

Headings must use the Orbitron font family. Body text, including paragraphs, list items, and form labels, must use the Inter font family. Both fonts must be loaded from Google Fonts.

### Buttons

All primary action buttons (such as "Play Now", "Browse Games", and "Save") must use the Primary Accent colour as the background with Text Primary as the font colour, with rounded corners of 8 pixels. On hover, the background colour transitions to the Secondary Accent colour over 0.2 seconds.

---

## 12. Responsive Behaviour

### Breakpoints

The site must use the following breakpoints for layout changes.

| Breakpoint | Width Range | Behaviour |
|------------|-------------|-----------|
| Desktop | 1024px and above | Full multi column layouts as described in each section |
| Tablet | 768px to 1023px | Game grids reduce to 2 columns, featured and new release rows scroll horizontally |
| Mobile | Below 768px | Game grids reduce to 1 column, the filter sidebar on the library page collapses behind a "Filters" toggle button, and the navigation bar links collapse into a hamburger menu |

### Hamburger Menu

On mobile widths, the navigation bar displays a hamburger icon on the right side. Clicking it slides a vertical menu panel in from the right edge of the screen, containing the same links as the desktop navigation bar stacked vertically. Clicking the hamburger icon again, or clicking outside the panel, closes it.

---

## 13. Code Quality and Performance

### File Organisation

The project must use the following file structure at minimum.

```
index.html
library.html
game.html
leaderboard.html
css/
  styles.css
js/
  main.js
  library.js
  game.js
  leaderboard.js
data/
  games.json
assets/
  images/
```

Shared logic, such as rendering a game card, reading from localStorage, and rendering the navigation bar, must be placed in main.js and reused across the page specific scripts through script tags. Logic must not be duplicated across the page specific JavaScript files.

### Code Style

All JavaScript must avoid global variable pollution by wrapping page logic inside functions or modules. All CSS must use custom properties (CSS variables) for the colour palette defined in section 11, declared once in the :root selector of styles.css.

### Performance

All images placed in the assets/images folder must be in either jpg, png, or webp format and must not individually exceed 500 kilobytes. The lazy loading attribute must be applied to all game thumbnail images that appear below the first visible section of any page.

---

## 14. Scope Boundaries

The following items are explicitly out of scope and must not be built.

- No user authentication beyond the local display name described in section 9. No passwords, accounts, or server side sessions are required.
- No payment, purchase, or in app currency systems of any kind.
- No backend server, database, or API beyond reading the static games.json file.
- No multiplayer or real time features.
- No games need to be built from scratch. All games are embedded through the iframe mechanism described in section 7 using existing publicly playable HTML5 games.
- No analytics, tracking scripts, or third party advertising integrations.

---

## 15. Deliverables Checklist

The following table lists every item that must be delivered. All 12 items are required. No deliverable may be omitted or partially completed.

| ID | Deliverable | Type | Notes |
|----|-------------|------|-------|
| D-01 | index.html home page with hero banner, featured row, new releases row, and category quick links | Page | Must match section 4 exactly |
| D-02 | library.html with filter sidebar, sort dropdown, and game grid | Page | Must match section 5 exactly |
| D-03 | game.html with title bar, embedded player, description, instructions, and related games | Page | Must match section 6 exactly |
| D-04 | leaderboard.html with sorted score table and empty state | Page | Must match section 10 exactly |
| D-05 | Shared navigation bar and footer present on all four pages | Component | Identical markup and styling across pages |
| D-06 | games.json containing at least 12 game entries across at least 3 categories | Data | Each entry includes id, title, category, thumbnail, embedUrl, description, instructions, dateAdded, and playCount fields |
| D-07 | Working search bar with query parameter based filtering | Feature | As described in section 8 |
| D-08 | Working category and sort filters on the library page | Feature | As described in section 5 |
| D-09 | Local sign in modal with persistent display name | Feature | As described in section 9 |
| D-10 | Favourites toggle with persistent heart icon state | Feature | As described in section 9 |
| D-11 | Score capture from embedded games via postMessage and leaderboard update | Feature | As described in sections 7 and 10 |
| D-12 | Fully responsive layout across desktop, tablet, and mobile breakpoints | Styling | As described in section 12, including the hamburger menu |

---

## Notes for the Development Team

All colour values, breakpoint widths, and category lists must be driven by the values defined in this document and in games.json. No hard coded sample text such as "Lorem ipsum" may remain in the final delivery. Every interactive element described in this document, including buttons, toggles, and modals, must be functional in the final build, not only styled.
