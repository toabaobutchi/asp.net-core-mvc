# ASP.NET Core MVC - Chapter 10: Partial View

## Partial view

Partial view trong ASP.NET Core là một trang Razor `*.cshtml` chứa nội dung có thể tái sử dụng ở nhiều trang View khác nhau.

Về cơ bản, Partial view chỉ chứa nội dung mà không phải toàn bộ cấu trúc HTML. Phần nội dung đó sẽ được trang View gọi đến và nhúng vào vị trí chỉ định.

Các ứng dụng của Partial view:

* Chia các nội dung lớn thành các thành phần nhỏ hơn.

* Giúp nội dung trong trang View chính trở nên dễ quản lý hơn, tập trung vào nội dung chính.

* Hạn chế các nội dung trùng lặp ở các trang View. Partial không nên được sử dụng như một giao diện chung cho các trang. Hãy sử dụng Razor Layout Page thay thế.

## Thêm Partial view

Partial view là thành phần có thể tái sử dụng, vì vậy nên được tạo trong thư mục `Shared`.

* Right-click vào thư mục **Shared > Add > View ...**

* Chọn **Razor View – Empty** và đặt tên cho file.

Sau khi tạo view, hãy chỉ định một nội dung đơn giản, ví dụ như:

`_pvHeader.cshtml`:

```html
    <h3 style="color:red;"> Content from partial view </h1>
```

## Sử dụng Partial

### Partial Tag Helper

Một cách sử dụng Partial view đơn giản nhất trong ASP.NET Core là sử dụng Partial Tag Helper với cú pháp:

```html
    <partial name="<partial-name>" />
```

**Ví dụ:**

```html
    <partial name="_pvHeader" />
	<p> Body content </p>
```

> [!Note]
> Partial Tag Helper yêu cầu ASP.NET Core 2.1 trở lên.

Nếu có thêm phần mở rộng thì Partial view phải nằm trong thư mục của trang gọi đến.

**Ví dụ:**

```html
    <partial name="_pvHeader.cshtml" />
```

Ta cũng có thể chỉ định Partial view bằng đường dẫn bắt đầu từ đường dẫn gốc với `~/` hoặc `/`.

**Ví dụ:**

```html
    <partial name="~/Views/Shared/_pvHeader.cshtml" />
```

Partial Tag Helper sẽ mặc định nhúng bất đồng bộ nội dung vào trang View đang gọi.

### Asynchronous HTML Helper

Ngoài Partial Tag Helper, ta có thể sử dụng phương thức `Html.PartialAsync()` của `IHtmlHelper`. Vì là phương thức bất đồng bộ nên phải đặt từ khóa `await` trước khi gọi phương thức.

**Ví dụ:**

```cs
    @await Html.PartialAsync("_pvHeader");
```

Tương tự Partial Tag Helper, nếu phần mở rộng được chỉ định, Partial view phải đặt trong thư mục của trang gọi đến. Bên cạnh đó, phương thức `Html.PartialAsync()` có thể nhận đường dẫn gốc bắt đầu bằng `~/` hay `/`.

**Ví dụ:**

```cs
    @await Html.PartialAsync("_pvHeader.cshtml");
	@await Html.PartialAsync("~/Views/Shared/_pvFooter.cshtml");
```

Một phương thức khác dùng để sử dụng Partial view là `Html.RenderPartialAsync()`.

> Xem chi tiết về `Html.RenderPartialAsync()` tại: [**HtmlHelperPartialExtensions.RenderPartialAsync Method**](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync?view=aspnetcore-7.0).

### Partial view discovery

Partial view discovery sẽ thực hiện tìm kiếm Partial View nếu như không chỉ định đường dẫn và phần mở rộng. Các thư mục được tìm kiếm theo thứ tự sau:
1. `/Areas/<Area-Name>/Views/<Controller-Name>`
2. `/Areas/<Area-Name>/Views/Shared`
3. `/Views/Shared`

### Truyền dữ liệu sang Partial view

Khi một Partial view được khởi tạo, nó sẽ nhận một bản sao chép của đối tượng `ViewData`, vì vậy mà Partial view có thể sử dụng `ViewBag` và `ViewData` từ trang View gọi đến.

Tuy nhiên vì là bản sao chép, các thay đổi trên đối tượng `ViewData` hay `ViewBag` không thể cập nhật lại ở trang View gốc.

<img src="https://github.com/toabaobutchi/asp.net-core-mvc/assets/147165208/f2d95870-74aa-4e4b-b16d-d852f97dbb68" width="250" />

Ngoài ra ta có thể sử dụng một cách thức tương tự *Viewmodel* để truyền dữ liệu:

* Sử dụng thuộc tính `model` trong Partial Tag Helper:

```html
	<partial name="..." model="<object>" />
```

* Sử dụng phương thức `Html.PartialAsync(string partialName, object model)`.




