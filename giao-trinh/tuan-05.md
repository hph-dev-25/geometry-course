# Tuần 5 — Nối Revit với thư viện

Hết tuần này, lệnh Revit lấy bounding box của phần tử đang chọn, đổi thành `Box`, gọi `FindPairs`, và hiện các cặp. Model không bị sửa. `Week01.Core` vẫn không biết Revit là gì.

Thời lượng: 5 buổi × 90 phút.

## Giáo án

### Buổi 1 — Hàm đổi đơn vị dữ liệu

Mục tiêu: một hàm thuần, test được, không mở Revit.

Trong project Revit, thêm class `RevitBoxes`. Method:

```csharp
public static Box? FromBoundingBox(string id, BoundingBoxXYZ? box)
```

`box` null thì trả về null. Ngược lại tạo `Box` từ `box.Min` và `box.Max`. Id là `ElementId` đổi thành chuỗi.

Test method này trên Linux được nếu tách phần đọc `XYZ` ra số trước khi gọi Core. Cách làm cho test không cần Revit: method thứ hai, thuần số, nằm trong Core hoặc trong Revit project nhưng không đụng `Document`.

```csharp
public static Box FromCorners(string id, double minX, double minY, double minZ,
    double maxX, double maxY, double maxZ)
```

`FromBoundingBox` chỉ việc lấy sáu số rồi gọi `FromCorners`. Test `FromCorners`.

Xong khi: test tạo hộp từ sáu số xanh, và `Week01.Core.csproj` không có package Revit.

### Buổi 2 — Lệnh gọi FindPairs

Mục tiêu: hộp thoại hiện cặp id.

Trong `Execute`:

1. Không có selection thì hiện `hay chon it nhat 2 phan tu` và trả `Succeeded`.
2. Với mỗi id, `GetElement`, lấy bounding box, bỏ qua phần tử không có hộp, đếm số bỏ qua.
3. Gọi `BoxQueries.FindPairs`.
4. Hiện các cặp, mỗi cặp một dòng, rồi một dòng `bo qua: N`.

Không tạo `Transaction`.

Xong khi: hai tường chồng lên nhau trong một file thử cho ra một cặp. Hai tường cách xa cho ra `khong giao`.

### Buổi 3 — File thử nhỏ

Mục tiêu: có một việc lặp lại được, không phụ thuộc model lớn.

Trong Revit, file mới, vẽ bốn tường tạo thành hai cặp: một cặp cắt nhau, một cặp cách xa. Save thành `notes/tuan-05-sample.rvt` trên máy Windows. File này không commit nếu quá nặng hoặc nếu license không cho đưa file Revit lên git công khai. Commit một `notes/tuan-05.md` mô tả bốn tường: tường nào giao tường nào.

Chạy lệnh ba lần: không chọn, chọn cặp xa, chọn cặp gần. Ghi ba kết quả vào note.

Xong khi: người đọc note dựng lại được ba lần chạy mà không cần mở file.

### Buổi 4 — Cấm kiểu Revit lọt vào Core

Mục tiêu: rà một lần cho chắc.

Tìm trong `src/Week01.Core` các chuỗi `Autodesk`, `XYZ`, `Element`, `Document`. Không được có.

Tìm trong `HelloCommand` chữ `<=` và công thức thể tích. Không được có. Chúng nằm trong Core từ tuần 2.

Nếu tuần 4 để logic đếm trong command, phần đếm "có hộp hay không" được phép ở lại command vì nó là việc của Revit. Phần "hai hộp có giao không" thì không.

Xong khi: hai lệnh tìm trên không ra kết quả sai chỗ.

### Buổi 5 — Viết lại luồng một trang

Mục tiêu: một trang mà tuần 8 sẽ phóng to thành `DESIGN.ja.md`.

`notes/tuan-05.md` thêm các mục:

- đầu vào: selection
- đầu ra: hộp thoại
- việc Revit làm
- việc Core làm
- việc không làm: không sửa model, không giao theo solid thật
- một lỗi đã gặp và cách xử lý, ví dụ DLL bị khóa

Xong khi: trang đó dài khoảng một màn hình, không dài hơn.

## Bài học

`BoundingBoxXYZ.Min` và `Max` là `XYZ`, đơn vị feet nội bộ của Revit. Tuần này giữ nguyên số đó trong `Box`. Chưa đổi sang milimét. Ghi một dòng comment tại `FromBoundingBox`: số là feet nội bộ, chưa đổi đơn vị. Nếu đổi ngầm, test thể tích của tuần 1 sẽ không còn cùng hệ với hộp thoại.

`ElementId.Value` trên Revit đời mới là `long`. Đổi sang chuỗi bằng `ToString()`. Không dùng id vẽ trên màn hình làm id duy nhất giữa hai lần mở file khác nhau. Trong một lần chạy lệnh thì đủ.

Đọc thì không cần transaction. Mở transaction rồi không sửa gì vẫn vô hại, nhưng nó dạy sai thói quen. Tuần này không mở.

## Bài tập nộp

- `FromCorners` có test
- lệnh Revit in cặp và số phần tử bỏ qua
- `notes/tuan-05.md` đủ các mục buổi 5
- Core không tham chiếu Revit
- không có `Transaction` trong command

## Tự chấm

| Mục | Đạt |
|---|---|
| Cặp gần hiện, cặp xa không hiện | |
| Không chọn gì thì có câu hướng dẫn | |
| Đóng Revit rồi build lại được | |
| Một trang note nói được phần nào là Core | |

Chưa đẩy hộp ra khỏi nhau. Đó là tuần 6, và chỉ đẩy trên số, không đẩy tường trong Revit.
