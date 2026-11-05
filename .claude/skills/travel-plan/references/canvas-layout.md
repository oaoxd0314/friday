# Canvas Layout Formula

## Node Dimensions

| File Type      | Width | Height |
|---------------|-------|--------|
| Main overview | 500   | 700    |
| Day file      | 400   | 600    |
| Scope file    | 400   | 400    |

## Positioning

### Row 0 — Main overview file (centered at top)

```
x = -250   (= -width/2 to center at x=0)
y = -900
```

### Row 1 — Day files (horizontal row, left to right)

```
GAP = 50
DAY_W = 400

total_row_width = N * DAY_W + (N - 1) * GAP
start_x = -(total_row_width / 2)

Day i (0-indexed):
  x = start_x + i * (DAY_W + GAP)
  y = 0
```

**Example — 5 days:**
- total = 5×400 + 4×50 = 2200
- start_x = -1100
- Day 0: x=-1100, Day 1: x=-650, Day 2: x=-200, Day 3: x=250, Day 4: x=700

### Row 2 — Scope files (horizontal row, below days)

```
GAP = 50
SCOPE_W = 400

total_scope_width = S * SCOPE_W + (S - 1) * GAP
start_x = -(total_scope_width / 2)

Scope i (0-indexed):
  x = start_x + i * (SCOPE_W + GAP)
  y = 750   (= Row1_y + DAY_H + gap = 0 + 600 + 150)
```

## Canvas JSON Structure

```json
{
  "nodes": [
    {
      "id": "main",
      "type": "file",
      "file": "plan/YYYY MM [dest]/YYYY MM [dest].md",
      "x": -250,
      "y": -900,
      "width": 500,
      "height": 700
    },
    {
      "id": "day0",
      "type": "file",
      "file": "plan/YYYY MM [dest]/MM-DD Day 1 - theme.md",
      "x": [computed],
      "y": 0,
      "width": 400,
      "height": 600
    }
  ],
  "edges": []
}
```

## Node ID Convention

- Main file: `"main"`
- Day files: `"day0"`, `"day1"`, `"day2"`, ...
- Scope files: `"scope0"`, `"scope1"`, ...

Use simple sequential IDs — no need for complex random strings unless Obsidian complains (it usually doesn't).

## Updating Canvas When Adding New Files

When a new scope file is added to the trip folder, append a new node to the `"nodes"` array in `overview.canvas`. Recompute scope row positions if needed to keep spacing even.
