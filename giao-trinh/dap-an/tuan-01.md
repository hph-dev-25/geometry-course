# Đáp án tuần 1

Một cách viết đủ test của tuần. Tên biến có thể khác. Hành vi thì không.

```csharp
namespace Week01.Core;

public sealed record Box(
    string Id,
    double MinX, double MinY, double MinZ,
    double MaxX, double MaxY, double MaxZ)
{
    public double Volume()
    {
        if (MaxX < MinX || MaxY < MinY || MaxZ < MinZ)
            return 0;
        return (MaxX - MinX) * (MaxY - MinY) * (MaxZ - MinZ);
    }

    public static Box? Parse(string? line)
    {
        if (string.IsNullOrWhiteSpace(line))
            return null;
        var parts = line.Split(' ', StringSplitOptions.RemoveEmptyEntries);
        if (parts.Length != 7)
            return null;
        if (!double.TryParse(parts[1], out var minX)) return null;
        if (!double.TryParse(parts[2], out var minY)) return null;
        if (!double.TryParse(parts[3], out var minZ)) return null;
        if (!double.TryParse(parts[4], out var maxX)) return null;
        if (!double.TryParse(parts[5], out var maxY)) return null;
        if (!double.TryParse(parts[6], out var maxZ)) return null;
        return new Box(parts[0], minX, minY, minZ, maxX, maxY, maxZ);
    }
}

public static class BoxQueries
{
    public static List<Box> LargerThan(IEnumerable<Box> boxes, double minVolume)
        => boxes.Where(box => box.Volume() > minVolume).ToList();
}
```

`Parse` cố ý chưa kiểm tra hộp có max nhỏ hơn min. Dòng đó vẫn là hộp, `Volume` sẽ trả 0. Nếu bản của bạn trả `null` trong trường hợp đó thì vẫn hợp lệ, miễn test nói rõ quyết định đó.
