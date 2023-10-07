# ASP.NET Core MVC - Chapter 5: Phương thức View()

Trong ASP.NET Core MVC, phương thức `View()` được dùng với 2 mục đích chính:

* Tìm trang View tương ứng với action đang gọi.
* Truyền dữ liệu kiểu *Viewmodel* (đã trình bày ở tài liệu [Chapter 4](/Chapter4.md)).

Phương thức `View()` trả về kiểu `ViewResult` – một kiểu dẫn xuất của `IActionResult`. Các phương thức Action có thể trả về trực tiếp kiểu `ViewResult`, nhưng điều đó thường không phổ biến.

## View discovery
View discovery (tạm dịch: Trình tìm kiếm View) là một tiến trình được thực hiện khi một Action trả về trang View. Tiến trình này sẽ có nhiệm vụ đi tìm đến trang View dựa vào tên của View.

Mặc định, phương thức View() với lệnh return View(); sẽ trả về trang View trùng tên với Action đang gọi. Thứ tự tìm kiếm trang View sẽ như sau:

1. `Views/[controller]/[view].cshtml`
2. `Views/Shared/[view].cshtml`
 
Các trang view có thể trùng tên nhau, miễn là chúng không cùng controller.

Cũng vì hành vi mặc định của `View()`, ta thường được khuyến nghị đặt tên các trang view trùng tên với action.

Tuy nhiên, ta cũng có thể chỉ định tên của trang view cần trả về với cú pháp sau:

```csharp
  View(string? viewName)
```
Do đó, nếu vì một lý do nào đó mà trang view không trùng tên với action hoặc muốn chuyển tiếp đến một trang view khác, ta có thể sử dụng cú pháp trên.

**Ví dụ:**
```csharp
	public IActionResult Index()
	{
		return View("About");
	}
```
Nếu muốn chuyển đến trang View khác thư mục với controller hiện tại, ta có thể sử dụng đường dẫn tương đối đến trang View đó. Lúc này, phần mở rộng `.cshtml` phải được chỉ định.

**Ví dụ:**
```csharp
public class HomeController : Controller
 {
  public IActionResult Index()
  {
   return View("~/Views/Web/About.cshtml");
  }
 }
```
