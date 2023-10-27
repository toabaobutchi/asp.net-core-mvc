# ASP.NET Core MVC - Chapter 12: Area

Area là một chức năng của ASP.NET dùng để tổ chức các chức năng có liên quan với nhau thành một nhóm riêng biệt. Mỗi Area đều có các Controller, View và Model riêng, tách biệt với các thành phần khác.

## Thêm Area cho dự án ASP.NET Core

* Để tạo một Area, right-click vào **tên project > Add > New Scaffolded Item...**.

* Chọn vào **MVC Area**.

* Đặt tên cho Area sau đó chọn **Add**.

* Thư mục `Areas` được tạo ra với cấu trúc thư mục như sau:

```
    .
    └── Areas
        └── [Area name]
                ├── Controllers
                ├── Data
                ├── Models
                └── Views
```

Các thư mục trong Area vừa tạo đều có đặc điểm tương tự các thư mục cùng tên bên ngoài cấu trúc MVC.

## Area Routing

Điều đầu tiên sau khi tạo một Area mới là khai báo các Route định tuyến cho Area và các Controller trong Area đó.

Đối với Area, ta thường dùng Conventional Route hơn là `[Route]` atrribute.

**Ví dụ:**

```cs
    // Khai báo Route cho Area 'Admin'
    app.MapControllerRoute(
        name: "Admin",
        pattern: "Admin/{controller=Home}/{action=Index}/{id?}",
        defaults: new { area = "Admin" }
    );
    app.MapDefaultControllerRoute(); // Route mặc định
```

Ngoài phương thức `MapControllerRoute()`, ta có thể sử dụng phương thức `MapAreaControllerRoute()` với cú pháp:

```cs
    MapAreaControllerRoute(name, areaName, pattern, defaults, constraints, dataTokens)
```

Muốn sử dụng phương thức `MapAreaControllerRoute()` ta cần chỉ định thuộc tính `[Area]` ở mức Controller với cú pháp:

```cs
    [Area(string areaName)]
```
Trong đó, tham số `areaName` sẽ trùng với tham số `areaName` của phương thức `MapAreaControllerRoute()`.

**Ví dụ:**

* Conventional Route:

```cs
    app.MapAreaControllerRoute(
        name: "AdminRoute",
        areaName: "Admin",
        pattern: "Admin/{controller=Home}/{action=Index}/{id?}"
    );
```

* Chỉ định attribute `[Area]` cho Controller:

```cs
    [Area("Admin")]
    public class HomeController : Controller
    {
        // action methods
    }
```

Nếu Controller có chỉ định thuộc tính `[Area]` thì ta có thể sử dụng phương thức `MapControllerRoute()` với mẫu `{area}` cùng với ràng buộc `exists`.

**Ví dụ:**

```cs
    app.MapControllerRoute(
        name: "Admin",
        pattern: "{area:exists}/{controller=Home}/{action=Index}/{id?}"
    );
```

Mẫu `{area:exists}` kiểm tra xem có tồn tại tên Area trên đường dẫn hay không. Nếu không thì bỏ qua Route hiện tại và di chuyển đến Route tiếp theo.

## Area Tag Helper

Tag Helper `asp-area` được dùng để chỉ định thông tin về Area, thường dùng cho các ngữ cảnh cần đường dẫn như `<a>`, thẻ `<form>`, ... Tag Helper này thường được sử dụng cùng với `asp-controller`, `asp-action`, ...

**Ví dụ:**

```html
    <a asp-action="Privacy" asp-controller="Home" asp-area="Admin"> Privacy</a>
    // mã HTML sẽ được tạo như sau - giả sử URL pattern là "{area}/{controller}/{action}"
    <a href="/Admin/Home/Privacy"> Privacy </a>
```

