# Git Tic-Tac-Toe

A turn-based game where moves are git commits and rules are enforced by GitHub Actions.

## How It Works

The game state lives in `game.json`. Players take turns by opening Pull Requests that modify this file. A GitHub Action validates each move before it can be merged.

**The key insight:** GitHub branch protection can require status checks to pass before merging. By making the status check a game rule validator, we turn GitHub into a game server that enforces turn order and move legality.

### Architecture

```
main                         <- Template + workflow (protected)
 └── game/alice-vs-bob       <- Game instance (protected, requires PR + CI)
      ├── move/alice-1       <- Alice's PR branch
      ├── move/bob-1         <- Bob's PR branch
      └── ...
```

- **`main`**: Contains the game template and validation workflow. Protected to prevent tampering.
- **`game/*`**: Each branch is an independent game. Protected by rulesets requiring validated PRs.
- **`move/*`**: Temporary branches for submitting moves via PR.

### GitHub Branch Protection Setup

Two rulesets enforce the game:

**1. `protect-main`** (targets: default branch)
- Require pull request before merging
- Block force pushes
- *Purpose: Prevent players from modifying the validation rules*

**2. `game-branches`** (targets: `game/**`)
- Require pull request before merging
- Require status checks to pass: `validate`
- Block force pushes
- Bypass: repo owner (to create new games)
- *Purpose: Force all moves through CI validation*

### Validation Workflow

The GitHub Action (`.github/workflows/validate-move.yml`) runs on every PR to a `game/*` branch and checks:

1. **Turn order**: PR author must match the player whose turn it is
2. **Single move**: Exactly one board cell changed
3. **Valid placement**: Cell was empty, correct symbol used
4. **State consistency**: Turn advanced correctly, winner detected if applicable

If any check fails, the PR cannot be merged.

## Playing the Game

### Starting a Game

A repo admin creates a game branch:

```bash
git checkout main
git checkout -b game/alice-vs-bob
# Edit game.json with player GitHub usernames
git push -u origin game/alice-vs-bob
```

### Making a Move

```bash
git fetch origin
git checkout game/alice-vs-bob
git pull
git checkout -b move/alice-1
# Edit game.json: place your symbol, update turn
git add game.json
git commit -m "X takes center"
git push -u origin move/alice-1
gh pr create --base game/alice-vs-bob --title "X takes center"
# Wait for CI to pass, then merge
```

### Board Layout

```
 0 | 1 | 2
---+---+---
 3 | 4 | 5
---+---+---
 6 | 7 | 8
```

### Game State Format

```json
{
  "board": [null, null, null, null, "X", null, null, null, null],
  "players": { "X": "alice", "O": "bob" },
  "turn": "O",
  "winner": null
}
```

- `board`: Array of 9 cells (null, "X", or "O")
- `players`: Maps symbols to GitHub usernames
- `turn`: Which symbol plays next
- `winner`: null, "X", "O", or "draw" when game ends
