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

---

## Recent Technical Updates (April 2026)

### 1. Weekend 1.15x Coin Multiplier
- **Multiplier Logic:** Implemented a `1.15x` bonus multiplier active every Saturday and Sunday.
- **Rounding Rule:** Uses standard mathematical rounding (`Math.round`). Values with tenths place < 5 round down, while ≥ 5 round up.
- **UI Integration:** A gold `1.15x` badge appears in the header on weekends. Win notifications include a "Weekend Bonus" toast message.

### 2. Shop UI Overhaul
- **Centered Layout:** Transitioned from a split/inline header to a clean, vertically-stacked centered layout as per mockup.
- **Earning Banner:** Added an integrated informational banner explaining the coin reward system (1 coin/win + 1 coin/extra guess).
- **Concentrated Points Pocket:** The coin balance is now displayed in a prominent gold pill centered below the instructions.

### 3. Navigation & Onboarding
- **Quick Rules Sidebar:** Added a "Quick Rules" banner at the top of the left sidebar for instant onboarding, including a shortcut button to the full instructions.
- **Premium Close Buttons:** Upgraded modal close buttons from simple text characters to interactive, circular glassmorphism buttons with hover scale effects.

### 4. Technical Refinements
- **Positioning Fix:** Resolved a major layout issue where the Shop close button was floating off-screen by adding `position: relative` to the base `.modal` class.
- **Code Consolidation:** Standardized point reward calculations into a single `calculateEarnedPoints()` helper to ensure multiplier consistency across all win scenarios.

---

## Firebase / Firestore Setup
*(Refer to earlier entries for stable Firestore collection schemas and security rules)*

## Development Rules
1. **Mobile First:** Any layout change MUST consider 320-375px widths.
2. **test.html Workflow:** New features are developed in `test.html` first.
3. **Local Server:** `python -m http.server 8765` from the project root.
