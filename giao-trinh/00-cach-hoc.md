# Cách học

Giáo trình này dành cho người đã làm web, chưa viết C# và chưa dùng CAD. Phần tiếng Nhật trong khóa chỉ đủ để đọc một spec ngắn và nói lại một thiết kế.

## Một buổi

1. Đọc mục tiêu buổi. Nếu không nói được mục tiêu bằng một câu, đọc lại.
2. Làm đúng phần "Làm". Không nhảy sang bài của tuần sau.
3. Chạy test trước khi đóng Rider.
4. Đối chiếu câu "Xong khi". Chưa đạt thì hôm sau làm nốt buổi đó, không mở buổi mới.

Mỗi tuần năm buổi, mỗi buổi khoảng 90 phút. Thiếu một buổi thì kéo dài tuần, không cắt bài tập.

## Nơi để code

| Loại | Chỗ |
|---|---|
| Kiểu, phép tính, đọc dữ liệu | `src/Week01.Core` |
| In ra console | `src/Week01.App` |
| Test | `tests/Week01.Tests` |
| Giao diện, Revit | project mới, từ tuần 3. Project đó chỉ được gọi Core |

Tên project `Week01` giữ đến hết tuần 5 cho đỡ đổi đường dẫn giữa chừng. Tuần 6 đổi tên thành `Clash.Core` bằng một commit riêng.

App và Revit không chứa công thức giao nhau. Test không cần mở Revit.

## Quy ước hình học dùng suốt khóa

`Box` là hộp chữ nhật thẳng trục. Hai góc đối nhau là min và max.

- Hộp hợp lệ khi `Max >= Min` trên cả ba trục.
- `Intersects`: chạm mặt cũng tính là giao. Trên mỗi trục, `aMin <= bMax` và `bMin <= aMax`.
- `Overlaps`: chồng thể tích, tức giao mà không chỉ chạm mặt. So sánh bằng `<` thay vì `<=`.
- Cặp giao lưu một lần. Id đứng trước theo thứ tự chữ cái viết trước. Không cặp một hộp với chính nó.

Hai quy ước này được chốt từ tuần 1 để tuần 6 khỏi đổi nghĩa test.

## Khi kẹt

Viết lại được điều đã biết, điều vừa thử, và câu lỗi nguyên văn. Kẹt quá 40 phút thì ghi câu hỏi vào một file `notes/tuan-XX.md` rồi sang bài đọc của buổi đó. Không để một lỗi build nuốt cả buổi.

## Đáp án

[Đáp án](dap-an/README.md) là để đối chiếu sau khi test của mình đã xanh, hoặc sau khi đã viết được một bản chạy nhưng thấy xấu. Chép đáp án vào `src/` thì tuần đó chưa tính xong.

## Git

Mỗi buổi một commit, câu chữ nói việc vừa xong. Ví dụ: `Add Box.Volume and its tests`. Tuần 7 sẽ review chính các commit này.
