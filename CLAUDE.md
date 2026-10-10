# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas + CSS). No `package.json`, no build, no tests, no linter. UI text and README are in Spanish.

## Running

Open `index.html` directly, or serve statically (e.g. `python -m http.server 8000`) and visit `http://localhost:8000`.

## Architecture

Three files: `index.html` (DOM + two canvases), `style.css`, and `game.js` (all logic, one script loaded via plain `<script>`, `'use strict'`, global state).

- **Board model**: `board` is a `ROWS × COLS` matrix; `0` = empty, `1–7` = index into both `COLORS` and `PIECES` (the same index identifies piece type, color, and the cell value stored on lock).
- **Game state** is a set of module-level `let` variables (`board, current, next, score, lines, level, paused, gameOver, ...`) reset by `init()`. The restart button simply calls `init()`.
- **Loop**: `loop(ts)` via `requestAnimationFrame` accumulates `dropAccum` and gravity-drops when it exceeds `dropInterval`. Pause/game-over work by `cancelAnimationFrame(animId)`; unpausing resets `lastTime` and re-enters `loop`.
- **Piece lifecycle**: `lockPiece()` → `merge()` → `clearLines()` (updates score/level/`dropInterval`) → `spawn()`. `spawn()` calls `endGame()` if the new piece collides immediately.
- **Rotation**: `rotateCW` + `tryRotate` with simple horizontal kicks `[0,-1,1,-2,2]` (not SRS).
- **Canvas size coupling**: `#board` width/height in `index.html` (300×600) must equal `COLS*BLOCK × ROWS*BLOCK`; the next-piece canvas (120×120) assumes a 4×4 grid at 30px (`NB` in `drawNext`).
- Drop interval formula is duplicated: initial `1000` in `init()` and `max(100, 1000 - (level-1)*90)` in `clearLines()`.
