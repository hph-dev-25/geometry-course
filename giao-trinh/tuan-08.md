# Tuần 8 — Chốt bài và trình bày

Hết tuần này, repo có một trang thiết kế tiếng Nhật, một PR tự review, và bạn nói được bài trong năm phút. Bài nói gồm ba phần: tách việc, vẽ luồng, và nói chỗ chưa hiểu.

Thời lượng: 5 buổi × 90 phút.

## Giáo án

### Buổi 1 — Chốt phạm vi bài

Mục tiêu: một đoạn mười dòng nói bài này là gì.

Bài là: thư viện hộp, test, cửa sổ WPF, lệnh Revit đọc selection và hiện cặp. Không có đẩy tường trong Revit. `PushOut` nằm trong thư viện như năng lực đã học, lệnh Revit không gọi nó, trừ khi bạn ghi rõ đó là việc chưa nối.

Viết mười dòng đó lên đầu `notes/tuan-08.md`.

Xong khi: một người đọc mười dòng đó biết cái gì không có trong bài.

### Buổi 2 — `DESIGN.ja.md`

Mục tiêu: một trang tiếng Nhật, đúng các mục dưới đây.

Tạo `DESIGN.ja.md` ở gốc repo. Viết ngắn. Mỗi mục năm đến mười dòng.

```text
# 干渉チェック

## 背景
## 入出力
## 処理の流れ
## Core と Revit の分担
## 干渉のルール
## やらないこと
## 確認事項
## テスト
```

Gợi ý nội dung, viết lại bằng câu của mình:

- 背景: muốn xem phần tử đang chọn có đụng không
- 入出力: selection vào, hộp thoại ra
- 分担: Revit lấy hộp và hiện chữ. Core quyết định cặp nào giao
- ルール: chạm mặt là giao. Số của Revit đang là feet nội bộ
- やらないこと: không sửa model, không giao theo solid, không tự dịch
- 確認事項: một câu chưa chắc, ví dụ đơn vị có cần hiện bằng milimét không
- テスト: tên ba test quan trọng nhất đang có

Xong khi: bạn đọc trang này thành tiếng trong bốn phút.

### Buổi 3 — Sơ đồ

Mục tiêu: một hình trong `notes/tuan-08.md`.

Ba hộp: Revit command, Core, WPF. Mũi tên từ command và từ WPF vào Core. Không có mũi tên từ Core đi ra. Bên cạnh mũi tên ghi tên method: `FindPairs`, `FromCorners`.

Xong khi: hình không có kiểu `Element` nằm trong khối Core.

### Buổi 4 — PR tự review

Mục tiêu: một nhánh, một PR, comment của chính mình.

```bash
git checkout -b tuan-08
git push -u origin tuan-08
gh pr create --title "Tuần 8: chốt bài interference check" --body-file notes/tuan-08-pr.md
```

`notes/tuan-08-pr.md` có các mục: đã có gì, chưa có gì, cách chạy test, cách chạy lệnh Revit.

Mở PR trên GitHub, tự comment hai chỗ trong diff bằng checklist tuần 7. Một chỗ đạt. Một chỗ còn yếu, nói sẽ để vậy vì ngoài phạm vi hoặc sẽ sửa trong tuần này nếu còn giờ.

Xong khi: PR mở được, có hai comment, `dotnet test` trên máy vẫn xanh.

### Buổi 5 — Nói năm phút

Mục tiêu: một lần nói, không slide.

Thứ tự:

1. Bài giải quyết việc gì. 30 giây.
2. Core làm gì, Revit làm gì. Một phút.
3. Vì sao chạm mặt vẫn là giao. Chỉ vào test. Một phút.
4. Việc không làm. 30 giây.
5. Một chỗ chưa hiểu hoặc chưa nối. 30 giây.
6. Dừng.

Đọc to hai lần. Lần hai bấm giờ. Viết giờ vào cuối `notes/tuan-08.md`.

Xong khi: lần hai không quá năm phút rưỡi, và có câu về test chạm mặt.

## Bài học

Trang thiết kế không lặp lại code. Nó giữ những quyết định mà đọc code mất mười phút mới thấy: chạm mặt có tính không, đơn vị là gì, cái gì cố ý không làm.

Câu "chưa hiểu" là một phần của bài nói. Nói được chỗ spec chưa đủ tốt hơn là bịa cho kín.

## Bài tập nộp

- `DESIGN.ja.md`
- `notes/tuan-08.md` có sơ đồ và giờ nói
- một PR với hai comment review
- test xanh

## Tự chấm

| Mục | Đạt |
|---|---|
| Trang Nhật có đủ tám mục | |
| Sơ đồ không để `Element` trong Core | |
| PR nói được việc chưa làm | |
| Nói lại dưới năm phút rưỡi | |
| Chỉ được test chứng minh chạm mặt là giao | |

Hết tuần 8, repo này là bài mang đi. Muốn để người khác xem thì đổi repo từ private sang public trên GitHub. Không cần làm thêm sản phẩm mới.
