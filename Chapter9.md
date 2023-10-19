# ASP.NET Core MVC - Chapter 9: Razor Layout page

## Layout page

**Layout page** là một trang giao diện cung cấp các thành phần dùng chung giữa các trang View. Ở đa phần các trang web hiện nay, ngoài các nội dung riêng của từng trang thì luôn chứa các nội dung chung không thay đổi như Header, Navigation Bar (Navbar), Footer, ...
Nếu mỗi trang đều chỉ định các thành phần chung đó thì sẽ trở nên dài dòng và khó sửa chửa, bảo trì (muốn sửa phải sửa từng trang).

Mặc định một Razor Layout Page được tạo ở thư mục `Views/Shared` là `_Layout.cshtml`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - TestMiddleware</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
    <link rel="stylesheet" href="~/TestMiddleware.styles.css" asp-append-version="true" />
</head>
<body>
    <header>
        <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
            <div class="container-fluid">
                <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestMiddleware</a>
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
            &copy; 2023 - TestMiddleware - <a asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
        </div>
    </footer>
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
    <script src="~/js/site.js" asp-append-version="true"></script>
    @await RenderSectionAsync("Scripts", required: false)
</body>
</html>
```

Về cơ bản, Razor Layout Page là một trang Razor View như các trang View khác, có thể sử dụng các cú pháp Razor. Thông thường các Razor Layout Page được tạo ở thư mục `Shared` như một đối tượng dùng chung.

## Thêm Layout page mới

Ta sẽ bỏ qua trang `_Layout.cshtml` mặc định và thực hành trên một Layout mới.

* Để tạo một Layout mới, right-click thư mục **Shared > Add > New Item...**.

* Chọn vào **Razor Layout** và đặt tên mới cho trang Layout.

* Các trang Layout nên đặt tên với ký tự `_` (Underscore) phía trước (không bắt buộc).

Nội dung được tạo ra cho trang Layout mới như sau:

```html
    <!DOCTYPE html>

    <html>
        <head>
            <meta name="viewport" content="width=device-width" />
            <title>@ViewBag.Title</title>
        </head>
        <body>
            <div>
                @RenderBody()
            </div>
        </body>
    </html>
```

Trong đó:
* Giá trị của `ViewBag` được lấy từ Content page (trang chứa nội dung chính). Ngoài `ViewBag`, ta hoàn toàn có thể sử dụng `ViewData`.

* Phương thức `RenderBody()` dùng để nhúng nội dung của Content page vào vị trí chỉ định để hoàn chỉnh phần giao diện.

Để đơn giản, hãy thay đổi nội dung của thẻ `<body>` như sau:

```html
    <h1> Header </h1>
    <hr />
    @RenderBody()
    <hr />
    <h2> Footer </h2>
```

## Sử dụng Layout

Để chỉ định một Layout page sẽ sử dụng, ta sử dụng thuộc tính `Layout` của trang Razor ở Content page.

```cs
    @{
        Layout = "_MainLayout";
    }
```

Thuộc tính `Layout` có thể được chỉ định bằng đường dẫn đầy đủ như `/Views/Shared/_MainLayout.cshtml` 
hoặc chỉ cần tên của trang Layout như `_MainLayout`.

Khi chỉ định bằng tên, **Discovery process** (Tiến trình tìm kiếm) sẽ thực hiện tìm trong thư mục `/Views/[controller]`, và tiếp theo là `/Views/Shared`.

Vì trang Layout đã cung cấp phần giao diện chung, nên các trang View chỉ cần cung cấp phần nội dung mà không cần có đầy đủ 
cấu trúc của tài liệu HTML gồm các thẻ như `<!DOCTYPE html>`, `<html>`, ...

Các đối tượng của `ViewData` và `ViewBag` của trang View có thể truyền cho Layout page.

**Ví dụ:**

```html
    @{
        ViewBag.Title = "Index"; // gửi cho Layout page
        Layout = "_MainLayout"; // sử dụng _MainLayout.cshtml
    }
    <p> Index content </p>
```
Nội dung hiển thị trên trang web sẽ là tổng hợp của Layout page và Content page:

```html
    <!DOCTYPE html>

    <html>
        <head>
            <meta name="viewport" content="width=device-width" />
            <title>Index</title> <!-- Giá trị từ ViewBag.Title -->
        </head>
        <body>
            <div>
                <h1> Header </h1>
                <hr />
                <p> Index content </p>
                <hr />
                <h2> Footer </h2>
            </div>
        </body>
    </html>
```

**Lưu ý:**
* Nếu không chỉ định thuộc tính `Layout`, Content page sẽ được sử dụng Layout page mặc định được khai báo trong `_ViewStart.cshtml`.

* Nếu không muốn sử dụng Layout page, hãy chỉ định `Layout = null`.

## Render Section

Một Layout page có thể chứa một hoặc nhiều section, trong đó section được khai báo trong Layout page
sẽ yêu cầu giữ một vị trí chứa nội dung có thể thay thế được (Replacable content). 

Đơn giản mà nói, khi đặt section vào một vị trí nào đó thì Layout page sẽ mong đợi Content page cung cấp phần nội dung ở vị trí section đó, 
có thể khác nhau giữa Content page sử dụng.

Để đặt một section, ta sử dụng phương thức `RenderSection()` ở **Layout page** với cú pháp:

```cs
    RenderSection(string name, bool required)
```

* Tham số `name` là tên của section, là định danh tùy ý sẽ sử dụng lại để xác định ở Content page.

* Tham số `required` quy định tính bắt buộc trang View phải cung cấp phần nội dung cho section tương ứng với giá trị `true`, 
ngược lại nếu không bắt buộc là `false`.

**Ví dụ:** `_MainLayout.cshtml`:

```html
    <!DOCTYPE html>

    <html>
        <head>
            <meta name="viewport" content="width=device-width" />
            <title>Index</title> <!-- Giá trị từ ViewBag.Title -->
        </head>
        <body>
            <div>
                <h1> Header </h1>
                <hr />
                @RenderBody()
                <hr />
                <h2> Footer </h2>
            </div>
            <div>
                @RenderSection("scripts", false)
            </div>
        </body>
    </html>
```

Để chỉ định nội dung cho section, ở Content page sẽ sử dụng cú pháp sau:

```cs
    @section section_name
    {
        // nội dung sẽ nhúng vào vị trí khai báo section ...
    }
```

**Ví dụ:**

```cs
    @section scripts
    {
        <script type="text/javascript" src="~/scripts/main.js"></script>
    }
```

Nếu các section bắt buộc (Require section) không tìm thấy nội dung từ Content page, một ngoại lệ sẽ ném ra.

**Xem thêm:** **[RenderSectionAsync()](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.razor.razorpage.rendersectionasync?view=aspnetcore-7.0)** để thêm các nội dung bất đồng bộ.
