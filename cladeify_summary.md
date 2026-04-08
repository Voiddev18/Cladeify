# Cladeify Project Summary

## What is Cladeify?
Cladeify is a daily, interactive phylogenetic guessing game. The objective is to identify a mystery animal by typing guesses and navigating down an unfolding taxonomic tree to pinpoint the answer. 

## Technology & Architecture
- **Single File Build:** All styles, structural DOM, and JavaScript algorithms are maintained purely in one monolithic `index.html` file using Vanilla HTML/CSS/JS without any heavy build system or external frameworks.
- **Tree Visualization:** We use an SVG canvas overlay manipulated dynamically in JavaScript to construct the node grid, connecting paths and spacing. 
- **Wikipedia API:** Whenever a clade or animal is guessed, the game performs an asynchronous fetch to Wikipedia’s REST API to bring back text and thumbnail knowledge panels seamlessly inside the UI without breaking state.
- **Theme:** Current theme is "Scientific Journal Dark Mode", adopting sleek sharp boxes, 1-pixel paper-graph backgrounds, and deep charcoal and cyan academic color palettes.

## Core Features & Mechanics
1. **LCA & Color Distances:** The engine relies on finding the Lowest Common Ancestor (LCA) string array between a user's guess taxonomy and the answer's taxonomy. 
2. **Color Legend:**
   - 🟥 **Red:** Very distant (diverged early on).
   - 🟨 **Yellow:** Getting closer (matching at the Class or Order level).
   - 🟩 **Green:** Very close (matching at the Family or Genus level).
   - 🟦 **Cyan:** Direct hit (Answer found).
3. **Daily PRNG:** A seeded random number generator tied specifically to the current daily timestamp picks a precise animal uniformly for everyone on earth on a given day.
4. **Mobile Adaptability:** The tree utilizes vertical stagger layout calculations (`layoutNode()`) specifically targeting smaller screens in order to maximize readability without the horizontal spreading forcing a need to pinch/pan awkwardly.

## Development Rules & Workflow
- **Mobile First Focus:** Any wide-ranging layout or interactive change MUST consider mobile screens (~320px-375px widths).
- **Physical Test Confirmations:** Never push layout adjustments directly to GitHub without the author testing and verifying the changes first on an actual physical mobile device (Developer Tools emulation is discouraged for final validation). All pushes must wait for an explicit "push it" command.
- **Dependency Free:** Keep relying on vanilla/built-in elements (like `<details>`, native SVG layout) to prevent library bloat. We aggressively removed `panzoom` for instance, to ensure native touch and performance superiority.

## Git Workflow
Changes should be tracked using local commits, pushed remotely via GitHub.
1. `git add .`
2. `git commit -m "Update descriptive message"`
3. `git push origin main`

## Future Concepts
- Scalability to 1000+ animal trees while maintaining rendering efficiency.
- Persisting long-lasting game streaks via `localStorage`.
