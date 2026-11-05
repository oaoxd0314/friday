---
name: travel-plan
description: This skill should be used when the user says "建立旅行計畫", "create travel plan", "新增旅遊規劃", "plan a trip to X", "幫我規劃旅行", or wants to create a structured travel plan in Obsidian. Creates a complete travel plan folder with a main overview file, per-day itinerary files, optional scope research files, and an overview.canvas whiteboard that displays all cards at once.
version: 0.1.0
---

# Travel Plan Skill

Create structured travel plans in the Obsidian vault under `plan/` following the established format. Each trip lives in its own folder and is visualized in a canvas whiteboard.

## Workflow

### Step 1: Gather Required Information

Before creating any files, confirm:
- Destination and trip name (e.g., `東京`)
- Year + month (e.g., `2026 05`)
- Travel dates and number of days (exact or estimated)
- A one-line theme for each day (placeholder `待定` if unknown)
- Any scope topics already identified (shopping, attractions, food lists, etc.)

### Step 2: Create Folder

```bash
mkdir -p "plan/YYYY MM [destination]"
```

### Step 3: Create Draft File

**Path**: `plan/YYYY MM [destination]/draft.md`

The draft is the raw research dump — a flat categorical list of everything the user has gathered before the schedule is finalized. Think of it as a brain-dump notepad, not a structured plan.

Use the template in `references/templates.md` (`## Draft File Template`).

Key rules:
- Organize by category: 住, 景點 (by area/sub-region), 美食, 購物, etc.
- No dates or time slots — those go in day files later
- Add notes inline (e.g., "吃完要去味", reservation links, disclaimers like ⚠️)
- If the user provides pre-researched data at creation time, populate it now; otherwise leave placeholder stubs

### Step 4: Create Main Overview File

**Path**: `plan/YYYY MM [destination]/YYYY MM [destination].md`

Use the template in `references/templates.md` (`## Main Overview Template`).

Key rules:
- Frontmatter `tags:` uses the trip slug (e.g., `2026東京`)
- `# 預定資訊` holds 住宿資訊, 餐廳清單 (by day), 票卷清單 (Tasks checkboxes)
- `## 行程` lists each day as a link to its day file
- Use `(待確認)` for anything not yet decided

### Step 5: Create Day Files

**Naming**: `MM-DD Day N - [theme].md`  
(Use `-` not `/` — `/` is invalid in filenames; the owner does not want `!`)

One file per day. If theme is unknown, use `待定` as placeholder.

Use the template in `references/templates.md` (`## Day File Template`).

Key rules:
- Same `tags:` as the main file
- `## 時間表` section always present with the table header + one placeholder row — **do not pre-fill content**; research lives in `draft.md` and gets moved here when the schedule is confirmed
- Add `## 班機資訊` only on departure/arrival days
- Use `(待確認)` for anything unconfirmed

### Step 7: Create Scope Files (Optional at Start)

Scope files = topic-specific research notes. Examples:
- `Check List.md` — pre-trip checklist
- `flight.md` — flight options research
- `交通攻略.md` — transport details

Create only the ones already needed. Add more later as research accumulates. `draft.md` covers general attraction/food research so separate topic scope files are only needed for distinct domains (logistics, checklists, etc.).

### Step 8: Create Canvas

**Path**: `plan/YYYY MM [destination]/overview.canvas`

The canvas is the whiteboard — it displays all .md files as cards simultaneously.

Use the positioning formula in `references/canvas-layout.md` to compute node coordinates.

Layout:
- Row 0 (top): Main overview file, centered
- Row 1 (middle): Day files, left to right in order
- Row 2 (bottom): Scope files, left to right

## Minimal vs. Expanded Structure

**Minimal (start here):**
```
plan/YYYY MM [dest]/
├── YYYY MM [dest].md     ← main overview
├── draft.md              ← raw research dump (attractions, food, shopping)
├── MM-DD Day 1 - 待定.md
├── MM-DD Day 2 - 待定.md
├── ...
└── overview.canvas
```

**Expanded (add as needed):**
```
plan/YYYY MM [dest]/
├── YYYY MM [dest].md
├── draft.md              ← always present; grow as research accumulates
├── MM-DD Day N - [theme].md  (one per day, filled in from draft)
├── Check List.md
├── [scope].md               (any number of research notes)
└── overview.canvas          (updated to include new files)
```

To add a new scope file: create the `.md`, then add a new node to `overview.canvas`.

## Flight Research Integration

When the user provides specific flight dates (or a date range), trigger the **find-flight** skill to research flights as part of the planning process.

**When to trigger find-flight:**
- Dates are confirmed → run find-flight at Level 3 (指令 01 + 03 + 04 → 決策)
- Date range given but flexible → run find-flight at Level 2 (指令 02 + 01)
- No dates yet → skip for now; remind the user to run find-flight once dates are narrowed down

**After find-flight results:**
- Update the `票卷清單` in the main overview file with the recommended flight
- Fill in `班機資訊` in the Day 1 (departure) and last day (return) files

## Conventions

- All content in Traditional Chinese (繁體中文)
- `-` separator in day filenames (never `!` or `/`)
- Wikilinks `[[filename]]` for cross-referencing between files in the same trip
- Tasks plugin checkboxes (`- [ ]`) for items that need booking/confirmation
