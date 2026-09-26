# Tuần 4 — Revit, lệnh đầu tiên

Hết tuần này, Revit có một lệnh do mình viết. Lệnh đọc các phần tử đang chọn, hiện số phần tử và số phần tử có bounding box. Chưa kiểm tra giao nhau.

Thời lượng: 5 buổi × 90 phút. Gõ trên Linux được. Bấm chạy trong Revit thì phải ở Windows.

## Giáo án

### Buổi 1 — Từ vựng của add-in

Mục tiêu: nói được lệnh của mình sẽ ngồi ở đâu trong Revit.

Đọc, và ghi vào `notes/tuan-04.md` bằng tiếng Việt, mỗi ý một câu:

- Revit là phần mềm BIM. Add-in là DLL được Revit tải lúc mở.
- Một lệnh hiện trong tab Add-Ins sau khi có file `.addin`.
- `IExternalCommand.Execute` là hàm Revit gọi khi người dùng bấm lệnh.
- Đọc dữ liệu không cần `Transaction`. Sửa model thì cần. Tuần này chỉ đọc.
- API Revit chạy trên thread của Revit. Không đưa `Element` vào `Task.Run`.

Xong khi: năm câu đó viết được mà không mở lại trang này.

### Buổi 2 — Cài Revit và tạo project

Mục tiêu: project build ra một DLL.

Trong Windows, cài Revit trial, bản 2025 hoặc 2026. Nhớ số năm. Gói NuGet phải cùng năm đó.

Trên máy sẽ build, Linux hoặc Windows:

```bash
dotnet new classlib -n Week04.Revit -o src/Week04.Revit -f net8.0
dotnet sln add src/Week04.Revit
```

Sửa csproj:

```xml
<PropertyGroup>
  <TargetFramework>net8.0-windows</TargetFramework>
  <EnableWindowsTargeting>true</EnableWindowsTargeting>
  <ImplicitUsings>enable</ImplicitUsings>
  <Nullable>enable</Nullable>
</PropertyGroup>
```

Thêm gói, đổi `2026.*` thành năm Revit đã cài nếu khác:

```bash
dotnet add src/Week04.Revit package Nice3point.Revit.Api.RevitAPI -v 2026.*
dotnet add src/Week04.Revit package Nice3point.Revit.Api.RevitAPIUI -v 2026.*
```

Nếu NuGet không nhận `2026.*`, mở nuget.org, gói `Nice3point.Revit.Api.RevitAPI`, chọn version có prefix đúng năm Revit.

`EnableWindowsTargeting` để SDK trên Linux chịu build target Windows. DLL vẫn chỉ tải được trong Revit.

Xong khi: `dotnet build src/Week04.Revit` xanh trên máy đang gõ.

### Buổi 3 — Lệnh hiện một hộp thoại

Mục tiêu: bấm được lệnh trong Revit, chưa đọc phần tử.

```csharp
using Autodesk.Revit.Attributes;
using Autodesk.Revit.DB;
using Autodesk.Revit.UI;

namespace Week04.Revit;

[Transaction(TransactionMode.Manual)]
public sealed class HelloCommand : IExternalCommand
{
    public Result Execute(ExternalCommandData commandData, ref string message, ElementSet elements)
    {
        TaskDialog.Show("Clash", "lenh da chay");
        return Result.Succeeded;
    }
}
```

`TransactionMode.Manual` nghĩa là mình tự mở transaction khi nào cần sửa. Tuần này không mở.

Tạo file `Clash.addin` trong `%AppData%\Autodesk\Revit\Addins\2026\` (đổi năm cho đúng). `Assembly` trỏ tới DLL trong `bin/Debug/net8.0-windows/`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<RevitAddIns>
  <AddIn Type="Command">
    <Name>Clash Hello</Name>
    <Assembly>C:\duong\dan\tuyet\doi\Week04.Revit.dll</Assembly>
    <AddInId>8f6c1c4e-9a2b-4d5e-8c7a-1234567890ab</AddInId>
    <FullClassName>Week04.Revit.HelloCommand</FullClassName>
    <VendorId>STUD</VendorId>
  </AddIn>
</RevitAddIns>
```

`AddInId` là một GUID tự sinh, giữ cố định sau lần đầu. Sinh bằng `[guid]::NewGuid()` trong PowerShell.

Mở Revit, tab Add-Ins, bấm lệnh. Thấy hộp thoại.

Xong khi: đóng Revit, build lại, mở Revit, lệnh vẫn còn. Revit khóa DLL khi đang mở, nên build sẽ lỗi nếu chưa đóng Revit. Đó là việc bình thường, ghi vào note.

### Buổi 4 — Đọc phần tử đang chọn

Mục tiêu: hộp thoại hiện hai số.

```csharp
var uidoc = commandData.Application.ActiveUIDocument;
var ids = uidoc.Selection.GetElementIds();
var doc = uidoc.Document;
var withBox = 0;
foreach (var id in ids)
{
    var element = doc.GetElement(id);
    if (element?.get_BoundingBox(null) != null)
        withBox++;
}
```

`get_BoundingBox(null)` lấy hộp trong không gian model. Một số phần tử không có hộp. Chúng được đếm vào số chọn, không đếm vào `withBox`.

Hộp thoại hiện `chon: N, co hop: M`.

Xong khi: chọn hai bức tường thì `co hop` bằng 2. Không chọn gì thì `chon: 0` và lệnh vẫn `Succeeded`, không ném.

### Buổi 5 — Nói được ranh giới

Mục tiêu: chỉ ra dòng nào sẽ không được phép ở tuần 5.

Trong `HelloCommand`, đánh dấu bằng comment những dòng nào đụng kiểu của Revit (`Element`, `Document`, `XYZ`). Những dòng đó không được chuyển vào `Week01.Core`.

Viết mười dòng trong `notes/tuan-04.md`: lệnh làm gì, không làm gì, vì sao không có `Transaction`.

Xong khi: note đó đọc được mà không cần mở Revit.

## Bài học

File `.addin` là manifest. Revit đọc nó lúc khởi động, tải DLL, tìm đúng class trong `FullClassName`. Sai tên class thì lệnh không hiện, Revit ghi lỗi trong journal. Journal nằm trong `%LocalAppData%\Autodesk\Revit\Autodesk Revit 2026\Journals\`. Khi lệnh biến mất, mở journal mới nhất và tìm tên class của mình.

`Result.Failed` cộng với `message` để Revit hiện lỗi của mình. Tuần này đường đi đúng trả `Succeeded`. Đường đi "không chọn gì" cũng là thành công, vì người dùng được thông báo bằng hộp thoại.

## Bài tập nộp

- `src/Week04.Revit` build xanh
- `HelloCommand` đếm phần tử chọn và phần tử có bounding box
- một ảnh hoặc một dòng note ghi lại hai số khi chọn hai tường
- `notes/tuan-04.md`
- Core chưa có `using Autodesk`

## Tự chấm

| Mục | Đạt |
|---|---|
| Lệnh hiện trong Revit | |
| Không chọn gì thì không crash | |
| Build lại được sau khi đóng Revit | |
| Core không tham chiếu Revit | |

Chưa chuyển tọa độ Revit sang `Box`. Đó là tuần 5.
