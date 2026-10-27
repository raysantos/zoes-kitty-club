# Zoe's Kitty Club — handoff for Claude Code

A pet cat care game Ray built for his daughter Zoe. Single-file web app (`index.html`), no build step, no dependencies besides Google Fonts. Progress saves to `localStorage` (key `kitty-cuddle-club-v1`).

## First job: publish to GitHub Pages

The repo has not been created on GitHub yet. Commit history is included in `zoes-kitty-club.bundle`.

1. Restore the history (from the folder holding these files):
   ```
   git clone zoes-kitty-club.bundle zoes-kitty-club
   cd zoes-kitty-club
   git remote remove origin
   ```
   If the bundle isn't usable, just `git init -b main` in a folder with `index.html`, `README.md` and this file and make a first commit.
2. Create the public repo and push:
   ```
   gh repo create raysantos/zoes-kitty-club --public --source . --push --description "Zoe's Kitty Club - a pet cat care game"
   ```
3. Turn on Pages from the `main` branch root:
   ```
   gh api -X POST repos/raysantos/zoes-kitty-club/pages -f "source[branch]=main" -f "source[path]=/"
   ```
4. Live URL (about a minute after): https://raysantos.github.io/zoes-kitty-club/

Commit attribution Ray uses: author `Ray Santos <raysandesigns@gmail.com>`.

## What the game does

- **Cat care loop:** five needs that slowly drop every 4s: Tummy (`hunger`), Fun, Clean, Energy, Potty. Actions earn XP and fish treats (coins). Happy cats (average need > 70) earn 1.5x XP.
- **Actions** (bottom dock, fly-out groups):
  - Care → Feed (bowl slides in, cat chomps), Bath (tub slides in, soap bubbles; tap anywhere to make more), Potty (litter box scene, sand kicks).
  - Fun → Play (3 random animations: yarn roll + pounce, toss + chase, flip), Park (park scene with sun, tree, butterflies, flowers; cat walks).
  - Nap → room goes dark with moon and stars; dock shows only Wake. Energy refills; auto-wakes at 100.
  - Style → wardrobe sheet (fur colors, accessories).
- **Tap the cat** to pet it: happy face, head bob, "purr~"/"meow!" words.
- **Level badge** (top left) opens the cat collection sheet: level progress, need percentages, **Name your cat** (per-cat names in `S.names`), cat cards, Start over.
- **Leveling:** XP needed = `20 + level*10`. Level ups fire confetti and a toast.
- **Unlocks:**
  - Cats: Mochi (1), Tiger Lily tabby (3), Pearl siamese (5), Patches calico (7), Luna tuxedo (9), Stella galaxy (12).
  - Furs: Cream, Snow, Ginger (1), Smoky (2), Bubblegum (4), Mint (6), Midnight (8), Lilac (10), Sparkle Gold (14).
  - Accessories: Bow (2), Bell collar (3), Flower (5), Heart glasses (7), Tiara (10), Royal crown (13).

## Code map (`index.html`)

- **CSS tokens** on `:root`: palette, fonts (Chewy display, Nunito body), and layout vars `--dock`, `--catw`, `--tubw`, `--boxw` that position the cat, tub and litter box relative to the dock.
- **Layout:** full-screen `.stage` (the room) with `.hud` (level pill, coins, ring meters) floating on top and `.dock` at the bottom. Sheets (`#s-style`, `#s-cats`) slide up. While a sheet is open, `.stage.dressing` lifts the cat above it.
- **Tablet media queries** (`min-width:700px and min-height:600px`): larger controls. In landscape (`min-aspect-ratio:5/4`) sheets become a right-side panel.
- **Scenes** are classes on `.stage`: `bathing`, `potty`, `park`, `night`, `dressing`. The cat gets `inbath`, `inpotty`, `inpark`, `walk`, `sleep`, `happy`, `eating`.
- **Key JS:**
  - `catSVG(breed, fur, acc, {sleep, face, uid})` draws the cat as SVG. Groups `.tail`, `.body`, `.head`, `.ear.l/.r`, `.blink`, `.whisk` are animated by CSS. `face` is `happy`, `eat` or null; `uid` keeps gradient ids unique per SVG.
  - `drawCat()` redraws only when breed, fur, accessory, sleep or face changes, so CSS idle animations aren't reset by the 4s tick.
  - `act(kind)` handles all actions. `startBath`, `startPotty`, `startPark` and `feedAnim` set `BUSY` during scenes.
  - `gain(xp)` handles leveling and unlock toasts. `nm(b)` returns the custom or default cat name.
  - `render()` / `renderStats()` refresh the UI.
- **State `S`:** `{xp, lvl, coins, breed, fur:{breedId:furId}, names:{breedId:name}, acc, stats:{hunger,fun,clean,energy,potty}, asleep}`. Old saves are merged with `fresh()` so new fields don't break them.

## Ideas Ray hasn't asked for yet

- Treat shop to spend fish treats (toys, room decorations).
- Sound effects (meow, purr, bubbles) behind a mute toggle.
- Progress lives per browser. Cross-device saves would need a backend; Ray has used Supabase on other projects.
