# C#, hình học 3D, WPF, Revit

Giáo trình 8 tuần. Mỗi tuần có giáo án theo buổi, bài học, bài tập và tiêu chí tự chấm.

Code trên máy này nằm ở `~/learn/geometry-course`. Tuần 1 bắt đầu từ `Hello, World!` có sẵn. Chưa có lời giải trong `src/`. Lời giải tham khảo nằm ở [giao-trinh/dap-an](giao-trinh/dap-an/README.md). Làm bài trước, mở sau.

## Bắt đầu

```bash
rider ~/learn/geometry-course/Geometry.slnx
```

Hoặc:

```bash
cd ~/learn/geometry-course
dotnet run --project src/Week01.App
dotnet test
```

Đọc theo thứ tự trong [mục lục giáo trình](giao-trinh/README.md). Buổi đầu tiên nằm ở [tuần 1](giao-trinh/tuan-01.md).

## Tám tuần

| Tuần | Việc | Nơi làm |
|---|---|---|
| 1 | `record`, nullable, LINQ, test | Omarchy |
| 2 | JSON, hộp giao nhau, đọc một hàm dài | Omarchy |
| 3 | WPF: danh sách hộp và nút kiểm tra | Windows |
| 4 | Revit add-in đầu tiên, đọc bounding box | Windows để chạy, Linux để gõ |
| 5 | Đưa hộp Revit vào đúng thư viện tuần 2 | Windows để chạy |
| 6 | Vector, đẩy hộp hết chồng thể tích | Omarchy |
| 7 | Spec tiếng Nhật, ticket, review | Omarchy |
| 8 | Trang thiết kế và trình bày 5 phút | Cả hai |

Windows là máy ảo sẵn trên Omarchy (`omarchy-windows-vm launch -k`). Chưa cần mở cho đến tuần 3. Chi tiết máy ở [môi trường](giao-trinh/01-moi-truong.md).

## Cách học

Mỗi tuần khoảng 8–10 giờ, năm buổi. Một buổi có mục tiêu, việc làm, và câu "xong khi". Cuối tuần tự chấm theo rubric trong giáo án. Nhịp và quy tắc nằm ở [cách học](giao-trinh/00-cach-hoc.md).
