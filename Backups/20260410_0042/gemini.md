# Cladeify — Project Context

## What is Cladeify?
Cladeify is a daily, interactive phylogenetic guessing game. Players identify a mystery animal by typing guesses and navigating an unfolding taxonomic tree. Think "Wordle meets taxonomy."

**Live URL:** Hosted via GitHub Pages at `https://voiddev18.github.io/Cladeify/`
**Repository:** `Voiddev18/Cladeify`

---

## Technology & Architecture

- **Single-File Build:** All styles, DOM, and JS live in one monolithic `index.html` (vanilla HTML/CSS/JS, no frameworks).
- **Tree Visualization:** SVG canvas overlay dynamically constructed via JS (`layoutNode()`) with vertical stagger calculations for mobile.
- **Wikipedia API:** Async fetch to Wikipedia REST API for clade/animal knowledge panels.
- **Theme:** "Scientific Journal Dark Mode" — charcoal backgrounds, cyan accents, 1px paper-graph patterns. This theme is unified across the main game, social pages, and leaderboard panels.

### File Structure
| File | Purpose |
|------|---------|
| `index.html` | Main entry point served to users (live game). Synced with `cladeify.html`. |
| `cladeify.html` | Core game file containing all HTML, CSS, and JS logic. |
| `404.html` | Custom 404 error page for GitHub Pages and Firebase. |
| `CNAME` | Custom domain configuration for GitHub Pages. |
| `firebase.json` | Firebase configuration for Hosting and Firestore settings. |
| `.firebaserc` | Firebase project alias configuration (`cladeify`). |
| `firestore.rules` | Security rules for the Firestore `leaderboard` collection. |
| `firestore.indexes.json` | Composite indexes for sorting leaderboard data. |
| `gemini.md` | Comprehensive project documentation and AI context (this file). |
| `cladeify_summary.md` | Condensed summary of technical features and status. |
| `README.md` | Basic project overview for the repository landing page. |
| `Backups/` | Directory containing timestamped historical project snapshots. |
| `.git/` | Git version control metadata. |

---

## Recent Technical Updates (April 2026)

### 1. Unified Design Language
The leaderboard and social panels were overhauled to match the "Scientific Journal" dark theme. Standardized on `var(--accent)` (cyan/light blue) for all interactive elements, removing legacy orange/yellow highlight colors.

### 2. Mobile Tree Layout Engine Fixes
To solve leaf node overlapping on narrow devices (320px-375px):
- **3-Tier Cascade:** Implemented a `[25px, 65px, 105px]` vertical stagger waterfall for leaf nodes. This ensures that adjacent animal names always land in unique vertical rows, preventing text collisions.
- **Dynamic Bounding Box:** Updated `svgH` and `levelH` calculations to automatically expand the canvas vertically based on the deepest possible drop in the 3-tier cascade.
- **Horizontal Buffering:** Increased `leafW` to 65px on mobile to provide sufficient breathing room without forcing excessive horizontal zoom.

### 3. Header Optical Alignment
The main header layout was geometrically refined:
- Stripped default browser margins from `<h1>` to anchor the bounding box.
- Switched to strict `align-items: center` for the logo container.
- Applied a `-2px` vertical transform to the title/icon for perfect optical baseline matching with the "Social" button on the right.

### 5. Share Button Link & Code Cleanup
- **Fixed Share URL:** Updated the `shareResults()` text generation to use `https://cladeify.com` instead of the legacy GitHub Pages URL.
- **Deduplication:** Removed a redundant `shareResults()` and `getShareText()` function block that was causing confusion. Standardized on the single-argument `shareResults(btnElement)` version which supports modern clipboard copying and fallback mechanisms.

---

## Firebase / Firestore Setup

### Project
- **Project ID:** `cladeify`
- **Auth Support Email:** `voiddev18@gmail.com`
- **Firestore Location:** `nam5`
- **Hosting:** Firebase Hosting + GitHub Pages

### Authentication
- **Google Sign-In** is available but **optional**.
- Authorized redirect URIs: `localhost:8765`, `cladeify.firebaseapp.com`, `cladeify.web.app`, `voiddev18.github.io`

### Firestore Collection: `leaderboard`
Each document represents one player. Document ID is either:
- The user's Firebase Auth `uid` (if signed in with Google)
- A client-generated `localId` (format: `local_<timestamp36><random>`) stored in `localStorage`

