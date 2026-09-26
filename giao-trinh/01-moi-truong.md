# Môi trường trên máy này

## Đang có

| Thứ | Chỗ |
|---|---|
| SDK | .NET 10.0.401, cài bằng mise. `dotnet --version` in `10.0.401` |
| IDE | Rider 2026.2.2. Lệnh `rider`, hoặc mục Rider trong launcher |
| Solution | `/mnt/storage/learn/geometry-course/Geometry.slnx` trên ổ Storage (`/mnt/storage`) |
| Windows | Máy ảo Windows 11 của Omarchy, ổ ảo 64 GB |

`/usr/bin/dotnet` chỉ là runtime của hệ thống, không có SDK. Terminal và lệnh `rider` đã trỏ vào SDK của mise. Terminal mở từ trước khi cài thì đóng và mở lại.

## Kiểm tra trước buổi 1

```bash
dotnet --version
dotnet test --project /mnt/storage/learn/geometry-course
```

Kỳ vọng: version `10.0.401`, một test pass.

## Windows, từ tuần 3

```bash
omarchy-windows-vm launch -k
```

Cờ `-k` giữ máy ảo sống sau khi đóng cửa sổ. Không có cờ đó, đóng RDP là máy ảo tắt. Lần mở có thể hỏi mật khẩu quản trị vì Docker cần quyền đó.

Trong Windows, clone lại chính repo GitHub này. Không sửa chung một thư mục qua ổ share `~/Windows` trong lúc Linux cũng đang commit. Ổ share chỉ dùng để copy DLL add-in khi cần.

Trước tuần 4, mở Task Manager trong Windows.

- RAM khoảng 4 GB và 2 nhân: Revit không đủ. Ghi lại số đó trước khi cài Revit.
- Ổ C còn dưới khoảng 35 GB: chưa cài Revit. Cài Rider trong Windows, không cài nguyên bộ Visual Studio.

Card NVIDIA của máy không gắn vào máy ảo. Khung nhìn 3D của Revit sẽ chậm. Bài đọc bounding box vẫn làm được.

## Revit và .NET 8

Add-in Revit 2025 và 2026 chạy trên .NET 8. SDK 10 trên Omarchy vẫn build được project `net8.0`. Project có `UseWPF` chỉ build và chạy trong Windows.
