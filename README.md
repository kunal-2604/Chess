# Chess — Pygame Chess with Basic AI

## Overview
A small Python chess project using Pygame for the GUI and a simple AI engine for automated play. The project includes:

- a Pygame front-end and input/animation loop,
- a move-generation and game-state engine,
- a basic AI using negamax/alpha-beta search.

## Repository Structure
- [AI_player.py](AI_player.py) — AI routines: random move picker, minimax/negamax variants, alpha-beta pruning, and board scoring.
- [ChessEngine.py](ChessEngine.py) — `GameState`, move generation, castling/en-passant/pawn-promotion support, and the `Move` class.
- [ChessMain.py](ChessMain.py) — Pygame UI, main loop, input handling, animation, and simple game controls.
- `images/` — piece art (expected files: `wp.png`, `wR.png`, `wN.png`, `wB.png`, `wK.png`, `wQ.png`, and black equivalents).

## Requirements
- Python 3.8+ (tested on 3.8/3.9)
- Pygame

Install dependencies using the included requirements file:

```bash
python -m pip install -r requirements.txt
```

Or run the provided installer scripts on Windows:

```powershell
install_requirements.bat
# or
.\install_requirements.ps1
```

## Running
From the project root (the folder that contains `ChessMain.py`) run:

```bash
python ChessMain.py
```

Notes:
- By default both `playerOne` and `playerTwo` in `ChessMain.py` are set to `False`, which allows the AI to play both sides. Set `playerOne = True` to play White as a human, or `playerTwo = True` to play Black.
- Make sure you run the script from the repository root so the `images/` path resolves correctly.

## Controls
- Mouse: click source square then destination square to make a move.
- `z` key: undo last move.
- `r` key: reset the board.

## AI details
- Implemented in [AI_player.py](AI_player.py).
- Piece values: King=0, Queen=10, Rook=5, Bishop=3, Knight=3, pawn=1.
- Main search: negamax with alpha-beta pruning (`findMoveNegaMaxAlplaBeta`), search depth controlled by the `DEPTH` constant (default 3).
- `findBestMove` is the public helper used by `ChessMain.py` to request the AI move.

## Notable code observations & potential issues (analysis)
Below are things I observed while reviewing the three files; these are suggestions and potential bugs you may want to fix before running or distributing the project.

### ChessEngine.py
- Top imports `from os import get_handle_inheritable` and `from turtle import color` appear unused and can be removed.
- In `makeMove`: the en-passant detection line uses `abs(move.startRow - move.endCol) == 2` — the second operand looks wrong (should be `move.endRow`). This will break en-passant logic and can cause incorrect coordinates.
- Several variable names contain typos (for example `castleightslog`). They are used consistently in places, but consider renaming for clarity (`castlingRightsLog`).
- Comments and variable names sometimes misspell "ally" as "allay" — purely cosmetic, but cleaning helps readability.

### AI_player.py
- There are multiple typos and logical issues that may raise exceptions or cause incorrect behavior:
  - `from typing import Counter` is unused.
  - `findMoveMinMax` calls `gs.makeMoves(move)` (plural) — should be `gs.makeMove(move)`.
  - `findBestMoveMinMaxNoRecursion` uses the variable `score` in a branch where it may be uninitialized (logic bug).
  - Function names contain misspellings (e.g., `findMoveNegaMaxAlplaBeta`) — the implementation appears to attempt alpha-beta pruning but the misspelled names and some variable handling may confuse future maintenance.
- `scoreBoard` and `scoreMaterial` are both present; some search functions call `scoreBoard` and others use `scoreMaterial` — double-check which evaluation is intended at leaf nodes.
- The global `nextMove` and `counter` pattern works but is fragile; returning the best move directly from the search is safer.

### ChessMain.py
- Default `playerOne = False` and `playerTwo = False` means both players are AI by default — that may be surprising. If you expect a human player, set `playerOne = True` for White or `playerTwo = True` for Black.
- `IMAGES` load expects `images/<piece>.png` files matching the naming used in the board (e.g., `wp.png`, `wR.png`). Ensure that folder exists with correct filenames.

## Suggested improvements
- Fix the typos and incorrect expressions (particularly the en-passant row/col bug and `makeMoves` typo).
- Add a `requirements.txt` or `pyproject.toml` to pin `pygame`.
- Make AI options configurable (CLI args or a small settings object) for `DEPTH` and which side is AI vs human.
- Replace global `nextMove`/`counter` with return values from the search functions.
- Add basic unit tests for move generation and legal move filtering (helps catch regressions in castling/en-passant/promotion logic).
- Improve logging and error handling around image loading.

## Quick troubleshooting
- "No module named pygame": run `pip install pygame`.
- "File not found" for piece images: ensure you run from the project root so `images/` is found.
- Unexpected crashes during AI search: check the typos described above in `AI_player.py` and `ChessEngine.py`.

## Contributing / Next steps
If you want, I can:
- fix the bugs I noted and add a `requirements.txt`, or
- add CLI flags to configure AI depth and human/AI players, or
- run the game locally and iterate until it runs cleanly on your system.

---
Created by an automated code review — ask if you want me to implement the fixes now.
