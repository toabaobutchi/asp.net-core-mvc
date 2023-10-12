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

Ta sẽ tìm hiểu về các vấn đề ràng buộc trên tham số ở phần [Route Contraints](#route-constraints).

Ngoài ta, ASP.NET Core cung cấp phương thức `MapDefaultControllerRoute()` để thay thế cho Route mặc định.
```csharp
    app.MapDefaultControllerRoute();

    // có thể thay thế cho
    app.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
```

**Lưu ý:** Đường dẫn của yêu cầu gửi đến không phân biệt chữ hoa chữ thường. Ví dụ: `home/index` là đường dẫn hoàn toàn giống với `Home/Index`.

### Tham số defaults

Tham số `defaults` trong phương thức `MapControllerRoute()` được dùng để chỉ định các giá trị mặc định trên URL.

Mặc dù ta có thể chỉ định ngay trên URL các giá trị mặc định, nhưng nếu muốn ta vẫn có thể dùng tham số `defaults` như sau:

```csharp
    app.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}"); // định nghĩa giá trị mặc định ngay trên URL pattern

    // hoặc chỉ định cho tham số 'defaults'
    app.MapControllerRoute(
        name: "default",
        pattern: "{controller}/{action}/{id}",
        defaults: new { controller = "Home", action = "Index", id = 0);
```
Tham số `defaults` thường nhận một đối tượng ẩn danh chứa các thuộc tính và giá trị mặc định cho tham số. Tên thuộc tính phải trùng với tên tham số trên URL cần gán giá trị mặc định.

### Thêm Route mới

Trước khi tạo ra một Route mới, ta sẽ thực hành chỉnh sửa trên Route sẵn có.

Trong URL pattern, các tham số đều nằm trong ngoặc `{ }` đại diện cho các giá trị *có thể thay đổi*. Nếu không nằm trong ngoặc `{ }`, tham số đó được xem là tiền tố – ***luôn phải*** có trên đường dẫn khi gửi yêu cầu.

**Ví dụ:**

* Với route được khai báo như sau:
```csharp
    app.MapControllerRoute(
        name: "default",
        pattern: "web/{controller=Home}/{action=Index}/{id?}");
```
* Thì các yêu cầu được gửi đến phải có tiền tố `web` mới trùng khớp với route trên, ví dụ:

```
    /web/Home/Privacy
    /web/Product/Show/5
```

Tham số trên URL pattern là một định danh tùy chọn, tuy nhiên có một số từ khóa được định sẵn của ASP.NET Core khi khai báo URL Pattern như `{action}`, `{controller}`, `{page}`, `{handler}` và `{area}`.

Một URL pattern có thể là một chuỗi xác định sẵn – tức là không có tham số nào.

**Ví dụ:**
```csharp
    app.MapControllerRoute(
        name: "default",
        pattern: "user/home"); // URL không cần tham số nào
```
Tuy nhiên, Route ở ví dụ trên sẽ không sử dụng được và trả về lỗi **HTTP ERROR 404**.

Nguyên nhân là do Route trên không khai báo Action và Controller sẽ được gọi, do đó mà request không biết chuyển tiếp đến đến đâu. Một Route dù có URL pattern như thế nào cũng phải ít nhất chỉ định thông tin về Action và Controller.

Ở trường hợp trên có thể chỉ định với tham số `defaults` như sau:
```csharp
    // truy cập vào đường dẫn 'user/home' sẽ chuyển đến cho action 'Index' của 'HomeController'
    app.MapControllerRoute(
        name: "default",
        pattern: "user/home",
        defaults: new { controller = "Home", action = "Index");
```

Như vậy, đối với các URL pattern nói chung nếu không có 1 trong 2 hoặc cả 2 tham số `{controller}` và `{action}` thì phải chỉ định chúng trong tham số `defaults`.

Ta có thể khai báo thêm nhiều Route khác sử dụng cùng với Route mặc định. Tuy nhiên hãy lưu ý về thứ tự được thêm vào.

Khi Request được gửi đến, Route có URL pattern ***phù hợp đầu tiên*** sẽ được sử dụng để định tuyến và bỏ qua các Route khác. Do đó nếu giữa các URL pattern có sự tương đồng thì sẽ xảy ra xung đột và kết quả định tuyến có thể không như mong muốn.

**Ví dụ:**
```csharp
    app.MapControllerRoute(
        name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");

    app.MapControllerRoute(
        name: "products",
        pattern: "product/{action=Show}/{id?}",
        defaults: new { controller = "Product" });
```
Ở các route đăng ký như trên, thì yêu cầu có đường dẫn `products/Show/5` sẽ dùng Route `default` để định tuyến và bỏ qua Route `products`. Do ứng dụng kiểm tra được rằng đường dẫn `products/Show/5` phù hợp đầu tiên với URL pattern `{controller}/{action}/{id}`. 

Để tránh xung đột, ta thường phải cân nhắc trước khai báo các Route và đưa Route mặc định xuống dưới cùng.

### Giá trị mặc định cho tham số

Như đã trình bày ở [Tham số defaults](#tham-số-defaults), các tham số trên URL pattern có thể được chỉ định bằng tham số `defaults`.

Ngoài ra, ngay trên URL pattern ta có thể chỉ định giá trị cho tham số với cú pháp: `{parameter=value}`

**Ví dụ:**
```csharp
    app.MapControllerRoute(
        name: "plus",
        pattern: "plus/{a=0}/{b=0}",
        defaults: new { controller = "Home", action = "Sum" });
```
Các đường dẫn sau sẽ hợp lệ với Route trên:
```
    /plus/14/7
    /plus/15
    /plus
```

### Route Constraints

Các tham số trên URL pattern thường không khai báo các ràng buộc về kiểu dữ liệu, kích thước, khoảng giá trị, ... do đó trong một số trường hợp ta cần phải kiểm tra ràng buộc ngay trong Action.

**Ví dụ:**
```csharp
    public IActionResult Sum(string a, string b) // bắt dữ liệu với kiểu string
    {
        try
        {
            // kiểm tra xem tham số nhận được có phải là số hay không
            return Content(int.Parse(a) + int.Parse(b) + "");
		}
        catch
        {
            return Content("Sai định dạng số!");
        }
    }
```

Tuy nhiên, ta hoàn toàn có thể chỉ định các ràng buộc trên tham số. Khi thỏa mãn toàn bộ ràng buộc, Request mới được định tuyến đến Action tương ứng.

Có 2 cách để đặt ràng buộc cho tham số:

* ***Inline Constraint***: chỉ định ràng buộc ngay trên URL pattern với cú pháp: `{parameter:constraint}`

Bảng bên dưới sẽ liệt kế các ràng buộc có thể dùng:

<table>
    <tr>
        <th> Ràng buộc </th>
        <th> Mô tả </th>
        <th> Ví dụ </th>
    </tr>
    <tr>
        <td><code>int</code>, <code>bool</code>, <code>datetime</code>, <code>decimal</code>, <code>long</code>, <code>double</code>, <code>float</code></td>
        <td>Ràng buộc kiểu dữ liệu</td>
        <td><code>{id:int}</code></td>
    </tr>
    <tr>
        <td>
            <code>minlength(n)</code><br />
            <code>minlength(n)</code><br />
            <code>length(min, max)</code><br />
        </td>
        <td>Ràng buộc độ dài chuỗi</td>
        <td><code>{pwd:length(8, 16)}</code></td>
    </tr>
    <tr>
        <td>
            <code>length(n)</code>
        </td>
        <td>Ràng buộc độ dài chính xác của chuỗi</td>
        <td><code>{phone:length(10)}</code></td>
    </tr>
    <tr>
        <td>
            <code>min(n)</code><br />
            <code>min(n)</code><br />
            <code>range(min, max)</code><br />
        </td>
        <td>Ràng buộc khoảng giá trị số</td>
        <td><code>{count:range(1,100)}</code></td>
    </tr>
    <tr>
        <td>
            <code>alpha</code>
        </td>
        <td>Ràng buộc chuỗi không chứa số</td>
        <td><code>{name:alpha}</code></td>
    </tr>
    <tr>
        <td>
            <code>required</code>
        </td>
        <td>Yêu cầu phải có giá trị</td>
        <td><code>{msg:required}</code></td>
    </tr>
    <tr>
        <td>
            <code>regex(exp)</code>
        </td>
        <td>Ràng buộc khớp RegEx</td>
        <td><code>{ssn:regex(^\\d{{3}}-\\d{{2}}$)}</code></td>
    </tr>
</table>

**Ví dụ:**
```csharp
    app.MapControllerRoute(
        name: "products",
        pattern: "product/get/{productId:min(1)}",
        defaults: new { controller = "Product", action = "Detail" });
```
Một tham số có thể chỉ định nhiều ràng buộc, phân cách bằng dấu `:`, ví dụ `{id:alpha:required}`.

Một lưu ý quan trọng là tham số **không thể** vừa chỉ định giá trị mặc định vừa khai báo các ràng buộc. Ta có thể chỉ định giá trị mặc định trong tham số `defaults`.

* Sử dụng tham số `constraints`: Tham số `constraints` được dùng để khai báo các ràng buộc trên tham số đường dẫn. Các tham số được chỉ định ràng buộc với cú pháp:
```csharp
    parameter = new constraint_class()
```
Trong đó, `constraint_class` là các lớp có phần hậu tố `[...]RouteConstraint` như `IntRouteConstraint`, `MinRouteConstraint`, `DateTimeRouteConstraint`, ... tương tự các ràng buộc của Inline Constraint.

**Ví dụ:**
```csharp
    app.MapControllerRoute(
        name: "plus",
        pattern: "plus/{a=0}/{b=0}",
        defaults: new { controller = "Home", action = "Sum" },
        constraints: new { a = new IntRouteConstraint(), b = new IntRouteConstraint() });
```

### Optional Parameters

Tham số tùy chọn là các tham số có thể bỏ qua khi gửi yêu cầu, không cần xuất hiện trong URL.

Để biểu diễn tham số tùy chọn trên URL pattern, ta sẽ sử dụng dấu hỏi `?` phía sau tham số và sau tất cả các ràng buộc nếu có.

**Ví dụ:**
```
    {id:max(100)?}
    {name:alpha?}
```

**Lưu ý:**
* URL pattern không thể chứa cùng lúc cú pháp tham số tùy chọn và giá trị mặc định. Nếu muốn hãy chỉ định giá trị mặc định trong tham số `defaults`.
* Các tham số tùy chọn phải ở phía sau các tham số bắt buộc trên URL pattern.
**Ví dụ:** `{a}/{b?}/{c}` là không hợp lệ vì tham số tùy chọn `{b?}` nằm phía trước tham số bắt buộc `{c}`. Ta có thể phải viết lại thành: `{a}/{c}/{b?}`.

## Route attribute

Attribute `[Route]` được dùng để xác định Route trực tiếp trên một controller hay action cụ thể.

Cú pháp:
```csharp
    [Route(string template, Properties: [Name = string?], [Order = int])]
```

Trong đó:
* `template`: URL pattern dành cho controller hay action đang chỉ định (giống như [Conventional Route](#conventional-route)).
* `Name`: tên route, mặc định là `null`.
* `Order`: thứ tự thực thi của Route. Giá trị càng nhỏ thì sẽ được thực thi trước. Mặc định là `0`. Attribute `[Route]` sẽ ***được xem xét trước*** các Conventional Route.

### [Route] dùng cho action

Khi attribute `[Route]` chỉ định cho action, nó sẽ khai báo một URL pattern dẫn đến trực tiếp action đó, vì vậy ta sẽ không chỉ định tham số về controller hay action nữa.

**Ví dụ:**
```csharp
    public class HomeController : Controller
    {
        [Route("sum/{a=0}/{b=0}", Name = "plus")]
        public string Sum(int a, int b)
        {
            return a + b + "";
        }
        
        [Route("log/{msg?}", Name = "log")]
        public string Log(string? msg)
        {
            return msg ?? "No message found !";
        }
    }
```

Khi chỉ định Route, ta có thể khai báo cả thuộc tính `Name` để cung cấp một tên Route. Một số phương thức trong ASP.NET Core cần thông tin về tên Route, ví dụ phương thức `Html.BeginRouteForm()`.

Ta hoàn toàn có thể khai báo nhiều Route cho một Action (nhiều đường dẫn trỏ về một Action).

**Ví dụ:**
```csharp
    [Route("about")]
    [Route("company/about")]
    public IActionResult About()
    {
        return View();
    }
```

### [Route dùng cho Controller]

Khi `[Route]` sử dụng cho controller, nó có thể khai báo một tiền tố chung cho các Request gửi đến. 

Khi khai báo `[Route]` cho Controller, các Action cũng phải được chỉ định với attribute `[Route]`.

**Ví dụ:**
```csharp
    [Route("page")] // các yêu cầu phải bắt đầu bằng tiền tố 'page'
    public class HomeController : Controller
    {
        [Route("")] // mặc định: ~/page
        [Route("home")] // ~/page/home
        public IActionResult Index()
        {
            return View();
        }
    }
```
Sẽ có một Action được phép chỉ định làm Action mặc định với attribute `[Route("")]`.

Ta có thể sử dụng các thông báo `[controller]` và `[action]` để thay thế cho tên controller và action.

**Ví dụ:**
```csharp
    [Route("[controller]")] // thay cho /home
    public class HomeController : Controller
    {
        [Route("")] // ~/home
        [Route("[action]")] // ~/home/index
        public ActionResult Index()
        {
            return View();
        }

        [Route("search")] // ~/home/search
        [Route("[action]/{id:int}")] // ~/home/searchbyid/15
        public ActionResult SearchById(int id)
        {
            // code block ...
            return View();
        }
    }
```

