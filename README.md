# ASP.NET Core MVC

*Tài liệu được thực hiện trong Visual Studio 2022 cùng với phiên bản .NET 7.0*

## Controller
Trong ASP.NET Core MVC, Controller được dùng để truy vấn, xử lý và truyền dữ liệu cho Model hoặc View.

Controller là các lớp dẫn xuất từ lớp `Microsoft.AspNetCore.Mvc.Controller` chứa các phương thức gọi là các **Action** hay **Action method**. Các Controller được tạo trong thư mục **Controllers**.
Khi một project được tạo ra, một Controller mặc định được tạo ra với tên là `HomeController` với nội dung như sau:
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
Lớp `HomeController` chứa nhiều thành viên, nhưng ở mức độ cơ bản ta sẽ quan tâm đến 2 Action: `Index` và `Privacy`. Ngoài ra phương thức `Error` cũng là một Action, nhưng hiện tại ta không cần quan tâm đến.

Các Action thường là trả về kiểu `IActionResult`, nhưng vẫn có thể trả về các kiểu dữ liệu khác như `string`, `int`, `double`, ...

Các Action sử dụng phương thức `View()` để chuyển đến trang giao diện `*.cshtml` tương ứng. Mặc định, nếu không có tham số thì phương thức `View()` sẽ đến thư mục `Views/[controller-name]` và tìm trang View **trùng tên** với Action (`[action-name].cshtml`).

***Ví dụ***
	
 	Action `Index` trong `HomeController` sẽ trả về trang `Views/Home/Index.cshtml`.
 	
  	Action `Privacy` trong `HomeController` sẽ trả về trang `Views/Home/Privacy.cshtml`.

