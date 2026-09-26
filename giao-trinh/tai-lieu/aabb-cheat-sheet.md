# Cheat-sheet AABB — tuần 2

Đọc kèm [giáo án tuần 2](../tuan-02.md). In hoặc để cạnh lúc viết `Intersects` và `BoxFile.Load`.

PDF gốc: [02-aabb-cheat-sheet.pdf](02-aabb-cheat-sheet.pdf). Header PDF ghi “Week 1”. Trong repo này AABB là tuần 2. Quy ước hình học cả khóa nằm ở [cách học](../00-cach-hoc.md).

## AABB là gì

**AABB** (axis-aligned bounding box) là hộp chữ nhật, cạnh song song các trục. Trong repo, kiểu đó tên `Box`: hai góc đối nhau `min` và `max`.

Hộp hợp lệ khi `Max >= Min` trên cả ba trục. `Intersects` của hộp có `Max < Min` trên một trục trả về `false`.

Broad-phase: loại nhanh các cặp hộp không giao, trước khi so hình chi tiết. Tuần này chỉ làm broad-phase trên vài hộp. Chưa có index không gian.

## Công thức

Giao trên một trục khi hai đoạn chồng, kể cả chạm đầu mút:

```csharp
private static bool RangesMeet(double aMin, double aMax, double bMin, double bMax)
    => aMin <= bMax && bMin <= aMax;
```

`Intersects` là AND của ba trục. Hết giao trên một trục thì hết giao trong không gian.

`<=` là cố ý. Chạm mặt vẫn là giao. Đổi thành `<` thì cặp `A-D` trong file mẫu biến mất: mặt `x = 2` của A không còn tính là chạm D.

Cách viết trong PDF gốc, tách trục bằng `<`, là cùng một phép so:

```text
!(aMax < bMin || bMax < aMin)
```

tương đương `aMin <= bMax && bMin <= aMax`. Khi code trong repo, chỗ giáo án bảo chỉ vào là toán tử `<=`.

`Overlaps` là quy ước khác: chồng thể tích, so bằng `<`. Chưa viết ở tuần 2. Tuần 2 chạm mặt vẫn là giao. So sánh đúng số trên fixture, chưa cộng dung sai. Dung sai là tuần 6.

Một hộp `Intersects` chính nó thì `true`, vì hình trùng nhau. Cặp với chính mình bị bỏ ở `FindPairs`, không phải ở `Intersects`.

## File JSON của repo

[exercises/tuan-02/boxes.json](../../exercises/tuan-02/boxes.json) là một mảng. Mỗi phần tử có `id`, `min`, `max`. `min` và `max` là mảng ba số `[x, y, z]`:

```json
[
  { "id": "A", "min": [0, 0, 0], "max": [2, 2, 2] },
  { "id": "B", "min": [1, 1, 1], "max": [3, 3, 3] },
  { "id": "C", "min": [10, 10, 10], "max": [11, 11, 11] },
  { "id": "D", "min": [2, 0, 0], "max": [3, 0.5, 0.5] }
]
```

PDF gốc bọc danh sách trong `{ "boxes": [ ... ] }` và ghi góc thành object `{ "x", "y", "z" }`. Khi làm bài, theo file mẫu trên. `Box` trong Core vẫn là sáu số phẳng. `JsonSerializer` cần một kiểu trung gian cho hai mảng, rồi map sang `Box` ở một method.

`BoxFile.Load(string path)` trả `List<Box>`.

| Tình huống | Việc làm |
|---|---|
| Thiếu `id`, thiếu mảng, hoặc mảng không đủ ba số | Bỏ phần tử đó, không ném |
| File không tồn tại | Ném `FileNotFoundException` |
| File mẫu | Load ra 4 hộp |

SDK 10 đã có `System.Text.Json` trong shared framework. Thêm package chỉ khi project chưa thấy namespace. Đọc [tổng quan System.Text.Json](https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json/overview) lúc viết loader.

## File mẫu: giao và không giao

| Cặp | Kết quả | Lý do |
|---|---|---|
| A `[0,0,0]-[2,2,2]` và B `[1,1,1]-[3,3,3]` | Giao | Chồng cả ba trục |
| A và C `[10,10,10]-[11,11,11]` | Không | Tách trên X (và cả Y, Z). Một trục tách là đủ |
| A và D `[2,0,0]-[3,0.5,0.5]` | Giao | Chạm mặt `x = 2` |
| B và D | Không | D chỉ tới `y = 0.5`, `z = 0.5`. B bắt đầu ở `y = 1`, `z = 1` |
| C với hộp nào trong file | Không | C đứng riêng |

`FindPairs` trên file mẫu ra đúng hai cặp, theo thứ tự gặp khi duyệt từ trái sang: `A-B`, rồi `A-D`. Trong mỗi cặp, `FirstId` là id đứng trước theo `string.CompareOrdinal`. Không thêm cả `(A,B)` lẫn `(B,A)`. Không thêm cặp hai id giống nhau.

App in mỗi cặp một dòng `A B`. Không có cặp thì in `khong giao`. Không truyền đối số thì dùng file mẫu.

## Case ngoài file mẫu

Tự kiểm công thức trước khi đổ JSON. Không có trong `boxes.json`.

| # | A min→max | B min→max | Kỳ vọng | Lý do |
|---|---|---|---|---|
| 1 | `(0,0,0)→(1,1,1)` | `(2,0,0)→(3,1,1)` | Không | Hở trên X (`1 < 2`) |
| 2 | `(0,0,0)→(1,1,1)` | `(1,0,0)→(2,1,1)` | Giao | Chạm mặt X |
| 3 | `(0,0,0)→(5,1,1)` | `(1,2,0)→(2,3,1)` | Không | Tách Y |
| 4 | `(0,0,0)→(1,1,1)` | `(0,0,2)→(1,1,3)` | Không | Tách Z |
| 5 | `(0,0,0)→(1,1,1)` | `(0,0,0)→(1,1,1)` | Giao | Trùng hình. `Intersects` là `true` |
| 6 | `(0,0,0)→(1,1,1)` | `(1,1,1)→(2,2,2)` | Giao | Chạm góc |
| 7 | Hộp có `MaxX < MinX` | Hộp bất kỳ | Không | Hộp không hợp lệ |

Vẽ nhanh mặt phẳng, bỏ trục Z, khi một case đỏ. Lỗi hay gặp: một phía của trục (`MaxA` với `MinB`) mà quên phía kia, hoặc chỉ so trục X.

## Cặp

```csharp
public readonly record struct ClashPair(string FirstId, string SecondId);

public static List<ClashPair> FindPairs(IReadOnlyList<Box> boxes)
```

Đặt trong `BoxQueries`. Ba quy tắc ở giáo án buổi 3 là đủ để viết hàm. Lời giải tham khảo ở [đáp án tuần 2](../dap-an/tuan-02.md), mở sau khi đã có bản chạy hoặc sau khi đã viết ba nhận xét buổi 5.

Comment một dòng tại chỗ `<=`: chạm mặt là giao. Nhìn toán tử không biết đó là chủ ý.

## Từ Nhật

Từ của tuần 2 (干渉, 判定, 仕様, 例外, 単体テスト) ở [phụ lục](../phu-luc-thuat-ngu.md). 干渉 đọc *kanshō*: va chạm, giao nhau. 配管, 配筋, ファミリ, スナップショット nằm ở phần phụ của phụ lục, khi cần nói về ống, cốt thép, family, và bản chụp trạng thái model.
