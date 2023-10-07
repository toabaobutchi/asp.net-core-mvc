# ASP.NET Core MVC - Chapter 1: Controller và View

*Tài liệu được thực hiện trong Visual Studio 2022 cùng với phiên bản .NET 7.0*

## Controller
Trong ASP.NET Core MVC, Controller được dùng để truy vấn, xử lý và truyền dữ liệu cho Model hoặc View.

Controller là các lớp dẫn xuất từ lớp `Microsoft.AspNetCore.Mvc.Controller` chứa các phương thức gọi là các **Action** hay **Action method**. Các controller được tạo trong thư mục **Controllers**.


Khi một project được tạo ra, một controller mặc định được tạo ra với tên là `HomeController` với nội dung như sau:
```csharp
public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;

    public HomeController(ILogger<HomeController> logger)
    {
        _logger = logger;
    }

    public IActionResult Index()
    {
        return View();
    }

    public IActionResult Privacy()
    {
        return View();
    }

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
    public IActionResult Error()
    {
        return View(new ErrorViewModel { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
    }
}
```
Lớp `HomeController` chứa nhiều thành viên, nhưng ở mức độ cơ bản ta sẽ quan tâm đến 2 action: `Index` và `Privacy`. Ngoài ra phương thức `Error` cũng là một action, nhưng hiện tại ta không cần quan tâm đến.

Các action thường là trả về kiểu `IActionResult`, nhưng vẫn có thể trả về các kiểu dữ liệu khác như `string`, `int`, `double`, ...

Các action sử dụng phương thức `View()` để chuyển đến trang giao diện `*.cshtml` tương ứng. Mặc định, nếu không có tham số thì phương thức `View()` sẽ đến thư mục `Views/[controller-name]` và tìm trang View **trùng tên** với action (`[action-name].cshtml`).

**Ví dụ:**

* Action `Index` của `HomeController` sẽ trỏ về trang `~/Views/Home/Index.cshtml`.
* Action `Privacy` của `HomeController` sẽ trỏ về trang `~/Views/Home/Privacy.cshtml`.

Tóm lại, tất cả các phương thức `public` trong lớp controller đều là các action - trừ một số trường hợp cấu hình lại.

## Thêm Controller mới
Các controller trong ASP.NET Core MVC đều được tạo trong thư mục **Controllers**.

Để tạo controller mới trong Visual Studio 2022, ta sẽ:

* Right-click vào thư mục **Controllers** > **Add** > **Controller...**
* Chọn vào **MVC Controller - Empty**.
* Đặt tên cho controller. **Lưu ý**: Tên controller cần phải giữ lại phần hậu tố `[...]Controller`.

Khi một controller mới được tạo ra, action có tên `Index` cũng được tạo ra theo đó.

Như đã đề cập, các action thường trả về kiểu `IActionResult`, nhưng cũng có thể trả về kiểu dữ liệu khác.

**Ví dụ:**
```csharp
    public class TestController : Controller 
    {
        public string Index() 
        {
            return "Hello, World!";
        }
    }
```
Chọn **F5** hoặc **Ctrl + F5** để chạy project, sau đó thay đổi đường dẫn thành `https://localhost:[port]/[controller-name]/Index`. Cụ thể, ở ví dụ trên, ta sẽ thay đường dẫn thành `~/Test/Index`.

Vấn đề về đường dẫn trong ASP.NET Core MVC sẽ được đề cập ở phần sau.

## Thêm thành phần View
Mỗi action của các controller thường sẽ trả về tương ứng với một trang Razor View (`*.cshtml`).

Biểu diễn một luồng đơn giản khi nhận 1 request: `Request --> Controller --> Action --> View`

Thay đổi nội dung của action `Index` ở phần trên như bên dưới:
```csharp
    public IActionResult Index()
    {
        return View();
    }
```
Khi vừa được tạo ra, action sẽ không có view tương ứng. 

Các bước tạo view cho 1 action:
* Right-click vào tên của action và chọn **Add View...**
* Chọn **Razor View - Empty**.
* Đặt tên cho view. **Lưu ý**: Tên của view ***nên*** trùng với tên của action.

Nếu controller có ít nhất 1 action được tạo view, một thư mục với tên `[controller-name]` sẽ tự động tạo trong thư mục **Views**. Thư mục `[controller-name]` sẽ toàn bộ trang view của các action trong nó.

Thêm một số mã HTML vào trang view vừa tạo, sau đó truy cập vào action, ta sẽ thấy được kết quả của trang view. Các trang view mặc định sẽ dùng phần header, footer và navbar được tạo sẵn với nội dung có thể tìm thấy ở file `~/Views/Shared/_Layout.cshtml`.

`_Layout.cshtml` có nội dung như sau:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - EFCoreServiceRegister</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
    <link rel="stylesheet" href="~/EFCoreServiceRegister.styles.css" asp-append-version="true" />
</head>
<body>
    <header>
        <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
            <div class="container-fluid">
                <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">EFCoreServiceRegister</a>
                <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target=".navbar-collapse" aria-controls="navbarSupportedContent"
                        aria-expanded="false" aria-label="Toggle navigation">
                    <span class="navbar-toggler-icon"></span>
                </button>
                <div class="navbar-collapse collapse d-sm-inline-flex justify-content-between">
                    <ul class="navbar-nav flex-grow-1">
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                        </li>
                        <li class="nav-item">
                            <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                        </li>
                    </ul>
                </div>
            </div>
        </nav>
    </header>
    <div class="container">
        <main role="main" class="pb-3">
            @RenderBody()
        </main>
    </div>

    <footer class="border-top footer text-muted">
        <div class="container">
            &copy; 2023 - EFCoreServiceRegister - <a asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
        </div>
    </footer>
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
    @await RenderSectionAsync("Scripts", required: false)
</body>
</html>

```

