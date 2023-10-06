# ASP.NET Core MVC - Chapter 2 - Chuyển hướng trong ASP.NET Core MVC

## Chuyển hướng phía người dùng
### Sử dụng thẻ \<a>

Để chuyển hướng phía người dùng, một cách đơn giản nhất để chuyển hướng là sử dụng siêu liên kết – thẻ `<a>` trong HTML.
Vấn đề ở đây là giá trị cho thuộc tính `href`. Các đường dẫn trên trang web của ASP.NET Core phải gọi được đúng đến action của trang cần chuyển hướng.

Theo URL pattern mặc định được định nghĩa trong `Program.cs`, đường dẫn sẽ có định dạng: `~/{controller}/{action}/{id}`. 

Ở [Chapter 9: ASP.NET Routing](), ta sẽ tìm hiểu cách cấu hình lại định dạng mặc định dạng đường dẫn theo nhu cầu cá nhân. Hiện tại ta vẫn sử dụng định dạng đường dẫn mặc định.

Ta sẽ không xem xét về tham số `{id}` trong nội dung tài liệu này.

Ký tự `~` thể hiện đường dẫn gốc (Root directory) của ứng dụng web, hạn chế việc chỉ định đường dẫn tuyệt đối.


**Ví dụ:**

```html
    <a href="~/Web/Welcome"> Welcome page </a>
    <a href="~/Home/Privacy"> Privacy </a>
```
Đối với thẻ `<a>`, ASP.NET Core cho phép sử dụng các [Anchor Tag Helper](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/tag-helpers/built-in/anchor-tag-helper?view=aspnetcore-7.0), là các thuộc tính với tiền tố **`asp-`**. Trong đó, 2 thuộc tính phổ biến cho thẻ `<a>` là **`asp-controller`** và **`asp-action`**.

**Ví dụ:**

```html
    <a asp-controller="Web" asp-action="Index"> Web home page </a>
    <!-- biểu diễn tương tự -->
    <a href="~/Web/Index"> Web home page </a>
```
2 thuộc tính **`asp-controller`** và **`asp-action`** dùng để chỉ định tên action và controller nào sẽ được chuyển hướng đến và không bị phụ thuộc vào định dạng URL pattern, giảm thiểu các lỗi đường dẫn khi thực hiện các thay đổi về Routing.

Nếu không muốn sử dụng Anchor Tag Helper, ta có thể dùng phương thức `Url.ActionLink()` với cú pháp:

```csharp
    Url.ActionLink(string? action, string? controller)
```
**Lưu ý**: Cú pháp được giới thiệu bên trên chỉ là một phần của cú pháp đầy đủ với các tham số tùy chọn. Xem chi tiết về phương thức `Url.ActionLink()` tại: [UrlHelperExtensions.ActionLink](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.urlhelperextensions.actionlink?view=aspnetcore-7.0).

**Ví dụ:**
```html
    <a href="@Url.ActionLink("Privacy", "Home")"> Privacy </a>
    <a href="@Url.ActionLink("Welcome", "Web")"> Welcome page </a>
```
[Cú pháp Razor]() cho phép ta nhúng mã C# vào trong mã HTML và bắt đầu bằng ký tự `@`.

### Sử dụng phương thức Html.ActionLink()
Khác với phương thức `Url.ActionLink()` chỉ trả về đường dẫn cho thuộc tính `href`, phương thức `Html.ActionLink()` sẽ tạo ra cả một thẻ `<a>`.

Cú pháp đơn giản nhất của phương thức `Html.ActionLink()` bao gồm các tham số về văn bản hiển thị và thông tin về action và controller được chuyển hướng đến:

```csharp
    Html.ActionLink(string linkText, string action, string controller)
```
**Ví dụ:**
```csharp, html
    @Html.ActionLink("Privacy page", "Privacy", "Home")
    <! -- tương tự -->
    <a href="~/Home/Privacy"> Privacy page </a>
```
Một cú pháp khác của phương thức `Html.ActionLink()` cho phép chỉ định các thuộc tính HTML như `class`, `style`, `id`, `title`, ...
```csharp
    Html.ActionLink(linkText, action, controller, object routeValues, object htmlAttributes)
```
Sẽ có một tài liệu đề cập chi tiết về cách truyền giá trị cho tham số `routeValues`, vì vậy hiện tại sẽ sử dụng giá trị `null` cho tham số này.

Tham số `htmlAttributes` thường nhận một *đối tượng ẩn danh* (Anonymous Object) chứa các thuộc tính trùng tên với thuộc tính HTML.

**Ví dụ:**
```csharp, html
    @Html.ActionLink("Home page", "Index", "Home", null,
        new { 
            @class = "btn btn-success", 
            title = "Go to Home page"
        }
    )
    <! -- tương tự -->
    <a href="~/Home/Index" class="btn btn-success" title="Go to Home page"> Home page </a>
```
**Lưu ý:** Thuộc tính HTML `class` sẽ biểu diễn thành `@class` để tránh xung đột với từ khóa `class` trong C#.
`Html.ActionLink()` là một trong các phương thức của `IHtmlHelper`, thứ thường được dùng trong ASP.NET MVC (.NET Framework).

## Chuyển hướng phía máy chủ
Một số các hành vi chuyển hướng phải được thực hiện nội bộ và chủ động từ phía máy chủ tùy vào từng hành vi của người dùng.

**Ví dụ:**

> Để vào trang Thông tin khách hàng, người dùng sẽ ấn vào liên kết đến trang. Tuy nhiên nếu khách hàng chưa đăng nhập thì sẽ phải chuyển đến trang Đăng nhập/Đăng ký.
>
> Ngược lại sẽ hiển thị trang Thông tin khách hàng. Việc chuyển hướng có điều kiện như vậy không thể hiện thực nếu chỉ sử dụng cách chuyển hướng ở phía người dùng với siêu liên kết, mà phải được xử lý ở máy chủ.

Để chuyển hướng ở máy chủ, ta sử dụng một trong 2 phương thức sau:

* **`RedirectResult Redirect(string url)`**: chuyển hướng theo `url` chỉ định, có thể là trang web bên ngoài.
* **`RedirectToActionResult RedirectToAction(string action, string controller)`**: chuyển hướng xử lý đến `action`. Bỏ qua tham số `controller` nếu như action hiện tại và action cần chuyển hướng có cùng controller.

Xem thêm các cú pháp khác của phương thức `RedirectToAction()` tại [đây](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.redirecttoaction?view=aspnetcore-7.0).

**Ví dụ:**
```csharp
    public IActionResult Index() {
	    return View();
    }
    public IActionResult GoToIndex() {
	    return RedirectToAction("Index"); // chuyển hướng đến Action 'Index'
    }
```
Kiểu trả về `RedirectResult` và `RedirectToActionResult` đều là các kiểu dẫn xuất của `IActionResult`.
