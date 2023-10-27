# ASP.NET Core MVC - Chapter 13: Model Binding

Các Controller và trang Razor làm việc với các dữ liệu từ các HTTP Request. Thông thường, ta sẽ viết mã cho Action để nhận từng các dữ liệu và chuyển đổi từ kiểu chuỗi sang các kiểu dữ liệu .NET, điều đó cũng sẽ dễ gây ra lỗi.

Model Binding là một quá trình sẽ thực hiện tự động việc chuyển đổi kiểu trên. Quá trình Binding sẽ như sau:

* Nhận các dữ liệu mà View truyền sang Controller từ nhiều nguồn khác nhau: Form, Query string, Route data, ...

* Chuyển các dữ liệu nhận được từ kiểu chuỗi sang các kiểu dữ liệu .NET.

* Cố gắng ánh xạ các dữ liệu vào các thuộc tính của kiểu dữ liệu phức tạp (Complex type).

## Đặt vấn đề

Để bắt đầu tìm hiểu về Model Binding, ta sẽ làm quen với một ví dụ đơn giản và làm nền tảng để tiếp tục ở các phần sau.

Dữ liệu nhận được từ trang View có thể từ nhiều nguồn, nhưng nội dung này ta sẽ bắt đầu với dữ liệu từ biểu mẫu – nguồn dữ liệu sử dụng phổ biến nhất.

Ta có một biểu mẫu đơn giản sau:

```html
    <form action="~/Home/ShowProduct" method="post">
        <input type="text" name="Name" />
        <br />
        <input type="number" name="Price" />
        <br />
        <input type="text" name="Description" />
        <br />
        <input type="submit" value="Submit" />
    </form>
```

Để nhận các dữ liệu từ biểu mẫu trên, thông thường ta có thể định nghĩa Action `ShowProduct` như sau:

```cs
    [HttpPost]
    public IActionResult ShowProduct(string Name, double Price, string Description)
    {
        return Content($"{Name} - {Price} - {Description} VND");
    }
```

Việc khai báo nhiều tham số trên Action khiến việc lập trình trở nên phiền phức và dễ gây lỗi về kiểu dữ liệu. Như vậy, ta có thể ứng dụng cơ chế Model Binding để ánh xạ vào đối tượng có kiểu dữ liệu phức tạp hơn.

Ở đây, ta có thể khai báo một lớp `Product` – kiểu dữ liệu tự định nghĩa như sau:

```cs
    public class Product
    {
        public string Name { get; set; }
        public double Price { get; set; }
        public string Description { get; set; }
    }
```

Ta sẽ không cần khai báo nhiều tham số như ví dụ trên mà có thể khai báo Action `ShowProduct` như sau:

```cs
    [HttpPost]
    // binding các dữ liệu nhận được vào đối tượng Product
    public IActionResult ShowProduct(Product pro)
    { 
        return Content($"{pro.Name} - {pro.Price} - {pro.Description} VND");
    }
```

Quá trình ánh xạ kiểu dữ liệu diễn ra như sau:


