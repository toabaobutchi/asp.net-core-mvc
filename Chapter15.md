# Session trong ASP.NET Core MVC

> [!Note]
> Tài liệu không đề cập đến các khái niệm liên quan đến Session trong lập trình Web nói chung

ASP.NET Core duy trì các trạng thái của Session bằng cách cung cấp Cookie cho máy khách (Client) chứa thông tin về Session ID. Cookie chứa Session ID sẽ được kèm theo mỗi yêu cầu (Request) được gửi đến máy chủ và được dùng để xác định dữ liệu mà Session đang lưu trữ.

## Cấu hình Session

Để sử dụng session trong ASP.NET Core, ta cần cài đặt gói `Microsoft.AspNetCore.Session`.

Tuy nhiên, ở **.NET 6** thì gói `Microsoft.AspNetCore.Session` được ngầm định thêm vào trong dự án và cung cấp một Middleware quản lý trạng thái của session. Ta sẽ thực hiện cấu hình sử dụng session trong file `Program.cs` như sau:

```cs
    var builder = WebApplication.CreateBuilder(args);

    builder.Services.AddRazorPages();
    builder.Services.AddControllersWithViews();

    // ================================================================
    builder.Services.AddDistributedMemoryCache();

    builder.Services.AddSession(options =>
    {
        options.IdleTimeout = TimeSpan.FromSeconds(10);
        options.Cookie.HttpOnly = true;
        options.Cookie.IsEssential = true;
    });
    // ================================================================

    var app = builder.Build();

    if (!app.Environment.IsDevelopment())
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthorization();

    app.UseSession(); // khai báo sử dụng Session

    app.MapRazorPages();
    app.MapDefaultControllerRoute();

    app.Run();
```

Ở mức độ hiện tại, ta sẽ chỉ quan tâm đến phương thức `AddSession()`. Phương thức này dùng để kích hoạt và cấu hình session cho ứng dụng. Nếu không truyền tham số, cấu hình mặc định sẽ được sử dụng.
Nhưng nếu muốn cấu hình một số thông tin cho session, ta có thể dùng cú pháp sau:

```cs
    AddSession(Action<SessionOptions> configure)
```

Lớp `SessionOptions` cung cấp 3 thuộc tính để cấu hình lại session như sau:

Thuộc tính | Mô tả
--- | ---
`CookieBuilder Cookie { get; set; }` | Xác định các cài đặt để tạo cookie thông qua các thuộc tính: `Name`, `Path`, `Samesite`, `HttpOnly`, `IsEssential`.
`TimeSpan IdleTimeout { get; set; }` | Thời gian mà session có thể không hoạt động trước khi nội dung bị hủy. Các truy cập vào session sẽ đặt lại thời gian. Chỉ áp dụng với nội dung của session, không phải cookie. Mặc định là 20 phút.
`TimeSpan IOTimeout { get; set; }` | 

> Xem thêm các thuộc tính `Cookie` tại: [**SessionOptions.Cookie Property**](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie?view=aspnetcore-7.0#microsoft-aspnetcore-builder-sessionoptions-cookie)

## Sử dụng Session trong Controller

