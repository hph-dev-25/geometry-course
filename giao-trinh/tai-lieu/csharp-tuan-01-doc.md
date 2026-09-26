# C# tuần 1 — cho người đã làm web

Đọc kèm [giáo án tuần 1](../tuan-01.md). Hết tuần, trong `Week01.Core` có `record Box`, `Volume`, `Parse` trả về `Box?`, và `BoxQueries.LargerThan`. App in thử. Test xanh.

Bản PDF gốc: [01-week1-reading-pack.pdf](01-week1-reading-pack.pdf). SDK, Rider và đường dẫn máy nằm ở [môi trường](../01-moi-truong.md). Năm từ Nhật của tuần nằm ở [phụ lục](../phu-luc-thuat-ngu.md).

## Khi PDF và giáo án lệch

PDF viết cho một lab tự tạo. Repo này đã có solution và đã chia tuần.

| PDF gốc | Làm theo repo |
|---|---|
| Tuần 1 nộp app đọc JSON, hàm `Intersects` | Tuần 1 dừng ở `Box`, `Volume`, `Parse`, `LargerThan`. JSON và giao hộp là [tuần 2](../tuan-02.md), kèm [cheat-sheet AABB](aabb-cheat-sheet.md) |
| `dotnet new` solution `AabbLab`, `net8.0` | Mở `Geometry.slnx`. Project tuần 1 là `net10.0` |
| Model `Aabb` và file `boxes.json` | Kiểu tên `Box`. Dữ liệu tuần 1 là [boxes.txt](../../exercises/tuan-01/boxes.txt), mỗi hộp một dòng chữ |
| Checklist “parse JSON, in cặp giao” | Checklist ở cuối file này, cùng tiêu chí với giáo án |

Chương AABB, schema JSON và bài console giao hộp trong PDF đọc ở tuần 2.

## Đọc theo buổi

| Buổi | Trước khi gõ | Trong giáo án |
|---|---|---|
| 1 | `class` / `struct` / `record`, rồi “Solution này và Rider” | Mở solution, chạy app, thêm `Box`, test `Id` |
| 2 | Bảng kiểu, rồi “Method, `static`” | `Volume()` |
| 3 | “`null` khác JS chỗ nào” | `Box.Parse` trả `Box?` |
| 4 | “Collection và LINQ” | `LargerThan` |
| 5 | “Code đọc được” | Đặt tên lại, đọc `Parse` thành tiếng |

Mỗi buổi khoảng 90 phút. Kẹt quá 40 phút thì ghi câu hỏi vào `notes/tuan-01.md`, như [cách học](../00-cach-hoc.md).

## Bản đồ từ web sang C#

Bạn đã quen JS/TS hoặc Python/Java. Bảng dưới để đối chiếu. Tên trong BCL nhớ dần khi gặp trong code.

| Ý đã có | C# tuần này | Ghi chú |
|---|---|---|
| `number` | `int`, `double` | Sáu tọa độ của `Box` là `double` |
| `string` | `string` | So sánh nội dung bằng `==` |
| `boolean` | `bool` | `true` / `false`, chữ thường |
| `null` / `undefined` | `null`, và `T?` | `Box?` là kết quả có thể không có hộp |
| object | `class`, `record` | Hộp tuần này là `record` |
| `T[]` / array | `T[]`, `List<T>` | `LargerThan` trả `List<Box>` |
| `Map` | `Dictionary<TKey,TValue>` | Biết tên. Tuần 1 chưa bắt buộc dùng |
| `Promise<T>` | `Task<T>` | Để sau. Tuần 1 đọc file đồng bộ |
| `any` | `object`, `dynamic` | Tránh `dynamic` |

```csharp
int count = 3;
double edge = 2.5;
bool keep = true;
string id = "A";
string? maybeName = null;
int? optionalIndex = 42;
```

### `null` khác JS chỗ nào

C# tách **value type** (`int`, `bool`, `struct`) và **reference type** (`class`, `string`, `record` dạng class). Project trong repo bật nullable (`<Nullable>enable</Nullable>`). Compiler cảnh báo khi dùng một giá trị có thể là `null`.

`Box?` nghĩa là biến đó có thể không giữ hộp. `int?` là `Nullable<int>`, có `HasValue` và `Value`.

```csharp
string label = maybeName ?? "(không tên)";
int len = maybeName?.Length ?? 0;

Box? parsed = Box.Parse(line);
if (parsed is Box box)
{
    Console.WriteLine(box.Id);
}
```

