# ASP.NET Core MVC - Chapter 7: Route Values

**Route Values** hay **Route Data** trong ASP.NET Core MVC là một đối tượng chứa các tham số của Route (ở mức độ hiện tại, ta có thể hiểu Route là đường dẫn).

Route Values thường là một trong các tham số tùy chọn trong các phương thức liên quan đến đường dẫn và chuyển hướng như `Url.ActionLink()`, `Html.ActionLink()`, `RedirectToAction()`, 
... với tên là `routeValues`. Một số phương thức dùng tên tham số là `routeData`.

Tham số `routeValues` thường được truyền dưới dạng là một đối tượng ẩn danh (Anonymous Object) bao gồm các thuộc tính đại diện cho các tham số trên đường dẫn.

**Ví dụ:** 

* Để biểu diễn đường dẫn `~/Home/Show?name=Bod&age=15`, ta có thể dùng `<a>` của HTML với `href` hoặc **`asp-route-{param}`** của Tag Helper:
```html
    <a href="~/Home/ShowMessage?msg=Hello"> Message </a>
    <a asp-action="ShowMessage" asp-route-msg="Hello"> Message </a>
```
* Nhưng trong trường hợp đang sử dụng các phương thức đường dẫn và chuyển hướng được liệt kê bên trên, ta có thể chỉ định cho tham số `routeValues`.

```html
    @Html.ActionLink("Message", "ShowMessage", "Home", new { msg = "Hello" })

    <!-- Nội dung HTML nhận được -->
    <a href="~/Home/ShowMessage?msg=Hello"> Message </a>
```
Mặc dù, việc sử dụng phương thức `Html.ActionLink()` và cả `Url.ActionLink()` đã không còn phổ biến trong ASP.NET Core MVC với sự xuất hiện của Tag Helper, 
nhưng vẫn còn một số phương thức khác thường dùng đôi khi cần phải chỉ định `routeValues`, điển hình là `RedirectToAction()`.

Khi chuyển hướng đến một action với `RedirectToAction()`, nhưng action đó cần các tham số dữ liệu thì ta có thể khai báo các giá trị Route cho nó.

**Ví dụ:**
```csharp
    public IActionResult Index()
    {
        return RedirecToAction("ShowMessage", new { msg = "Hello" });
    }
    public IActionResult ShowMessage(string msg)
    {
        return Content(msg);
    }
```
Tuy nhận một đối tượng ẩn danh, nhưng vẫn có một số tên thuộc tính đặc trưng dành cho ASP.NET Core. Điển hình là `controller` và `action`.

**Ví dụ:**
```csharp
    @Html.BeginRouteForm(new { controller = "Home", action = "Login" })
```
Phương thức `Html.BeginRouteForm()` hiện đã không còn sử dụng phổ biến trong ASP.NET Core.

Ngoài đối tượng ẩn danh `new { }`, các `routeValues` còn có thể nhận dữ liệu với kiểu dẫn xuất của `IDictionary<TKey, TValue>`, thường dùng nhất là `Dictionary<TKey, TValue>` và `RouteValueDictionary`.

**Ví dụ:**
```csharp
    @Html.ActionLink("Result", "Sum", "Home", 
        new Dictionary<string, object>() {
            { "a", 157 },
            { "b", 45 }
        }
    )
    <!-- URL: ~/Home/Sum?a=157&b=45 -->
```
