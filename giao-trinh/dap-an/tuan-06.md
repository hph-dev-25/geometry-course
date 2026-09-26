# Đáp án tuần 6

## Số của ví dụ

A `[0,0,0]-[2,2,2]`, B `[1,1,1]-[3,3,3]`.

Độ chồng mỗi trục:

```text
min(2, 3) - max(0, 1) = 1
```

Ba trục bằng nhau, chọn X. Tâm A trên X là 1. Tâm B trên X là 2. 2 lớn hơn 1, đẩy B theo chiều tăng thêm đúng 1.

B sau khi đẩy: `[2,1,1]-[4,3,3]`.

`Overlaps` lúc này là false. `Intersects` vẫn true vì mặt `x = 2` chạm nhau.

## Hướng còn lại

Nếu hộp đang đẩy có tâm nhỏ hơn trên trục đã chọn, trừ `depth` khỏi min và max của trục đó. Một test nên khóa hướng này, ví dụ đổi B thành `[-1,-1,-1]-[1,1,1]` đối với A. Độ chồng vẫn là 1 trên mỗi trục. Tâm B là 0, nhỏ hơn tâm A là 1, nên B thành `[-2,-1,-1]-[0,1,1]`.

## Cross

`(1,0,0) × (0,1,0) = (0,0,1)`. Đổi thứ tự hai vector thì đổi dấu. `PushOut` không gọi `Cross`.
