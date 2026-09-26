# Tuần 7 — Đọc spec và tách việc

Hết tuần này, bạn tách được một spec tiếng Nhật thành ticket, review được một thay đổi, và nói được một daily 15 phút. Code mới không phải là sản phẩm của tuần. Sản phẩm là trang viết.

Thời lượng: 5 buổi × 90 phút.

## Spec dùng cả tuần

Đọc tiếng Nhật trước. Bản tiếng Việt ở dưới là để kiểm tra sau buổi 1, không đọc cùng lúc.

### 仕様: 選択要素の干渉チェック

背景。Revitで作業中に、今選んでいる要素同士がぶつかっていないかを短い操作で見たい。

やりたいこと。

1. ユーザーは要素を2つ以上選ぶ。
2. アドインのコマンド「干渉チェック」を実行する。
3. 各要素のバウンディングボックスを取る。取れない要素はスキップし、スキップ件数を結果に出す。
4. ボックス同士の干渉ペアを一覧する。面が触れていても干渉とする。
5. ペアが0件のときは「干渉なし」と出す。
6. モデルは変更しない。

対象外。ソリッド同士の精密な干渉、要素の自動移動、ファミリ編集。

完了の見方。

- 選択が0件のときは、選ぶように案内する。コマンドは失敗扱いにしない。
- 離れた2要素では「干渉なし」。
- 重なった2要素ではペアが1件出る。
- 面だけが触れる2要素でもペアが1件出る。

### Cùng spec, tiếng Việt

Người dùng muốn biết các phần tử đang chọn có đụng nhau không, bằng một lệnh ngắn.

Chọn từ hai phần tử, chạy lệnh. Lấy bounding box. Phần tử không có hộp thì bỏ qua và đếm. Liệt kê cặp giao. Chạm mặt vẫn là giao. Không có cặp thì báo không giao. Không sửa model.

Không làm giao theo solid, không tự dịch phần tử, không sửa family.

Xong khi: không chọn gì thì chỉ hướng dẫn, lệnh vẫn thành công. Hai phần tử xa thì không giao. Hai phần tử chồng thì một cặp. Hai phần tử chạm mặt thì vẫn một cặp.

## Giáo án

### Buổi 1 — Đọc spec

Mục tiêu: nói lại spec bằng tiếng Việt mà không nhìn bản dịch.

Đọc bản tiếng Nhật hai lần. Che bản tiếng Việt. Viết lại các ý vào `notes/tuan-07-doc.md`. Rồi mở bản dịch, gạch ý nào bị sót. Ý dễ sót là "chạm mặt vẫn tính" và "lệnh không bị tính là thất bại khi chưa chọn gì".

Xong khi: lần viết lại thứ hai không còn sót hai ý đó.

### Buổi 2 — Tách ticket

Mục tiêu: ba ticket mà một người chưa họp vẫn làm được.

Mỗi ticket có đủ các mục:

- tiêu đề
- mục đích một câu
- làm
- không làm
- xong khi
- cách test

Chia thế này:

1. Đổi bounding box Revit thành `Box`, kể cả phần tử không có hộp.
2. Hiện cặp giao theo đúng quy ước chạm mặt.
3. Câu chữ khi không chọn gì, và dòng đếm số bỏ qua.

Viết bản tiếng Việt cho người sẽ code, và bản tiếng Nhật năm đến mười dòng cho người bên Nhật đọc lại. Để trong `notes/tuan-07-tickets.md`.

Xong khi: ticket 2 không chứa bước cài Revit, ticket 1 không chứa câu chữ hộp thoại.

### Buổi 3 — Review một diff giả

Mục tiêu: comment như sẽ gửi cho đồng nghiệp.

Diff giả này triển khai nhầm spec:

```text
+ if (ids.Count == 0)
+     return Result.Failed;
+ var box = element.get_BoundingBox(null);
+ boxes.Add(FromCorners(id, box.Min.X, box.Min.Y, box.Min.Z, box.Max.X, box.Max.Y, box.Max.Z));
+ if (a.Overlaps(b))
+     pairs.Add(...);
```

Viết ba comment. Mỗi comment nói việc spec yêu cầu và dòng nào làm khác. Không viết "sai rồi". Viết "spec nói chạm mặt vẫn là giao, `Overlaps` thì bỏ cặp chỉ chạm mặt".

Để trong `notes/tuan-07-review.md`.

Xong khi: ba comment covers đủ ba chỗ trong diff: `Failed`, không kiểm tra null, và `Overlaps` thay vì `Intersects`.

### Buổi 4 — Daily

Mục tiêu: nói trong bảy phút, không quá mười lăm.

Soạn bốn câu, đúng thứ tự:

1. Hôm qua xong ticket nào.
2. Hôm nay làm ticket nào.
3. Đang kẹt ở đâu, một câu.
4. Cần người viết spec xác nhận điều gì.

Đọc to. Bấm giờ. Nếu quá mười phút, cắt giải thích nguyên nhân, giữ câu hỏi.

Viết bản nói vào `notes/tuan-07-daily.md`. Một bản tiếng Việt, một bản tiếng Nhật ngắn của câu 4. Câu 4 mẫu nếu bạn không nghĩ ra câu khác: 「面が触れる場合も干渉に含める、で合っていますか。」

Xong khi: đọc một mạch dưới bảy phút.

### Buổi 5 — Checklist review để dùng lại

Mục tiêu: một danh sách mười ý, tuần 8 sẽ áp vào PR của mình.

`notes/tuan-07-checklist.md` gồm mười dòng có/không. Bắt đầu từ các ý này và thêm cho đủ mười:

- tên method nói việc nó làm
- null của bounding box được xử lý
- không có `Transaction` trong lệnh chỉ đọc
- Core không có kiểu Revit
- `Intersects` chứ không phải `Overlaps` khi spec nói chạm mặt là giao
- test có cặp chạm mặt
- câu thông báo khi selection rỗng không dùng `Result.Failed`
- app WPF không chứa công thức
- một comment tại `<=` nói quy ước
- build được khi Revit đã đóng

Xong khi: bạn dùng checklist này soi `FindPairs` và ghi được ít nhất một ý đã đạt.

## Bài học

Ticket tốt là ticket người nhận không cần dự họp. "Làm phần giao nhau" không phải ticket. "Hiện các cặp `Intersects` của selection, kể cả chạm mặt, không sửa model" là ticket.

Review chỉ việc và quy ước. "Đặt tên lại cho đẹp" không đủ cho buổi 3. Ba lỗi trong diff là lỗi hành vi.

Daily không phải báo cáo cảm xúc. Bốn câu ở buổi 4 là đủ cho một lần nói ngắn về việc đang làm.

## Bài tập nộp

Bốn file trong `notes/`:

- `tuan-07-doc.md`
- `tuan-07-tickets.md`
- `tuan-07-review.md`
- `tuan-07-daily.md`
- `tuan-07-checklist.md`

## Tự chấm

| Mục | Đạt |
|---|---|
| Bản tự nhớ có cả "chạm mặt" và "không Failed" | |
| Ba ticket không chồng phạm vi | |
| Ba comment review trúng ba lỗi | |
| Daily đọc dưới bảy phút | |

Chưa viết `DESIGN.ja.md`. Tuần 8 viết từ các note này.
