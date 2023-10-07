# ASP.NET Core MVC - Chapter 6: Truyền dữ liệu sang Controller

Bên cạnh thao tác truyền dữ liệu từ Controller sang View, thao tác theo chiều ngược lại cũng rất quan trọng. Nhận được dữ liệu từ View, Controller có thể nhận biết các yêu cầu (Request) từ người dùng và đưa ra các phản hồi phù hợp như chuyển hướng, cung cấp dữ liệu động được yêu cầu truyền ngược lại cho View, ...

Có 2 cách phổ biến để truyền dữ liệu từ View sang Controller:
* Truyền dữ liệu thông qua chuỗi truy vấn (Query string).
* Truyền dữ liệu bằng HTML Form.

## Query string
Sử dụng chuỗi truy vấn để truyền dữ liệu cho Controller là một trong các cách truyền thống và cơ bản nhất. 

Khi tìm hiểu đến nội dung [ASP Routing](), ta sẽ có thể cấu hình lại chuỗi truy vấn, nhưng trước tiên ta sẽ làm quen với chuỗi truy vấn cổ điển.

Trên các trang web hiện nay, không khó để bắt gặp được các đường dẫn như sau:
```
    https://www.youtube.com/watch?v=N-22YS[...]
    https://www.facebook.com/profile.php?id=100024[...]
```
Các thành phần sau ký tự `?` trên đường dẫn được gọi là *chuỗi truy vấn* (Query String). 
Chuỗi truy vấn cho phép khai báo các tham số (hay biến) đi kèm với giá trị và gửi đến máy chủ theo phương thức **HTTP GET**.
Chuỗi truy vấn bắt đầu bằng ký tự `?` và thường sẽ có cú pháp như sau:
```
    [url]?param1=value1&param2=value2&param3=value3&...
```
Trong đó, `param` là một định danh bất kỳ và `value` sẽ là một chuỗi không khoảng trắng và không đặt trong dấu nháy (nháy đơn hoặc nháy kép). 
Các tham số thứ 2 trở đi sẽ kết nối bằng ký tự `&`.

## Get query data
Để bắt các tham số và giá trị được gửi kèm trên đường dẫn, action xử lý sẽ phải thực hiện khai báo tham số - tương tự tham số cho các phương thức thông thường.

Một điều bắt buộc là tên của các tham số của action sẽ phải trùng với các tham số được gửi đến trên chuỗi truy vấn.

**Ví dụ:**
* Để nhận dữ liệu từ đường dẫn như sau:

    `~/Home/Sum?a=10&b=20`
* Ta có thể định nghĩa một Action Sum sau:
```csharp
    public string Sum(int a, int b)
    {
        return (a + b) + "";
    }
```
Chạy project và nhập chuỗi truy vấn như trên vào thanh địa chỉ, kết quả `30` sẽ trả về cho giao diện web.

Chuỗi truy vấn không khai báo cụ thể kiểu dữ liệu cho tham số, vì vậy hãy cẩn thận và có phương án xử lý các dữ liệu không mong muốn.
Các đường dẫn chứa chuỗi truy vấn vẫn được xem là đường dẫn bình thường, hoàn toàn có thể chỉ định ở các ngữ cảnh cần đường dẫn, ví dụ với thẻ `<a>`:
```html
    <a href="~/Home/Sum?a=10&b=20"> Get result </a>
```
Với các phương thức liên quan đến đường dẫn như `Url.ActionLink()` hay `Html.ActionLink()`, ta không thể chỉ định trực tiếp chuỗi truy vấn mà phải sử dụng tham số `routeValues`.

**Ví dụ:**
```html
    @Html.ActionLink("Get result", "Sum", "Home", new { a = 10, b = 20 })
    @* Nội dung HTML nhận được *@
    <a href="/Home/Sum?a=10&b=20"> Get result </a>
```
Nội dung về ***Route Values*** sẽ được trình bày ở [Chapter 7]().

## Get form data
Biểu mẫu (Form) thường dùng một trong 2 phương thức HTTP để truyền dữ liệu: POST hoặc GET.

Đối với biểu mẫu nói chung, thuộc tính HTML `name` của các đầu vào như `<input>`, `<select>`, ... sẽ trở thành tên tham số mà action cần nhận và lấy giá trị.

Để nhận các thông tin đầu vào của một biểu mẫu khi người dùng gửi đến (bằng cách ấn vào các phần tử như `<input type="submit" />`, `<button type="submit">` hoặc `<input type="image" />`), ta vẫn sẽ khai báo tham số cho action. 
Tuy nhiên sẽ có khác biệt giữa biểu mẫu sử dụng phương thức GET và POST.
Với biểu mẫu sử dụng phương thức `GET`, các giá trị đầu vào sẽ được gửi trên đường dẫn và có thể nhận dữ liệu theo cách thông thường.

**Ví dụ:**
* Ta có một biểu mẫu sau:
```html
    <form action="~/Home/ShowData" method="get">
        <input type="text" name="firstName" /> <br />
        <input type="text" name="lastName" /> <br />
        <input type="submit" value="Submit" />
    </form>
```
* Cùng với action xử lý dữ liệu:
```csharp
    public string ShowData(string firstName, string lastName)
    {
        return $"{firstName} {lastName}";
    }
```
Tuy nhiên, với biểu mẫu sử dụng phương thức `POST`, ta cần phải đặt attribute `[HttpPost]` trước action nhận và xử lý.

```csharp
    [HttpPost]
    public string ShowData(string firstName, string lastName)
    {
        return "{firstName} {lastName}";
    }
```
Khi sử dụng biểu mẫu, ta có thể sử dụng 2 thuộc tính **`asp-controller`** và **`asp-action`** để thay cho thuộc tính HTML `action`.

**Ví dụ:**
```html
    <form asp-controller="Home" asp-action="ShowData" method="post">
        <!-- input elements -->
    </form>
```
Xem thêm về các thuộc tính tương tự **`asp-controller`** và **`asp-action`** tại [Form Tag Helper](https://learn.microsoft.com/vi-vn/aspnet/core/mvc/views/working-with-forms?view=aspnetcore-6.0#the-form-tag-helper)