`Parse` trả `null` với dòng rác, dòng thiếu mẩu, dòng thừa mẩu, dòng có chữ ở chỗ số, và dòng rỗng. Dòng hợp lệ có đúng bảy mẩu cách nhau bởi khoảng trắng: id và sáu số, dấu thập phân là dấu chấm. Nuốt dòng hỏng bằng `null` vì [boxes.txt](../../exercises/tuan-01/boxes.txt) cố ý có dòng không phải hộp. Ném exception thì lần đọc dừng giữa file.

Trong JS, `undefined` đôi khi bị bỏ qua. Trong C#, gọi thành viên trên `null` thì `NullReferenceException`. Rider sẽ gạch chỗ gọi `Parse` rồi dùng kết quả ngay. Sửa bằng `is Box box` hoặc một lần kiểm tra null. Giữ cảnh báo nullable bật.

`double.TryParse` trả `false` khi chuỗi không phải số, nên chỗ đổi chữ thành số không cần ném exception. Cách ghép các mẩu thành `Box` là bài của buổi 3. Viết trong `src/`, đối chiếu đáp án sau khi test xanh.

### `class`, `struct`, `record`

| | `class` / `record` (mặc định) | `struct` |
|---|---|---|
| Lưu | Tham chiếu | Giá trị, gán là copy field |
| `null` | Được, nếu kiểu có `?` | Không, trừ `T?` |
| So sánh mặc định | `class`: cùng tham chiếu. `record`: theo giá trị | Theo từng field |

Tuần này hộp là dữ liệu. Tạo xong không sửa từng cạnh. Giáo án dùng `record`, có `sealed` để chưa nghĩ về kế thừa:

```csharp
namespace Week01.Core;

public sealed record Box(
    string Id,
    double MinX, double MinY, double MinZ,
    double MaxX, double MaxY, double MaxZ);
```

Sáu tọa độ là tham số của record. Rider sinh constructor và thuộc tính. `Box` tuần 1 giữ `record` như giáo án. `struct` dùng khi đọc code người khác.

`record` so sánh theo giá trị, nên hai hộp cùng id và cùng sáu số là bằng nhau trong assert. `with` để copy rồi sửa một field. Tuần này chưa cần `with`.

### Thuộc tính

Tham số của `record` ở trên trở thành thuộc tính get. API công khai của kiểu nên là thuộc tính. Field `private` dùng khi có trạng thái nội bộ cần giấu. `Box` tuần này chưa cần field riêng.

### Method, `static`, file `Program.cs`

`Volume()` là method của một hộp: gọi trên instance, vì thể tích tính từ sáu số của hộp đó.

`Parse` là method tĩnh: chưa có hộp, chỉ có một dòng chữ. `LargerThan` cũng tĩnh, đặt trong class `BoxQueries`, cùng namespace `Week01.Core`. Chữ ký giáo án đã chốt:

```csharp
public static List<Box> LargerThan(IEnumerable<Box> boxes, double minVolume)
```

Method trả các hộp có `Volume()` lớn hơn `minVolume`, giữ thứ tự đầu vào. Bên trong không có `for` hay `foreach`.

`Program.cs` của app đang là top-level statements, giống file script: câu lệnh nằm thẳng trong file, không cần tự viết `class Program`. Giữ app mỏng. Công thức và đọc dòng nằm trong Core.

### Collection và LINQ

| Kiểu | Gần với | Tuần này |
|---|---|---|
| `IEnumerable<T>` | thứ foreach được | Tham số `LargerThan`, để người gọi đưa list hoặc mảng |
| `List<T>` | array có `Add` | Kết quả sau `ToList()` |
| `Dictionary<K,V>` | `Map` | Chưa bắt buộc |
| `IReadOnlyList<T>` | list không mời caller sửa | Gặp lại ở tuần 2 |

`Where` giữ phần tử khi điều kiện đúng. `Select` đổi mỗi phần tử thành giá trị khác. `ToList` chạy câu truy vấn và chốt thành list. Chưa gọi `ToList` (hoặc một lệnh duyệt) thì câu LINQ chưa chạy.

