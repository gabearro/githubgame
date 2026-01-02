# Git Tic-Tac-Toe

Play tic-tac-toe by editing a JSON file. No servers, no frontend—just git.

## Play Now

**[Start a new game](../../issues/new?title=new%20game:%20player1%20vs%20player2)** — replace `player1` and `player2` with GitHub usernames.

A bot creates the game branch. You'll get notified on every move.

## Make a Move

```bash
# 1. Fork this repo, then:
git clone https://github.com/YOUR_USERNAME/githubgame
cd githubgame
git fetch origin game/you-vs-opponent-123-abc123
git checkout -b my-move origin/game/you-vs-opponent-123-abc123

# 2. Edit game.json — place your symbol, update the turn
# 3. Push and open a PR to the game branch
git add game.json && git commit -m "X takes center"
git push -u origin my-move
```

Open a PR targeting the game branch. If your move is valid, it auto-merges. If not, you'll see why.

### Board

```
 0 | 1 | 2
---+---+---
 3 | 4 | 5
---+---+---
 6 | 7 | 8
```

### Example Move

Before (your turn as X):
```json
{ "board": [null,null,null,null,null,null,null,null,null], "turn": "X", ... }
```

After (X takes center):
```json
{ "board": [null,null,null,null,"X",null,null,null,null], "turn": "O", ... }
```

---

## How It Works

The entire game is a single JSON file. GitHub Actions validates every move and auto-merges valid PRs.

**No game server.** GitHub *is* the game server.

### The Trick

GitHub can require status checks before merging. We use this to enforce game rules:

1. You open a PR with your move
2. A GitHub Action checks: Is it your turn? Is the move legal?
3. If valid → auto-merge. If not → PR blocked with explanation.

Your GitHub username is your identity. The Action checks that the PR author matches whose turn it is—you literally cannot submit a move for someone else.

### Architecture

```
main                              ← Workflows + template (protected)
 └── game/alice-vs-bob-42-x7k9   ← One branch per game
      └── PRs from players       ← Validated & auto-merged
```

### Notifications

Each game links to the issue that created it. Every move posts an update:

```
**alice** played X at cell 4

`·` `·` `·`
`·` `X` `·`
`·` `·` `·`

⏳ bob's turn (O)
```

Subscribe to the issue to follow along.

---

## Host Your Own

Fork this repo and configure:

### Rulesets

**`protect-main`** — Block direct pushes to main (protects the game logic)

**`game-branches`** — Block force pushes on `game/**` (prevents move rewrites)

### Permissions

- **Settings → Actions → Workflow permissions** → "Read and write"
- **Settings → Actions** → "Allow GitHub Actions to create PRs" (for issue notifications)

---

## Rules Enforced

- PR author must match current player
- Exactly one cell changes per move
- Correct symbol placed
- Turn advances correctly
- Winner/draw detected accurately