#### Document Fields
| Field | Type | Description |
|-------|------|-------------|
| `uid` | string | Firebase Auth UID (auth'd users only) |
| `localId` | string | Client-generated persistent ID (anon users only) |
| `name` | string | Display name (1-20 chars) |
| `nameLower` | string | Lowercased name for uniqueness checks |
| `photoURL` | string | Google profile photo URL |
| `plays` | number | Total games played |
| `wins` | number | Total wins |
| `winRate` | number | Win percentage (0-100) |
| `streak` | number | Current win streak |
| `maxStreak` | number | Best win streak ever |
| `todayGuesses` | number | Guesses used today |
| `todayWon` | boolean | Whether today's puzzle was won |
| `todayDay` | number | Puzzle day number |
| `updatedAt` | timestamp | Server timestamp of last update |

### Firestore Security Rules
```
read: anyone (public)
write (auth'd): request.auth.uid == docId
create/update (anon): localId == docId, name 1-20 chars, nameLower validated
delete (anon): resource.data.localId == docId
```
**Deploy command:** `npx firebase-tools deploy --only firestore:rules --project cladeify`

---

## Leaderboard System

### Dual-Identity Model
- **Authenticated users** use their Google `uid` as the document ID.
- **Anonymous users** get a persistent `localId` stored in `localStorage` (`cladeify_local_uid`).
- Both can set a custom display name stored in `localStorage` (`cladeify_lb_username`).

### Name Validation
1. **Length:** 1-20 characters.
2. **Uniqueness:** Checked via Firestore query on both `nameLower` and `name` fields. Case-insensitive.
3. **Content Filter:** A client-side `BANNED_WORDS` list blocks ethnic slurs and hateful terms. Mild profanity is intentionally allowed. The filter strips spaces, underscores, hyphens, and dots before checking substrings.

### Leaderboard Tabs
- **Today:** Sorted by today's guesses (ascending, winners first).
- **All Time:** Sorted by wins (descending).
- **Streaks:** Sorted by best streak (descending), then current streak.

### Settings Panel (collapsible ⚙️)
- Display name input + Submit/Save button.
- Google Sign-In button (optional, shown to anonymous users).
- **Remove from leaderboard:** User must type their exact username to confirm deletion. Deletes the Firestore document and clears `cladeify_lb_username` from localStorage.

### Auto-Sync
When a game ends (`saveDailyState`), stats automatically push to the cloud for:
- Authenticated users (always).
- Anonymous users who have previously submitted a name.

### "(you)" Detection
The leaderboard highlights the current user's row using a triple-check: `e.uid === myUid || e.id === myUid || e.localId === myUid`.

---

## Core Game Mechanics

1. **LCA & Color Distances:** Engine finds the Lowest Common Ancestor between guess taxonomy and answer taxonomy.
2. **Color Legend:**
   - 🟥 **Red:** Very distant (diverged early).
   - 🟨 **Yellow:** Getting closer (Class/Order level match).
   - 🟩 **Green:** Very close (Family/Genus level match).
   - 🟦 **Blue:** Direct hit (answer found).
3. **Daily PRNG:** Seeded RNG tied to current date — same animal for everyone worldwide on a given day.
4. **Stats Tracking:** `localStorage` keys: `cladeify_plays`, `cladeify_wins`, `cladeify_streak`, `cladeify_max_streak`, `cladeify_daily`.

---

## Development Rules

1. **Mobile First:** Any layout change MUST consider 320-375px widths. Tree uses vertical stagger for readability.
2. **Test Before Push:** Never push layout changes without physical mobile device testing. Developer Tools emulation is not sufficient for final validation. Wait for explicit "push it" command.
3. **Dependency Free:** Vanilla HTML/CSS/JS only. No external libraries (panzoom was removed). Use native elements like `<details>`, native SVG.
4. **test.html Workflow:** New features are developed in `test.html` first, then merged into `index.html` after verification.
5. **Local Server:** `python -m http.server 8765` from the project root for local testing.

## Git Workflow
```bash
git add .
git commit -m "Descriptive message"
git push origin main
```

## Deploying Firestore Rules
```bash
npx firebase-tools deploy --only firestore:rules --project cladeify
```
