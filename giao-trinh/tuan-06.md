# Tuần 6 — Toán 3D

Hết tuần này, có vector đủ dùng để nói chuyện với người viết thuật toán, và một hàm đẩy hộp theo một trục cho hết chồng thể tích. Revit không bị đụng tới. Tường trong model không bị dịch.

Thời lượng: 5 buổi × 90 phút. Làm trên Omarchy.

## Giáo án

### Buổi 1 — Vector

Mục tiêu: cộng, trừ, độ dài, tích vô hướng. Bốn test tính tay được.

Tạo `src/Week01.Core/Vec3.cs`:

```csharp
public readonly record struct Vec3(double X, double Y, double Z)
{
    public static Vec3 operator +(Vec3 a, Vec3 b) => new(a.X + b.X, a.Y + b.Y, a.Z + b.Z);
    public static Vec3 operator -(Vec3 a, Vec3 b) => new(a.X - b.X, a.Y - b.Y, a.Z - b.Z);
    public double Length() => Math.Sqrt(X * X + Y * Y + Z * Z);
    public double Dot(Vec3 other) => X * other.X + Y * other.Y + Z * other.Z;
}
```

Test:

- `(1,0,0) + (0,1,0)` là `(1,1,0)`
- độ dài `(3,4,0)` là 5
- `(1,0,0)` dot `(0,1,0)` là 0
- `(1,0,0)` dot `(2,0,0)` là 2

Xong khi: bốn test xanh và bạn nói được dot bằng 0 nghĩa là hai hướng vuông góc.

### Buổi 2 — Tích có hướng

Mục tiêu: biết hướng pháp tuyến của một mặt phẳng cho bởi hai cạnh. Chưa cần dùng vào hộp.

```csharp
public Vec3 Cross(Vec3 other) => new(
    Y * other.Z - Z * other.Y,
    Z * other.X - X * other.Z,
    X * other.Y - Y * other.X);
```

Test: `(1,0,0)` cross `(0,1,0)` là `(0,0,1)`. Đổi thứ tự thì ra `(0,0,-1)`.

Xong khi: bạn vẽ được hai trục X, Y trên giấy và chỉ hướng của cross lên trên.

### Buổi 3 — `Overlaps`

Mục tiêu: tách "chạm mặt" khỏi "chồng thể tích".

`Overlaps` giống `Intersects` nhưng mỗi trục dùng `<`, không dùng `<=`. Chạm mặt thì `Intersects` đúng và `Overlaps` sai.

Test lấy lại hộp A và D của tuần 2: `Intersects` true, `Overlaps` false. Hộp A và B: cả hai true.

Xong khi: hai test đó xanh và không sửa test cũ của `Intersects` cho khỏi đỏ.

### Buổi 4 — Đẩy theo một trục

Mục tiêu: hết `Overlaps`, được phép còn chạm.

```csharp
public static Box PushOut(Box moving, Box still)
```

Nếu không `Overlaps`, trả về `moving` nguyên vẹn.

Nếu có, tính độ chồng trên từng trục:

```text
depth = min(moving.Max, still.Max) - max(moving.Min, still.Min)
```

Chọn trục có `depth` nhỏ nhất. Đẩy `moving` theo trục đó, theo hướng ra xa tâm `still`. Độ đẩy bằng đúng `depth`. Sau khi đẩy, hai hộp chạm mặt: `Overlaps` false, `Intersects` true.

Ví dụ làm trong bài học, không lấy làm test duy nhất: A `[0,0,0]-[2,2,2]`, B `[1,1,1]-[3,3,3]`. Độ chồng cả ba trục đều là 1. Chọn trục X vì bằng nhau thì ưu tiên X, rồi Y, rồi Z. Tâm B nằm về phía tăng của X so với tâm A, nên đẩy B thêm 1 theo X. B thành `[2,1,1]-[4,3,3]`.

Test bắt buộc là ví dụ này, cộng một test hộp không chồng thì `PushOut` trả đúng hộp cũ.

Xong khi: bạn tính lại độ chồng bằng tay ra số 1 trước khi nhìn assert.

### Buổi 5 — Đổi tên project và ghi chú cho người đọc thuật toán

Mục tiêu: một commit đổi tên, solution vẫn build.

Đổi `Week01.Core` thành `Clash.Core`, namespace `Clash`. Cập nhật reference của App, Tests, và project WPF nếu đã có. Một commit: `Rename Week01.Core to Clash.Core`.

Viết `notes/tuan-06.md`, nửa trang: dot, cross, overlaps, và câu "hàm này chưa đẩy phần tử Revit".

Xong khi: `dotnet test` xanh sau khi đổi tên, và `rg Week01 src tests` không còn trên file `.cs` và `.csproj`.

## Bài học

Độ chồng trên một trục là bề rộng của đoạn giao. Với hai đoạn, đầu đoạn giao là max của hai đầu nhỏ, cuối đoạn giao là min của hai đầu lớn. Hiệu của chúng dương khi còn chồng thể tích.

Chọn trục nông nhất vì đó là cú đẩy ngắn nhất để tách. Bằng nhau thì ưu tiên X rồi Y rồi Z để test khỏi phụ thuộc thứ tự duyệt của một máy khác.

Hướng đẩy: so tâm, tức trung điểm mỗi hộp, trên trục đã chọn. Tâm `moving` lớn hơn tâm `still` thì cộng `depth` vào cả min và max của `moving` trên trục đó. Ngược lại thì trừ.

Cross không tham gia `PushOut`. Nó có trong tuần này để khi đọc thuật toán routing của người khác, bạn nhận ra pháp tuyến và hướng vuông góc, chứ không để đẩy hộp.

## Bài tập nộp

- `Vec3` với `Length`, `Dot`, `Cross`
- `Overlaps` và `PushOut` có test của ví dụ A, B
- commit đổi tên sang `Clash.Core`
- `notes/tuan-06.md`

Đối chiếu số ở [đáp án tuần 6](dap-an/tuan-06.md) sau khi tự tính.

## Tự chấm

| Mục | Đạt |
|---|---|
| A và D chạm mặt: Overlaps false | |
| PushOut của ví dụ ra B `[2,1,1]-[4,3,3]` | |
| Test tuần 2 vẫn xanh sau khi đổi tên | |
| Không có lệnh Revit nào gọi `PushOut` | |

Tuần này không dịch tường trong Revit. Làm vậy là một bài khác, và dễ hỏng model.
