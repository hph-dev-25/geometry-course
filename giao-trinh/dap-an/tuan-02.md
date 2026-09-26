# Đáp án tuần 2

## `Intersects` và cặp

```csharp
public bool Intersects(Box other)
{
    if (!IsOrdered() || !other.IsOrdered())
        return false;
    return RangesMeet(MinX, MaxX, other.MinX, other.MaxX)
        && RangesMeet(MinY, MaxY, other.MinY, other.MaxY)
        && RangesMeet(MinZ, MaxZ, other.MinZ, other.MaxZ);
}

private bool IsOrdered() => MaxX >= MinX && MaxY >= MinY && MaxZ >= MinZ;

// Chạm mặt vẫn là giao.
private static bool RangesMeet(double aMin, double aMax, double bMin, double bMax)
    => aMin <= bMax && bMin <= aMax;
```

`FindPairs` duyệt `i` từ 0, `j` từ `i + 1`, để một hộp không cặp với chính nó và không sinh cặp đảo. Rồi `OrderedPair` sắp hai id bằng `string.CompareOrdinal`.

File mẫu ra đúng hai cặp, theo thứ tự gặp: `A B`, rồi `A D`.

## Ba lỗi của đoạn review

1. Chỉ so `MaxX` với `MinX` của một phía. Thiếu trục Y, Z, và thiếu chiều còn lại `bMin <= aMax` theo đúng công thức. Hai hộp chỉ cần đứng gần trên trục X là bị tính giao.
2. Vòng `j` chạy cả khi `j == i`, nên mỗi hộp cặp với chính nó.
3. Cùng một cặp bị thêm hai lần, `(A,B)` và `(B,A)`, vì không có `j > i` và không chuẩn hóa thứ tự id.

Đoạn đó cũng không kiểm tra hộp hợp lệ. Đó là ý thứ tư, được tính nếu bạn đã viết.
