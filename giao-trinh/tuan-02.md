# Tuần 2 — JSON và giao nhau

Hết tuần này, app đọc một file JSON, in các cặp hộp giao nhau, và bạn giải thích được hàm tìm cặp cho người chưa đọc code.

Thời lượng: 5 buổi × 90 phút.

Nguyên liệu: [exercises/tuan-02/boxes.json](../exercises/tuan-02/boxes.json). Quy ước giao nhau nằm ở [cách học](00-cach-hoc.md).

## Tài liệu đọc

Mở lúc viết `Intersects` và `BoxFile.Load`.

- [Cheat-sheet AABB](tai-lieu/aabb-cheat-sheet.md) — công thức `<=`, file JSON của repo, cặp giao và không giao.
- [PDF cheat-sheet](tai-lieu/02-aabb-cheat-sheet.pdf) — bản in. PDF ghi góc thành `min.x`. File mẫu trong repo ghi `min` là mảng `[x, y, z]`.
- [System.Text.Json](https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json/overview) khi viết loader (buổi 2).

## Giáo án

### Buổi 1 — `Intersects`

Mục tiêu: một method, năm test, chưa có file.

`Intersects` dùng so sánh đóng: chạm mặt là giao. Kiểm tra cả ba trục. Hộp không hợp lệ trả về `false`.

Test bắt buộc, tọa độ lấy từ tên test cho khỏi nhầm:

- A `[0,0,0]-[2,2,2]` giao B `[1,1,1]-[3,3,3]`
- A không giao C `[10,10,10]-[11,11,11]`
- A giao D `[2,0,0]-[3,0.5,0.5]` vì chạm mặt `x = 2`. D mỏng và nằm ở góc mặt đó để không đụng B
- một hộp không giao chính nó theo nghĩa cặp. `Intersects` của một hộp với chính nó vẫn `true` vì hình trùng nhau. Việc bỏ cặp với chính mình nằm ở hàm tìm cặp, buổi 3
- hộp có `MaxX < MinX` không giao hộp nào

Xong khi: năm test xanh và bạn chỉ vào đúng một chỗ trong code nơi toán tử là `<=`.

### Buổi 2 — Đọc JSON

Mục tiêu: từ file mẫu ra đúng bốn hộp.

Thêm package `System.Text.Json` nếu project chưa thấy namespace. SDK 10 đã có sẵn trong shared framework, không cần package nếu chỉ dùng `JsonSerializer`.

Dạng file:

```json
{ "id": "A", "min": [0, 0, 0], "max": [2, 2, 2] }
```

Viết `BoxFile.Load(string path)` trả về `List<Box>`. Phần tử thiếu id, thiếu mảng, hoặc mảng không đủ ba số thì bỏ qua, không ném. File không tồn tại thì ném `FileNotFoundException` vì đó là lỗi của người gọi, khác với một dòng dữ liệu bẩn.

In từ app số hộp đọc được. Với file mẫu, số đó là 4.

Xong khi: test load file mẫu đếm được 4, và một file JSON có một phần tử thiếu `max` thì phần tử đó biến mất, phần tử khác còn.

### Buổi 3 — Cặp giao

Mục tiêu: danh sách cặp ổn định, test được.

```csharp
public readonly record struct ClashPair(string FirstId, string SecondId);

public static List<ClashPair> FindPairs(IReadOnlyList<Box> boxes)
```

Đặt trong `BoxQueries`. Quy tắc:

- chỉ thêm cặp khi `a.Intersects(b)` và hai id khác nhau
- `FirstId` là id đứng trước theo `string.CompareOrdinal`
- không thêm cả `(A,B)` lẫn `(B,A)`
- thứ tự các cặp trong list là thứ tự lần đầu gặp cặp đó khi duyệt từ trái sang

Với file mẫu, cặp phải có là `A-B` và `A-D`. `C` không có trong cặp nào. `A-D` có mặt vì chạm mặt.

Xong khi: test file mẫu khẳng định đúng hai cặp đó, đúng thứ tự id trong từng cặp.

### Buổi 4 — App và một hàm dài

Mục tiêu: `Program.cs` dưới 30 dòng. Phần việc nằm trong Core.

App nhận đường dẫn file. Không truyền đối số thì dùng `exercises/tuan-02/boxes.json`. In mỗi cặp một dòng `A B`. Không có cặp thì in `khong giao`.

Sau đó gom `FindPairs` và phần chuẩn hóa id vào một chỗ dễ đọc. Nếu hàm vượt 40 dòng, tách phần "thứ tự id" ra method `OrderedPair`.

Đọc chương 3 và 4 của *The Art of Readable Code*: bố cục và comment. Comment chỉ viết cho quy ước chạm mặt, vì nhìn `<=` không biết đó là chủ ý.

Xong khi: bạn giải thích `FindPairs` trong năm phút. Người nghe nói lại được vì sao `A-D` có trong kết quả.

### Buổi 5 — Review một đoạn cố tình viết xấu

Mục tiêu: chỉ ra lỗi bằng lời, trước khi sửa.

Đoạn dưới cố tình sai so với quy ước của khóa. Không chạy. Đọc và ghi ba nhận xét.

```csharp
public static List<(string, string)> Find(List<Box> boxes)
{
    var pairs = new List<(string, string)>();
    for (var i = 0; i < boxes.Count; i++)
    {
        for (var j = 0; j < boxes.Count; j++)
        {
            if (boxes[i].MaxX > boxes[j].MinX)
                pairs.Add((boxes[i].Id, boxes[j].Id));
        }
    }
    return pairs;
}
```

Mỗi nhận xét gồm: chỗ sai, vì sao sai với quy ước khóa, sửa theo hướng nào. Viết vào `notes/tuan-02-review.md`. Đối chiếu [đáp án](dap-an/tuan-02.md) sau khi đã viết xong ba nhận xét.

Xong khi: file review có ít nhất ba ý, và ý nào cũng chỉ vào hành vi chứ không chỉ vào kiểu đặt tên.

## Bài học

Giao trên một trục là hai đoạn thẳng chồng lên nhau, kể cả chạm đầu mút. Hết giao trên một trục thì hết giao trong không gian. Vì vậy `Intersects` là phép AND của ba trục, không phải cộng khoảng cách.

```csharp
private static bool RangesMeet(double aMin, double aMax, double bMin, double bMax)
    => aMin <= bMax && bMin <= aMax;
```

Chạm mặt `x = 2` làm hai đoạn `[0,2]` và `[2,4]` thỏa `<=`. Nếu viết `<`, cặp `A-D` biến mất và test buổi 1 đỏ.

`JsonSerializer` cần một kiểu trung gian vì `Box` đang là sáu số phẳng, còn file là hai mảng. Map sang `Box` ở một method, đừng để thuộc tính JSON lộ ra ngoài Core như API chính.

## Bài tập nộp

- `Intersects`, `BoxFile.Load`, `FindPairs`
- test file mẫu ra đúng `A-B` và `A-D`
- app in cặp
- `notes/tuan-02-review.md`
- comment một dòng tại chỗ `<=`, nói chạm mặt là giao

## Tự chấm

| Mục | Đạt |
|---|---|
| `A-D` có trong kết quả | |
| `C` không có trong cặp nào | |
| Không có cặp một hộp với chính nó | |
| App dưới 30 dòng | |
| Review chỉ ra được lỗi chỉ so một trục | |

Chưa mở WPF. Chưa cài Revit.
