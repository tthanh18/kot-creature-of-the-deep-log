---
name: kot-battle-log
description: >-
  Phân tích 1 ảnh kết quả King of Thieves (KOT) clan battle và xuất log theo
  template cố định; map/cá lấy từ mapping-battle.json (hỏi user nếu thiếu).
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

**Map:** Không có trên ảnh và user **không** nói map/cá trong prompt → xem mục [Map từ mapping-battle.json](#map-từ-mapping-battlejson) bên dưới.

**Top Battle:** Chỉ top 3 player phe SSS (nếu có trên ảnh); giữ đúng chữ hoa/thường tên trong game.

## Template (bắt buộc)

```
[DD/MM/YYYY] KOT {[clan_name]} vs [another_clan_name] -> [Win, Lose]
Map: [map_name] - [fish_list hoặc để trống]
Top Battle: [top1], [top2], [top3]
```

- `[another_clan_name]`: tên clan đối thủ **bên phải**, không kèm tag, vd. `Moczykije z Polski`.
- Mỗi lần chỉ xuất **một** log hoàn chỉnh, copy-paste được.
- Ba dòng log cách nhau bằng xuống dòng thật (không dùng ký tự `\n`).

## Map từ mapping-battle.json

File: `mapping-battle.json` (root repo). Mỗi entry: `{ map_name, fish: [[cá1, cá2, ...], ...] }`.

**Khi user chưa cho map/cá:**

1. Đọc `mapping-battle.json`.
2. Xuất log từ ảnh với dòng Map để trống phần fish: `Map:  - ` (hoặc tạm `Map: (chưa có) - `).
3. Liệt kê **tất cả** `map_name` trong file, đánh số nếu nhiều.
4. Hỏi user: _Map nào? Hoặc gửi **tên cá đầu tiên** của trận — đủ để match bộ cá trong file._

**Khi user trả lời (map hoặc cá đầu):**

| User gửi                                        | Cách match                                                                                                   |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Tên map (vd. `Amazon`, `Great Lake`)            | Khớp `map_name` (không phân biệt hoa thường). Lấy **bộ cá đầu tiên** trong `fish[]` của map đó.              |
| Tên cá đầu (vd. `Amazon Puffer`, `Coho Salmon`) | Tìm entry có phần tử `fish[i][0]` trùng (không phân biệt hoa thường). Lấy cả `map_name` + cả mảng `fish[i]`. |
| Map + cá đầu                                    | Ưu tiên khớp cả hai; không khớp thì báo và list lại map.                                                     |

**Điền dòng Map:** `Map: [map_name] - [cá1], [cá2], [cá3], ...` — join toàn bộ mảng cá đã match, phân tách bằng `, `.

**Khi user đã nói map/cá ngay từ đầu:** match file luôn, xuất log đủ 3 dòng một lần, không hỏi thêm.

## Ví dụ

**Input:** 1 ảnh — trái King Of Thieves {sSs} **11349**, phải Moczykije z Polski **11002**; top SSS trên ảnh: Con mẹ mày, Hung, Meo meo. User: "hôm nay".

**Output:**

```
17/05/2026 KOT {SSS} vs Moczykije z Polski -> Win
Map: Amazon - Amazon Puffer, Rock-Bacu, Cachama, Corvina
Top Battle: Con mẹ mày, Hung, Meo meo
```

**Input:** Cùng ảnh, user chưa nói map. File có `Great Lake`.

**Output (lần 1):**

```
18/05/2026 KOT {SSS} vs Moczykije z Polski -> Win
Map:  -
Top Battle: Con mẹ mày, Hung, Meo meo
```

Map có trong file: **Great Lake**

Bạn chơi map nào? Hoặc gửi tên **cá đầu tiên** của trận.

**Input:** User: `Coho Salmon`

**Output (lần 2 — log hoàn chỉnh):**

```
18/05/2026 KOT {SSS} vs Moczykije z Polski -> Win
Map: Great Lake - Coho Salmon, Brook Trout, Channel Catfish, Largermouth Bass
Top Battle: Con mẹ mày, Hung, Meo meo
```

**Input:** 1 ảnh — trái SSS **9800**, phải ClanXYZ **10200** (không có top trên ảnh).

**Output:**

```
18/05/2026 KOT {SSS} vs ClanXYZ -> Lose
Map:  -
Top Battle: (cần top 3 player SSS)
```

## Thiếu dữ liệu

- Thiếu top player trên ảnh → hỏi user hoặc ghi `Top Battle: (cần top 3 player SSS)`.
- Thiếu tên đối thủ hoặc điểm một phe → nêu rõ trước khi đoán Win/Lose.
- Không bịa tên player hay điểm không đọc được trên ảnh.
- Cá/map user gửi không khớp `mapping-battle.json` → báo không tìm thấy, list lại `map_name` có trong file; không đoán fish list.

mapping-battle.json
[ { "map_name": "Paradise", "fish": [ [ "Bluefish", "Longtail Tune", "Largetooth Flounder", "Spot-Fin Porcupinefish" ], ["White-Tuna", "Green Humphead Parrotfish", "Clownfish", "Blue Trevally"], ["Bonefish", "Blue Trevally", "Pelagic Stingray", "Snubnose Pompano"] ] }, { "map_name": "Great Lake", "fish": [ ["Coho Salmon", "Brook Trout", "Channel Catfish", "Largermouth Bass"], ["White bass", "Yellow Perch", "Sea lamprey", "Chinook Salmon"], ["Lake Trout", "Brook Trout", "Pink Salmon", "Lake Sturgeon"] ] }, { "map_name": "Costa Rica", "fish": [ ["Roosterfish", "Dorado", "Tarpon", "Yellowfin Tuna"], ["Blue Marlin", "Snook", "Barracuda", "Pompano"], [ "Pacific Sailfish", "Broomtail Grouper", "Jack Crevalle", "Striped Marlin" ] ] }, { "map_name": "Alaska", "fish": [ ["Arctic Char", "Dolly Varden", "Spiny Skate", "Rougheye Rockfish"], ["Halibut", "Humpback Salmon", "Coalfish", "Steelhead"], ["King Salmon", "Blue Lingcod", "Chum Salmon", "Lancetfish"] ] }, { "map_name": "Australia", "fish": [ [ "albacore", "golden trevally", "queensland grouper", "black-saddler coral grouper" ], ["barramundi", "tailor", "coral trout", "giant trevally"], ["skipjack tuna", "john dory", "carpet shark", "swordfish"] ] }, { "map_name": "Scotland", "fish": [ ["Rainbow Trout", "european whitefish", "carp", "freshwater bream"], ["tench", "european perch", "european eel", "sea trout"], [""] ] }, { "map_name": "Thailand", "fish": [ ["spotted sorubim", "empurau", "bambusa", "great snakehead"], ["black ear catfish", "bighead carp", "malayan leaffish", "wallago"], [] ] }, { "map_name": "Amazon", "fish": [ ["Amazon Puffer", "Rock-Bacu", "Cachama", "Corvina"], ["Red Piranha", "freshwater barracuda", "Giant Trahira", "Zungaro"] ] } ]
