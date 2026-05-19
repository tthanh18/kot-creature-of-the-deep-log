---
name: kot-battle-log
description: >-
  Xuất ĐÚNG 3 dòng plain-text log KOT từ ảnh battle — không báo cáo markdown.
  Map/cá từ JSON nhúng. Dùng khi screenshot KOT, SSS, log KOT, alaska halibut.
---

# OUTPUT — ĐỌC TRƯỚC, ƯU TIÊN CAO NHẤT

**Toàn bộ reply = đúng 3 dòng log.** Không thêm bất kỳ ký tự/dòng nào khác.

```
[DD/MM/YYYY] KOT {SSS} vs [đối thủ] -> Win
Map: [map] - [cá1], [cá2], [cá3], [cá4]
Top Battle: [tên1], [tên2], [tên3]
```

| Bắt buộc | Chi tiết |
| -------- | -------- |
| Số dòng | **3** — không 2, không 4+ |
| Clan mình | Luôn `{SSS}` — không `King Of Thieves`, không `{S}` |
| Kết quả | `Win` hoặc `Lose` — không `WIN`, `LOSE`, `WINNER` |
| Map | `Map: Alaska - Halibut, Humpback Salmon, Coalfish, Steelhead` — **đủ 4 cá** từ JSON, không chỉ 1 cá user nói |
| Top Battle | **3 tên**, phân tách `, ` — **không** điểm (`1831P`), **không** top 5, **không** xếp hạng `1.` `2.` |

### CẤM (vi phạm = sai skill)

- Tiêu đề `# KOT Battle Log`, `## Main Battle Info`, `## Notes`, …
- Bullet `- Clan:`, `- Result:`, `- Score:`
- Bảng markdown, danh sách đánh số top attacker
- `Rank: S`, `Battle Fish: Halibut` (một cá) thay vì dòng Map đủ bộ cá
- Giải thích, nhận xét (`Perfect sweep`, `Strong point spread`, …)

### Ví dụ SAI (không được trả kiểu này)

```
# KOT Battle Log
- Clan: King Of Thieves {S} vs Angler's of the deep
- Result: WIN
...
## Top Attackers
1. HuyNgoo — 1831P
```

### Ví dụ ĐÚNG (chỉ được trả thế này)

```
19/05/2026 KOT {SSS} vs Angler's of the deep -> Win
Map: Alaska - Halibut, Humpback Salmon, Coalfish, Steelhead
Top Battle: HuyNgoo, Aizn, Lmao
```

Chỉ khi **thiếu dữ liệu bắt buộc** (không đọc được ảnh / thiếu top 3): sau 3 dòng được phép **tối đa 1 câu** hỏi ngắn. Vẫn **không** được báo cáo markdown.

---

# KOT Battle Log

Đọc **một** ảnh kết quả trận KOT + map/cá user gửi (nếu có). Áp dụng [OUTPUT](#output--đọc-trước-ưu-tiên-cao-nhất) ở trên.

## Clan

- Tag **SSS** (ảnh có thể hiện `{sSs}`, `{S}`, **King Of Thieves**).
- Log: luôn `{SSS}`. Ảnh: **trái** = SSS, **phải** = đối thủ.

## Đọc ảnh

| Field | Cách lấy |
| ----- | -------- |
| Đối thủ | Tên clan **phải**, không tag |
| Win/Lose | Điểm phe **trái** vs **phải**; trái cao hơn → `Win` |
| Top 3 | Top 3 player phe SSS trên ảnh — **chỉ tên**, đúng hoa thường |

**Ngày:** User nói trong prompt, không thì hôm nay — `DD/MM/YYYY`.

## Map / cá (JSON cuối file)

`alaska, halibut` = map **Alaska** + cá đầu **Halibut** → lấy bộ `["Halibut","Humpback Salmon","Coalfish","Steelhead"]`, **không** lấy `fish[0]` (Arctic Char…).

| User gửi | Match |
| -------- | ----- |
| Chỉ map | `fish[0]` của map đó |
| Chỉ cá đầu | `fish[i][0]` trùng → cả `fish[i]` + `map_name` |
| Map + cá đầu | Trong map, `fish[i][0]` trùng cá đầu → cả `fish[i]` |

Chưa có map/cá: dòng 2 = `Map:  - `, sau đó **1 câu** list map / hỏi cá đầu — vẫn không báo cáo.

## Prompt mẫu (URL GitHub)

User có thể chỉ dán URL skill — agent **vẫn phải** tuân OUTPUT 3 dòng:

```
https://raw.githubusercontent.com/tthanh18/kot-creature-of-the-deep-log/refs/heads/master/skill.md

[ảnh battle]
alaska, halibut
```

Tùy chọn thêm 1 dòng để chặt hơn: `Chỉ 3 dòng log KOT, không markdown.`

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