Đọc `Where`, `Select`, `ToList` trong [LINQ](https://learn.microsoft.com/dotnet/csharp/linq/). Dừng ở ba method đó.

Ví dụ lọc, chưa phải lời giải `LargerThan`:

```csharp
List<string> ids = boxes.Where(box => box.Id.Length > 0)
    .Select(box => box.Id)
    .ToList();
```

Buổi 4 thay điều kiện bằng so thể tích với ngưỡng.

### Namespace và `using`

```csharp
namespace Week01.Core;

public static class BoxQueries { }
```

`namespace Week01.Core;` là file-scoped: cả file nằm trong namespace đó, hết một cấp indent. `using Ten.Namespace;` kéo mọi kiểu public của namespace, khác `from x import y` của Python.

`<ImplicitUsings>enable</ImplicitUsings>` đã kéo `System`, `System.Collections.Generic`, `System.Linq`. `Where` và `List<T>` dùng được mà không viết `using` thêm.

### `async`

`File.ReadAllText` là đủ khi đọc một file nhỏ trên đĩa. `await File.ReadAllTextAsync` tồn tại. Tuần 1 chưa có I/O song song và chưa có UI, nên giữ lời gọi đồng bộ.

### Chỗ web dev hay lệch

| JS/TS | C# |
|---|---|
| `===` | `==` với `string` và số so nội dung. `ReferenceEquals` khi cố ý so cùng object |
| duck typing | kiểu kiểm tra lúc compile. `List<Box>` chỉ chứa `Box` |
| `JSON.parse` | tuần 1 chưa đọc JSON. Tuần 2 dùng `System.Text.Json` |
| `for..of` | `foreach`. Buổi 4 thì dùng LINQ thay vòng lặp |
| `?.` | có `?.` và `??` |
| tham số mặc định | có. Thêm overload khi một method làm hai việc |

## Solution này và Rider

```
Geometry.slnx
src/Week01.App/Program.cs     in ra console
src/Week01.Core/              Box, Volume, Parse, BoxQueries
tests/Week01.Tests/           xUnit
exercises/tuan-01/boxes.txt   nguyên liệu tuần 1
```

`Week01.App` tham chiếu `Week01.Core`. Test tham chiếu Core. Công thức nằm trong Core.

Lệnh, từ thư mục repo, như README gốc:

```bash
dotnet run --project src/Week01.App
dotnet test
dotnet build
```

| Lệnh | Việc |
|---|---|
| `dotnet build` | Compile |
| `dotnet run --project src/Week01.App` | Build rồi chạy app. Hiện in `Hello, World!` |
| `dotnet test` | Chạy xUnit. Test mẫu đang pass |
| `dotnet --version` | Đối chiếu với [môi trường](../01-moi-truong.md) |

Truyền đối số cho app: `dotnet run --project src/Week01.App -- duong/dan/file`. Mọi thứ sau `--` vào `args`. Tuần 1 giáo án chưa bắt app nhận đối số. Tuần 2 mới nhận đường dẫn JSON.

Trong file `.csproj`, hai dòng đáng đọc: `TargetFramework` là `net10.0`, `Nullable` là `enable`. `System.Text.Json` có sẵn trong shared framework khi sang tuần 2. Tuần này chưa thêm package.

### Rider

| Việc | Cách |
|---|---|
| Mở solution | Open `Geometry.slnx` |
| Chạy app | Nút Run, hoặc mũi tên cạnh top-level trong `Program.cs` |
| Chạy một test | Mũi tên cạnh `[Fact]` |
| Debug | Breakpoint ở lề trái. Bug icon để chạy dưới debugger |
| Bước | Step over, step into, step out. Xem keymap đang dùng vì phím tắt JetBrains và Visual Studio khác nhau |
| Đổi tên an toàn | Rename (Shift+F6 trên keymap JetBrains) |
| Tìm chỗ dùng | Find usages |
| Tới file / kiểu | Go to File, Go to Type |
| Format | Reformat code |
| Cảnh báo | Thanh vàng của nullable. Sửa code, đừng tắt cảnh báo |

Breakpoint trong `Volume` hoặc `Parse` cho thấy giá trị từng cạnh và từng mẩu.

## Code đọc được

Buổi 5 đọc hai chương đầu của *The Art of Readable Code* (code dễ hiểu, và đặt tên). Chưa có sách thì áp các thói quen dưới vào `Parse` và `Volume`. Việc nộp vẫn là phần “Làm” của buổi 5 trong giáo án.

**Tên nói việc.** `Volume`, `Parse`, `LargerThan`, `minVolume`, `boxes`. `line` giữ được vì tham số đúng là một dòng. Tránh `temp`, `data2`, `obj`, và method một chữ.

**Thoát sớm.** Điều kiện loại (dòng trắng, thiếu mẩu, hộp có max nhỏ hơn min) xử lý rồi `return` trước. Phần còn lại của hàm nằm ở mức indent thấp, đọc một mạch. Với hộp không hợp lệ, `Volume` trả `0`, không trả số âm. Tuần sau mới quyết định có ném lỗi hay không.

**Số có nghĩa.** Trong `Parse`, bảy mẩu là một id cộng sáu tọa độ. Đọc lại dòng điều kiện mà vẫn phải đếm trên đầu ngón tay thì đặt tên cho con số đó. Tuần này chưa đưa dung sai số thực vào so sánh.

**Một hàm một việc.** `Parse` đổi một dòng thành `Box?`. `Volume` tính thể tích của hộp đã có. `LargerThan` chỉ lọc. In ra màn hình nằm ở app.

**Comment nói vì sao.** Ít khi cần comment tuần này. Comment kiểu “tăng biến đếm” cạnh `i++` không thêm thông tin. Quy ước “chạm mặt vẫn là giao” là comment của tuần 2, chỗ toán tử `<=`.

Đọc `Parse` thành tiếng, từ đầu đến cuối, không thêm lời ngoài tên và cấu trúc. Người nghe chưa thấy file phải nói lại được dòng nào bị bỏ.

## Link

Đọc đúng mục, rồi quay lại buổi. Chưa mở hết catalog.

| Đọc | Khi nào |
|---|---|
| [Tour of C#](https://learn.microsoft.com/dotnet/csharp/tour-of-csharp/) — kiểu, method, biểu thức | Buổi 2. Dừng khi sang chủ đề khác |
| [Hello world trong Tour](https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/tutorials/hello-world) | Buổi 1, nếu cú pháp top-level còn lạ |
| [Tour tương tác](https://learn.microsoft.com/en-us/dotnet/csharp/tour-of-csharp/tutorials/) | Muốn chạy snippet ngắn ngoài repo |
| [Get started with C#, Part 1](https://learn.microsoft.com/en-us/training/paths/get-started-c-sharp-part-1/) | Skim chỗ khác web. Bỏ qua nếu giáo án đang chạy |
| [Nullable reference types](https://learn.microsoft.com/dotnet/csharp/nullable-references) | Buổi 3 |
| [LINQ](https://learn.microsoft.com/dotnet/csharp/linq/) — `Where`, `Select`, `ToList` | Buổi 4 |
| [Introduction to .NET](https://learn.microsoft.com/en-us/dotnet/core/introduction) | Khi lẫn SDK với runtime |
| [Get started with .NET](https://learn.microsoft.com/en-us/dotnet/core/get-started) | Khi lệnh `dotnet` chưa quen |
| [Hub .NET fundamentals](https://learn.microsoft.com/en-us/dotnet/fundamentals/) | Bookmark. Chưa đọc tuần này |

[Tổng quan System.Text.Json](https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json/overview) mở ở tuần 2, lúc viết `BoxFile.Load`.

## Từ Nhật

Năm từ của tuần: クラス, メソッド, 引数, 戻り値, ヌル. Bảng và cách đọc ở [phụ lục](../phu-luc-thuat-ngu.md).

干渉, 配管, 配筋, ファミリ, スナップショット có trong PDF và trong phần phụ của phụ lục. Chúng chưa phải bài tập từ vựng tuần 1.

## Cùng tiêu chí với giáo án

Tick khi buổi tương ứng trong giáo án xong.

| # | Việc | Buổi |
|---|---|---|
| 1 | App chạy, test mẫu xanh, có `record Box`, test `Id` là `"A"` | 1 |
| 2 | Ba test `Volume` xanh, nói được công thức không nhìn code | 2 |
| 3 | `Parse` ra hộp hoặc `null`, hết cảnh báo nullable trên file mới | 3 |
| 4 | `LargerThan` xanh, thân method không có `for` / `foreach` | 4 |
| 5 | Đọc được `Parse` thành tiếng. Mỗi buổi một commit | 5 |

Lời giải tham khảo nằm ở [đáp án tuần 1](../dap-an/tuan-01.md). Mở sau khi test của mình xanh. Chép đáp án vào `src/` thì tuần chưa xong.
