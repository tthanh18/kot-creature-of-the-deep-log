---
name: kot-battle-log
description: >-
  From one KOT battle screenshot, reply with exactly 3 separate lines of plain
  text (real line breaks). No markdown report. Map/fish from embedded JSON.
---

# OUTPUT RULES (HIGHEST PRIORITY)

Your **entire** reply is **exactly 3 lines** of plain text.

Each line is **different**. Use a **real line break** (newline) after line 1 and after line 2.

## The 3 lines (structure)

**Line 1** — date, clans, result (ends after `Win` or `Lose`):

`[DD/MM/YYYY] KOT {SSS} vs [opponent] -> Win`

**Line 2** — map and full fish list (starts with `Map:`):

`Map: [map_name] - [fish1], [fish2], [fish3], [fish4]`

**Line 3** — top 3 SSS players (starts with `Top Battle:`):

`Top Battle: [name1], [name2], [name3]`

## Correct example (copy this shape — 3 lines, 2 newlines)

Line 1:
`19/05/2026 KOT {SSS} vs Angler's of the deep -> Win`

Line 2:
`Map: Alaska - Halibut, Humpback Salmon, Coalfish, Steelhead`

Line 3:
`Top Battle: HuyNgoo, Aizn, Lmao`

Rendered as the user must see it:

```
19/05/2026 KOT {SSS} vs Angler's of the deep -> Win
Map: Alaska - Halibut, Humpback Salmon, Coalfish, Steelhead
Top Battle: HuyNgoo, Aizn, Lmao
```

## WRONG — do NOT do this

**Wrong A — one long line (everything merged):**

`19/05/2026 KOT {SSS} vs Angler's of the deep -> Win Map: Alaska - Halibut, Humpback Salmon, Coalfish, Steelhead Top Battle: HuyNgoo, Aizn, Lmao`

**Wrong B — same merged line repeated 3 times:**

```
19/05/2026 KOT {SSS} vs Angler's of the deep -> Win Map: Alaska - ... Top Battle: HuyNgoo, Aizn, Lmao
19/05/2026 KOT {SSS} vs Angler's of the deep -> Win Map: Alaska - ... Top Battle: HuyNgoo, Aizn, Lmao
19/05/2026 KOT {SSS} vs Angler's of the deep -> Win Map: Alaska - ... Top Battle: HuyNgoo, Aizn, Lmao
```

**Wrong C — markdown report** (`#` headers, bullets, scores like `1831P`, top 5 list).

## Line-break checklist

Before sending, verify:

1. Line 1 contains `KOT {SSS} vs` and ends with `-> Win` or `-> Lose` — **nothing after that on the same line**.
2. Line 2 **starts** with `Map:` — not on line 1.
3. Line 3 **starts** with `Top Battle:` — not on line 1 or 2.
4. You output the block **once** — not 3 copies of the same text.

## Field rules

| Field      | Rule                                                                     |
| ---------- | ------------------------------------------------------------------------ |
| Our clan   | Always `{SSS}` in the log (never `King Of Thieves`, never `{S}`)         |
| Result     | `Win` or `Lose` (not `WIN` / `LOSE`)                                     |
| Opponent   | Clan name on the **right** of the image, no tag                          |
| Win/Lose   | Compare scores: **left** (SSS) vs **right**; left higher → `Win`         |
| Date       | From user prompt, else today — format `DD/MM/YYYY`                       |
| Map line   | Full fish array from JSON (4 fish), not only the fish name the user said |
| Top Battle | Exactly 3 player names, comma-separated — no points, no ranks `1.` `2.`  |

Only if data is missing: after the 3 lines you may add **one** short question. Still no markdown report.

---

# How to read the screenshot

- **Left** = our clan (SSS). **Right** = opponent.
- SSS may show as `{sSs}`, `{S}`, or **King Of Thieves** on screen — log always `{SSS}`.
- Top Battle = top 3 SSS players on the image; keep exact capitalization.

# Map / fish matching (embedded JSON below)

User input `alaska, halibut` = map **Alaska** + first fish **Halibut** → use fish set  
`["Halibut", "Humpback Salmon", "Coalfish", "Steelhead"]`  
Do **not** use `fish[0]` (Arctic Char…) when user gave both map and first fish.

| User says        | Match                                                                       |
| ---------------- | --------------------------------------------------------------------------- |
| Map only         | `fish[0]` for that map                                                      |
| First fish only  | Find `fish[i][0]` match → use full `fish[i]` + `map_name`                   |
| Map + first fish | In that map, find `fish[i]` where `fish[i][0]` matches → use full `fish[i]` |

If map/fish unknown: line 2 = `Map:  - `, then one short line listing available maps.

# GitHub prompt

```
https://raw.githubusercontent.com/tthanh18/kot-creature-of-the-deep-log/refs/heads/master/skill.md

[battle screenshot]
alaska, halibut
```

Optional: `Output exactly 3 lines with line breaks. No markdown.`
