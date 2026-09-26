# Tuần 3 — WPF

Hết tuần này, một cửa sổ Windows hiện các hộp trong file JSON tuần 2, có nút kiểm tra, và bảng các cặp giao. Công thức giao nhau vẫn nằm trong `Week01.Core`. Project WPF chỉ gọi.

Thời lượng: 5 buổi × 90 phút. Làm trong Windows. Trên Omarchy, WPF không chạy.

## Giáo án

### Buổi 1 — Mở được solution trong Windows

Mục tiêu: cùng một repo, build được trên Windows, test tuần 2 vẫn xanh.

Làm:

1. `omarchy-windows-vm launch -k`
2. Cài Rider trong Windows nếu chưa có. Dùng cùng tài khoản đã đăng nhập Rider trên Linux.
3. Cài SDK .NET 10 cho Windows từ trang tải bộ cài .NET, vì máy ảo không dùng mise của Linux.
4. Clone repo. Mở `Geometry.slnx`. Chạy `dotnet test`.

Xong khi: test tuần 1 và 2 xanh trong Windows, và bạn biết thư mục clone nằm ở đâu trong ổ C.

### Buổi 2 — Project WPF và một nút

Mục tiêu: cửa sổ hiện, bấm nút được, chưa cần dữ liệu thật.

Trong Rider trên Windows:

```bash
dotnet new wpf -n Week03.App -o src/Week03.App -f net10.0
dotnet sln add src/Week03.App
dotnet add src/Week03.App reference src/Week01.Core
```

`UseWPF` sẽ có trong csproj do template tạo. Giữ target `net10.0` cho app học này. Add-in Revit tuần sau mới xuống `net8.0-windows`.

Xóa phần code-behind đang để logic. Buổi này chỉ cần một `Window` với một `Button` và một `TextBlock`. Bấm nút thì text thành `da bam`. Làm bằng binding, không viết `textBlock.Text = ...` trong sự kiện click.

Khuôn tối thiểu, để trong project WPF:

```csharp
public sealed class RelayCommand : ICommand
{
    private readonly Action _execute;
    public RelayCommand(Action execute) => _execute = execute;
    public event EventHandler? CanExecuteChanged;
    public bool CanExecute(object? parameter) => true;
    public void Execute(object? parameter) => _execute();
}
```

`CanExecuteChanged` tuần này chưa dùng. Giữ event để đúng interface.

Xong khi: bấm nút đổi chữ, và file `MainWindow.xaml.cs` không gán chuỗi cho control.

### Buổi 3 — Danh sách hộp

Mục tiêu: `ListView` hiện id và thể tích.

`MainViewModel` có `ObservableCollection<BoxRow>`. `BoxRow` là một kiểu nhỏ của tầng UI: `Id` và `Volume`, vì `ListView` không cần sáu tọa độ.

Khi cửa sổ mở, load [boxes.json](../exercises/tuan-02/boxes.json). Copy file vào thư mục chạy hoặc trỏ đường dẫn tuyệt đối lúc dev. Bốn dòng hiện ra: A, B, C, D.

Xong khi: sửa một số trong JSON, chạy lại, thể tích trên cửa sổ đổi theo. Core không bị sửa để chiều UI.

### Buổi 4 — Nút kiểm tra

Mục tiêu: bấm nút thì bảng dưới hiện cặp.

ViewModel giữ `ObservableCollection<ClashPair>` hoặc một chuỗi đã format `A B`. Nút gọi `BoxQueries.FindPairs` trên các `Box` gốc, không gọi trên `BoxRow`.

Với file mẫu, bảng có `A B` và `A D`.

Xong khi: bạn xóa hộp D khỏi file, chạy lại, chỉ còn một cặp. Công thức `<=` không xuất hiện trong project WPF.

### Buổi 5 — Tách và nói được luồng

Mục tiêu: nhìn sơ đồ là chỉ được file nào biết Revit sau này sẽ không biết.

Vẽ trên giấy hoặc trong `notes/tuan-03.md` ba khối: file JSON, Core, cửa sổ. Mũi tên chỉ một chiều từ cửa sổ vào Core.

Đọc lại code-behind. Nếu còn logic, chuyển vào ViewModel. Cửa sổ chỉ có `DataContext`.

Xong khi: bạn nói được câu này mà không nhìn ghi chú: "đổi quy ước chạm mặt thì chỉ sửa Core và test Core, không sửa XAML".

## Bài học

WPF vẽ giao diện từ XAML. Binding nối thuộc tính của ViewModel sang thuộc tính của control. Control không giữ danh sách hộp. ViewModel giữ.

```xml
<ListView ItemsSource="{Binding Rows}">
  <ListView.View>
    <GridView>
      <GridViewColumn Header="Id" DisplayMemberBinding="{Binding Id}" />
      <GridViewColumn Header="The tich" DisplayMemberBinding="{Binding Volume}" />
    </GridView>
  </ListView.View>
</ListView>
```

`ObservableCollection` báo cho `ListView` khi thêm hoặc xóa phần tử. List thường không báo, nên gán list mới thì màn hình đứng im.

Code-behind của cửa sổ chỉ làm một việc:

```csharp
public MainWindow()
{
    InitializeComponent();
    DataContext = new MainViewModel();
}
```

Đây là mức MVVM đủ dùng cho một cửa sổ. Chưa cần framework, chưa cần DI.

## Bài tập nộp

- project `src/Week03.App` trong solution
- cửa sổ load file mẫu và hiện cặp `A B`, `A D`
- `MainWindow.xaml.cs` không chứa `Intersects`, `FindPairs`, hay đường dẫn parse JSON ngoài một lần gọi ViewModel
- `notes/tuan-03.md` có sơ đồ ba khối
- `dotnet test` của Core vẫn xanh

## Tự chấm

| Mục | Đạt |
|---|---|
| Bấm nút ra đúng hai cặp của file mẫu | |
| Core không có `using` WPF | |
| Sửa JSON là cửa sổ đổi, không sửa XAML | |
| Nói được vì sao UI không chứa `<=` | |

Revit chưa cài trong tuần này.
