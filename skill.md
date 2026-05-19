---
name: kot-battle-log
description: >-
  Phân tích 1 ảnh kết quả King of Thieves (KOT) clan battle và xuất log theo
  template cố định; map/cá lấy từ JSON nhúng trong skill (hỏi user nếu thiếu).
  Dùng khi user gửi screenshot KOT, battle result, clan vs clan, hoặc nhắc
  SSS / log KOT / template KOT.
---

# KOT Battle Log

Khi user gửi **một** ảnh kết quả trận KOT, đọc ảnh và trả **đúng một block log** theo template bên dưới. Không giải thích dài; chỉ thêm ghi chú ngắn nếu thiếu dữ liệu bắt buộc.

## Clan của user

- Tag clan: **SSS** (cũng có thể hiện `{sSs}`, `S`, hoặc tên đầy đủ **King Of Thieves**).
- Trong log luôn dùng `{SSS}` cho `[clan_name]`, không dùng tên đầy đủ.
- Trên ảnh, **bên trái** là phe mình (SSS); **bên phải** là clan đối thủ.

## Cách đọc ảnh (1 ảnh)

Ảnh thường có layout **clan trái vs clan phải**, mỗi tên clan có **điểm số ngay bên dưới** (hoặc sát tên).

| Lấy gì      | Cách đọc                                                                                   |
| ----------- | ------------------------------------------------------------------------------------------ |
| Tên đối thủ | Tên clan **bên phải** (không kèm tag trong ngoặc)                                          |
| Win / Lose  | So điểm hai phe: phe **trái** (SSS) cao hơn → `Win`; thấp hơn hoặc bằng → `Lose`           |
| Top Battle  | Nếu ảnh có bảng top player phe SSS → top 3; không có → xem [Thiếu dữ liệu](#thiếu-dữ-liệu) |

**Win/Lose:** Ưu tiên so **điểm số** dưới mỗi tên clan. Chỉ dùng nhãn `WINNER!` / highlight nếu điểm không đọc được.

**Ngày:** Dùng ngày user nói trong prompt (vd. "hôm nay"); không có thì dùng ngày hiện tại. Format `DD/MM/YYYY`.

**Map:** Không có trên ảnh và user **không** nói map/cá trong prompt → xem [Mapping map/cá](#mapping-mapcá).

**Top Battle:** Chỉ top 3 player phe SSS (nếu có trên ảnh); giữ đúng chữ hoa/thường tên trong game.

## Template (bắt buộc)

```
[DD/MM/YYYY] KOT {[clan_name]} vs [another_clan_name] -> [Win, Lose]
Map: [map_name] - [fish_list hoặc để trống]
Top Battle: [top1], [top2], [top3]
```

- `[another_clan_name]`: tên clan đối thủ **bên phải**, không kèm tag, vd. `Angler's of the deep`.
- Mỗi lần chỉ xuất **một** log hoàn chỉnh, copy-paste được.
- Ba dòng log cách nhau bằng xuống dòng thật (không dùng ký tự `\n`).

## Mapping map/cá

**Luôn** dùng JSON trong section [mapping-battle.json](#mapping-battlejson) cuối file này (kể cả khi load skill qua URL raw). Không fetch file ngoài.

Mỗi entry: `{ "map_name", "fish": [[cá1, cá2, ...], ...] }` — mỗi map có **nhiều bộ cá** (`fish[0]`, `fish[1]`, …).

### Quy tắc match

| User gửi | Cách match |
| -------- | ---------- |
| Chỉ tên map (vd. `Alaska`) | Khớp `map_name` → lấy **`fish[0]`**. |
| Chỉ tên cá đầu (vd. `Halibut`, `Coho Salmon`) | Tìm `fish[i][0]` trùng (không phân biệt hoa thường). Một kết quả → lấy `map_name` + `fish[i]`. Nhiều kết quả → hỏi user chọn map. |
| **Map và cá đầu** (vd. `alaska, halibut`) | Trong entry map khớp, tìm `fish[i]` có `fish[i][0]` khớp cá đầu → lấy **cả `fish[i]`**. **Không** lấy `fish[0]` chỉ vì trùng tên map. |
| Map + cá đầu không khớp | Báo không tìm thấy, list các `fish[i][0]` của map đó. |

**Điền dòng Map:** `Map: [map_name] - [cá1], [cá2], ...` — join cả mảng `fish[i]` đã match, phân tách `, `.

**User đã nói map/cá kèm ảnh:** match JSON → xuất **đủ 3 dòng một lần**, không hỏi thêm.

**User chưa nói map/cá:** xuất log với `Map:  - `, list `map_name`, hỏi map hoặc tên cá đầu.

## Cách dùng từ GitHub

1. **Đính kèm ảnh** battle.
2. Prompt: URL `skill.md` (raw) hoặc `@skill.md` + map/cá, vd. `alaska, halibut`.

## Ví dụ

**Input:** Ảnh — SSS thắng, phải **Angler's of the deep**; top: HuyNgoo, Aizn, Lmao. User: `alaska, halibut`.

**Output:**

```
19/05/2026 KOT {SSS} vs Angler's of the deep -> Win
Map: Alaska - Halibut, Humpback Salmon, Coalfish, Steelhead
Top Battle: HuyNgoo, Aizn, Lmao
```

**Input:** Ảnh — SSS **11349**, Moczykije z Polski **11002**; top: Con mẹ mày, Hung, Meo meo. User: `Amazon`.

**Output:**

```
17/05/2026 KOT {SSS} vs Moczykije z Polski -> Win
Map: Amazon - Amazon Puffer, Rock-Bacu, Cachama, Corvina
Top Battle: Con mẹ mày, Hung, Meo meo
```

**Input:** Cùng ảnh, chưa nói map → `Map:  - `, list map, hỏi user.

**Input:** User: `Coho Salmon` → `Map: Great Lake - Coho Salmon, Brook Trout, Channel Catfish, Largermouth Bass`.

## Thiếu dữ liệu

- Thiếu top trên ảnh → `Top Battle: (cần top 3 player SSS)` hoặc hỏi user.
- Thiếu tên đối thủ / điểm → nêu rõ, không đoán Win/Lose.
- Không bịa tên player hay điểm.
- Cá/map không khớp JSON → báo + list `map_name` hoặc cá đầu của map; không đoán fish list.

## mapping-battle.json

```json
[
  {
    "map_name": "Paradise",
    "fish": [
      ["Bluefish", "Longtail Tune", "Largetooth Flounder", "Spot-Fin Porcupinefish"],
      ["White-Tuna", "Green Humphead Parrotfish", "Clownfish", "Blue Trevally"],
      ["Bonefish", "Blue Trevally", "Pelagic Stingray", "Snubnose Pompano"]
    ]
  },
  {
    "map_name": "Great Lake",
    "fish": [
      ["Coho Salmon", "Brook Trout", "Channel Catfish", "Largermouth Bass"],
      ["White bass", "Yellow Perch", "Sea lamprey", "Chinook Salmon"],
      ["Lake Trout", "Brook Trout", "Pink Salmon", "Lake Sturgeon"]
    ]
  },
  {
    "map_name": "Costa Rica",
    "fish": [
      ["Roosterfish", "Dorado", "Tarpon", "Yellowfin Tuna"],
      ["Blue Marlin", "Snook", "Barracuda", "Pompano"],
      ["Pacific Sailfish", "Broomtail Grouper", "Jack Crevalle", "Striped Marlin"]
    ]
  },
  {
    "map_name": "Alaska",
    "fish": [
      ["Arctic Char", "Dolly Varden", "Spiny Skate", "Rougheye Rockfish"],
      ["Halibut", "Humpback Salmon", "Coalfish", "Steelhead"],
      ["King Salmon", "Blue Lingcod", "Chum Salmon", "Lancetfish"]
    ]
  },
  {
    "map_name": "Australia",
    "fish": [
      ["albacore", "golden trevally", "queensland grouper", "black-saddler coral grouper"],
      ["barramundi", "tailor", "coral trout", "giant trevally"],
      ["skipjack tuna", "john dory", "carpet shark", "swordfish"]
    ]
  },
  {
    "map_name": "Scotland",
    "fish": [
      ["Rainbow Trout", "european whitefish", "carp", "freshwater bream"],
      ["tench", "european perch", "european eel", "sea trout"],
      [""]
    ]
  },
  {
    "map_name": "Thailand",
    "fish": [
      ["spotted sorubim", "empurau", "bambusa", "great snakehead"],
      ["black ear catfish", "bighead carp", "malayan leaffish", "wallago"],
      []
    ]
  },
  {
    "map_name": "Amazon",
    "fish": [
      ["Amazon Puffer", "Rock-Bacu", "Cachama", "Corvina"],
      ["Red Piranha", "freshwater barracuda", "Giant Trahira", "Zungaro"]
    ]
  }
]
```
