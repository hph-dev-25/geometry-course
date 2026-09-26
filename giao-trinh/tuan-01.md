# Tuần 1 — C# và kiểu Box

Hết tuần này, trong `Week01.Core` có một `record` hộp, tính được thể tích, đọc được một dòng chữ, và lọc được danh sách bằng LINQ. Mọi thứ đó có test. App chỉ để in thử.

Thời lượng: 5 buổi × 90 phút.

Nguyên liệu: [exercises/tuan-01/boxes.txt](../exercises/tuan-01/boxes.txt). Năm từ Nhật của tuần nằm trong [phụ lục](phu-luc-thuat-ngu.md).

## Giáo án

### Buổi 1 — Vòng sửa, chạy, test

Mục tiêu: mở solution, chạy app, chạy test, thêm được một kiểu mới.

Làm:

1. Mở `Geometry.slnx`. Chạy `src/Week01.App/Program.cs`. Cửa sổ Run in `Hello, World!`.
2. Chạy test cạnh `[Fact]` trong `UnitTest1.cs`. Test đang pass.
3. Tạo `src/Week01.Core/Box.cs` với `record` ở phần bài học.
4. Đổi test: tạo hai hộp, assert `Id` của hộp thứ nhất là `"A"`.
5. Đổi `Program.cs` để tạo một hộp và in `Id`.

Xong khi: app in đúng id, test xanh, Rider không còn cảnh báo trên file vừa viết.

### Buổi 2 — Method và số

Mục tiêu: thêm hành vi cho `Box`, test cả trường hợp biên.

Đọc trong [Tour of C#](https://learn.microsoft.com/dotnet/csharp/tour-of-csharp/) các phần kiểu, method, biểu thức. Dừng khi sang chủ đề khác.

Làm: viết `Volume()`. Thể tích là tích ba cạnh. Hộp `0,0,0` đến `2,3,4` có thể tích 24. Hộp có một cạnh bằng 0 có thể tích 0. Hộp không hợp lệ, max nhỏ hơn min trên một trục, không được trả về số âm. Trả về 0 và để tuần sau quyết định có ném lỗi hay không. Tuần này chỉ cần không trả về âm.

Test bắt buộc:

- `Volume_of_a_2_by_3_by_4_box_is_24`
- `Volume_is_0_when_one_edge_is_0`
- `Volume_is_0_when_a_max_is_below_its_min`

Xong khi: ba test xanh và bạn nói được công thức mà không nhìn code.

### Buổi 3 — Null

Mục tiêu: phân biệt "không có giá trị" với "ném lỗi".

`Box.Parse(string line)` trả về `Box?`.

Dòng hợp lệ có đúng 7 mẩu, cách nhau bởi khoảng trắng: id và sáu số. Số dùng dấu chấm, kiểu `double`. Dòng sai, thiếu mẩu, thừa mẩu, hoặc có chữ ở chỗ số thì trả về `null`. Không ném exception với những dòng đó.

Đọc [nullable reference types](https://learn.microsoft.com/dotnet/csharp/nullable-references). Bật cảnh báo của Rider. Chỗ nào gọi `Parse` mà dùng kết quả ngay, Rider sẽ báo. Sửa bằng `is Box box` hoặc kiểm tra null, không tắt cảnh báo.

Test: dòng `"A 0 0 0 1 1 1"` ra hộp A. Dòng `"not-a-box"` ra null. Dòng rỗng ra null.

Xong khi: không còn cảnh báo nullable trên các file của tuần.

### Buổi 4 — LINQ

Mục tiêu: lọc một danh sách mà không viết vòng `for` tay.

Đọc `Where`, `Select`, `ToList` trong [LINQ](https://learn.microsoft.com/dotnet/csharp/linq/).

Viết method tĩnh:

```csharp
public static List<Box> LargerThan(IEnumerable<Box> boxes, double minVolume)
```

Đặt trong class `BoxQueries` cùng namespace `Week01.Core`. Method trả về các hộp có `Volume()` lớn hơn `minVolume`, giữ nguyên thứ tự đầu vào.

Test với ba hộp thể tích 1, 8 và 0. Ngưỡng 1 thì còn đúng hộp thể tích 8.

Xong khi: test của `LargerThan` xanh và trong method không có `for` hay `foreach`.

### Buổi 5 — Đọc được code của mình

Mục tiêu: người khác đọc tên method là hiểu việc nó làm.

Đọc hai chương đầu của *The Art of Readable Code*: code dễ hiểu, và đặt tên. Chưa có sách thì làm phần việc dưới, mua hoặc mượn sách trước tuần 2.

Làm:

1. Đọc lại `Parse` và `Volume`. Đổi tên biến một chữ thành tên chỉ việc. `line` được giữ vì nó đúng là một dòng.
2. Đọc `Parse` thành tiếng, từ đầu đến cuối, không thêm lời giải thích ngoài những gì tên và cấu trúc đang nói.
3. Commit từng buổi còn thiếu. Một commit một ý.

Xong khi: bạn đọc `Parse` trong ba phút cho một người chưa thấy file, và người đó nói lại được dòng nào bị bỏ.

## Bài học

`record` là kiểu dữ liệu bất biến, so sánh theo giá trị. Hộp là dữ liệu, không phải đồ vật cần đổi từng cạnh sau khi tạo, nên dùng `record` thay vì class có setter.

```csharp
namespace Week01.Core;

public sealed record Box(
    string Id,
    double MinX, double MinY, double MinZ,
    double MaxX, double MaxY, double MaxZ);
```

`sealed` để tuần này chưa phải nghĩ về kế thừa. Sáu tọa độ là tham số của record, Rider sinh constructor và thuộc tính.

`Box?` nghĩa là kết quả có thể không có hộp. `Parse` nuốt dòng rác bằng `null` vì file tuần 1 cố ý có dòng hỏng. Ném exception sẽ làm cả lần đọc dừng ở dòng thứ ba của [boxes.txt](../exercises/tuan-01/boxes.txt).

LINQ `Where` giữ phần tử làm điều kiện đúng. `ToList` chốt kết quả thành list. Chưa gọi `ToList` thì câu truy vấn chưa chạy.

## Bài tập nộp

Trong repo, cuối tuần có:

- `Box` với `Volume` và `Parse`
- `BoxQueries.LargerThan`
- test của ba method
- `Program.cs` in các hộp đọc được từ `exercises/tuan-01/boxes.txt`, bỏ dòng `Parse` trả về null
- ít nhất bốn commit

Đối chiếu [đáp án tuần 1](dap-an/tuan-01.md) sau khi test xanh.

## Tự chấm

| Mục | Đạt |
|---|---|
| `dotnet test` xanh | |
| Không cảnh báo nullable trên file mới | |
| `Parse` không ném với dòng rác | |
| `LargerThan` không dùng vòng lặp | |
| Đọc được `Parse` thành tiếng | |

Tuần 1 chưa đọc JSON, chưa viết `Intersects`, chưa mở Windows.
