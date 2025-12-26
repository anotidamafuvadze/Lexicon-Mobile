# Lexicon (Mobile)

## Overview

**Lexicon** is a React Native word-puzzle game that blends **2048-style sliding and merging** with **word formation**. Players swipe tiles on a 4×4 grid to merge matching letters (A → B → C …) and attempt to form a target word in any full row or column.

The app includes tile locking (double-tap), limited tile removal (“pops”, long-press), themable word banks, persistent game state, and smooth Reanimated-driven UI.

---

## Features

### Sliding & Merging
- Swipe up/down/left/right to slide tiles; adjacent identical letters merge into the next letter.
- Merge scoring follows a per-letter points table.

### Target Word & Themes
- A target word is selected from themed word banks.
- Completing a full row or column matching the target word wins the game.

### Locking Tiles
- Double-tap a tile to lock it (excluded from moves/merges).
- Double-tap again to unlock.

### Pop (Remove) Tiles
- Long-press a tile to pop (remove) it if pops remain.
- Pops are limited (`STARTING_POPS`).

### Persistence
- Game state and selected target word persist via AsyncStorage across restarts.

### Animated UI & Accessibility
- Movement, merge, and pop animations use Reanimated shared values.
- Accessible labels and hints are included for tiles and swipe gestures.

### Clean Component Structure
- Contexts and a reducer centralize game logic while UI components remain declarative and composable.

---

## Tech Stack

### Frameworks
- React Native (Expo-compatible project layout)
- TypeScript

### State & Architecture
- React Context + `useReducer`

### UI & Gestures
- `react-native-reanimated`
- `react-native-gesture-handler`
- `react-native-swipe-gestures`

### Storage & Utilities
- `@react-native-async-storage/async-storage`
- `lodash`
- `uid`

### Native Tooling (iOS)
- CocoaPods

---

## Architecture & Data Flow

### Contexts
- **GameContext**
  - Exposes game state, dispatch, move handlers, locking/popping, and win/loss checks.
- **WordContext**
  - Selects and persists the current `targetWord` from themed word banks.

### Reducer
- `gameReducer` implements core actions:
  - `CREATE_TILE`
  - `CLEAN_UP`
  - `MOVE_UP`, `MOVE_DOWN`, `MOVE_LEFT`, `MOVE_RIGHT`
  - `POP_TILE`
  - `RESET_GAME`
  - `UPDATE_STATUS`
- The board is represented as a 2D array of optional tile IDs plus a `tiles` map storing tile metadata.

### Movement Flow
1. `GameGrid` detects a swipe and calls `moveTiles` in `GameContext`.
2. The reducer compacts and merges tiles in the move direction, updates positions and score, and respects locked tiles.
3. After animations, the app dispatches `CLEAN_UP` and `CREATE_TILE` to spawn a new `'A'` tile.

### Win Detection
- Each row and column is scanned for an exact match to the `targetWord`.
- If found, game status becomes **WON** and winning tiles animate.

### Persistence
- Game state and target word are saved to AsyncStorage.
- On launch, the app attempts to restore the saved state.

---

## How the Game Plays (Brief)

- **Start:** Board resets and two `'A'` tiles are created.
- **Objective:** Form the current target word in a full row or column.
- **Controls:**
  - Swipe to move tiles.
  - Double-tap a tile to lock/unlock.
  - Long-press (hold) to pop a tile (consumes one pop).
- **Failure:** When the board is full, no merges are possible, and no pops remain, the game is lost.

