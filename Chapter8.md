# ASP.NET Core MVC - Chapter 8: ASP Routing

ASP.NET Routing là một tính năng quan trọng trong ứng dụng ASP.NET Core. Nó đảm nhiệm việc ánh xạ các yêu cầu (request) đến đúng Action cụ thể để thực hiện xử lý. 

Nếu sử dụng ASP.NET Routing, đường dẫn (URL) của ứng dụng web sẽ trở nên thân thiện với người dùng hơn. URL sẽ được cấu hình lại để:

* Không gọi trực tiếp đến một file xử lý như các framework lập trình web cổ điển.
		
**Ví dụ:** Các ứng dụng web sử dụng **ASP.NET WebForm** cũ sẽ có URL như sau:
```
    http://daotao1.stu.edu.vn/Default.aspx?page=gioithieu
```
* Không còn các tham số và chuỗi truy vấn (Query string) phức tạp và mang tính kỹ thuật trên đường dẫn. Các tham số sẽ được biểu diễn đơn giản hơn.

Có 2 cách routing phổ biến:
* Convention-based Routing hay Conventional Route.
* Sử dụng attribute `[Route]`.

Vì nội dung ASP.NET Routing không cần thao tác với Controller, View hay Model, do đó hãy tạo một dự án ASP.NET Core mới để đơn giản hóa quá trình thực hành tìm hiểu.

## Conventional Route

### Default Route

Khi một dự án ASP.NET Core được tạo ra, mặc định một Route sẽ được đăng ký và được định nghĩa trong `Program.cs`:
```csharp
    app.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
```
* Nội dung của Route mặc định được đăng ký như sau:
    * ***Tên Route*** (Route name): là tên đại diện cho Route. Một số phương thức trong ASP.NET Core khi sử dụng cần phải chỉ định thông tin này. Mặc định là `default`.
    * ***Định dạng / bản mẫu URL*** (URL Pattern): chỉ định yêu cầu gửi đến (Incoming Request) sẽ có định dạng như thế nào để được Route xử lý. Mặc định đường dẫn sẽ có dạng `{controller}/{action}/{id}`.

* Với Route mặc định, mọi yêu cầu được gửi đến phải có định dạng đã đăng ký. Bên cạnh đó, các giá trị mặc định cũng được khai báo ngay trong URL:
    * `{controller=Home}`: Nếu không có thông tin về Controller, tự động gọi đến `HomeController`. Báo lỗi nếu không tìm thấy.
    * `{action=Index}`: Nếu thông tin về Action được gọi không có, gọi đến Action `Index` của Controller hiện tại – tức là giá trị của `controller`. Báo lỗi nếu không tìm thấy.
    * `{id?}` là một tham số tùy chọn (Optional Parameter), tức là có thể không cần chỉ định trên URL. Tham số này là dạng cấu hình lại của chuỗi truy vấn `?id=value` trong cách biểu diễn truyền thống, đồng nghĩa rằng cả 2 đường dẫn bên dưới đều là như nhau:

```
    /Home/Show/5
    /Home/Show?id=5
```

Với đường dẫn `/Home/Show/5`, ta hoàn toàn có thể định nghĩa Action giống như đang làm việc với đường dẫn có sử dụng chuỗi truy vấn `/Home/Show?id=5`:
```csharp
    public IActionResult Show(int id) {
        // code here ...
    }
```

Tuy nhiên, do tham số `{id?}` không có ràng buộc về kiểu dữ liệu, nên các đường dẫn bên dưới đều xem là hợp lệ:
```
    /Home/Show/2
    /Home/Show/abc
```

Ta sẽ tìm hiểu về các vấn đề ràng buộc trên tham số ở phần [Route Contraints]().

Ngoài ta, ASP.NET Core cung cấp phương thức `MapDefaultControllerRoute()` để thay thế cho Route mặc định.
```csharp
    app.MapDefaultControllerRoute();

    // có thể thay thế cho
    app.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
```



