# Git Tic-Tac-Toe

Turn-based tic-tac-toe played via GitHub PRs.

## Players
- **X**: amiller
- **O**: xiaziyna

## How to Play

1. Check `game.json` to see if it's your turn
2. Create a branch: `git checkout -b my-move`
3. Edit `game.json`:
   - Place your symbol (X or O) in an empty cell
   - Update `turn` to the other player's symbol
   - If you won, set `winner` to your symbol
4. Push and open a PR to `main`
5. If the move is valid, CI passes and you can merge

## Board Layout
```
 0 | 1 | 2
---+---+---
 3 | 4 | 5
---+---+---
 6 | 7 | 8
```

## Rules Enforced by CI
- Only the current player can make a move
- Exactly one empty cell must be filled per turn
- Must use correct symbol (X or O)
- Must correctly advance the turn
- Must correctly detect winner/draw
