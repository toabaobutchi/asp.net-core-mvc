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

![snapedit_1698403449827](https://github.com/toabaobutchi/asp.net-core-mvc/assets/147165208/c7fe4679-af27-48d4-b01f-a35f853ee3d2)

## Các lưu ý khi Model Binding

Khi sử dụng cơ chế Model Binding, ta cần lưu ý một số điều sau đây:

* Tên thuộc tính của đối tượng và giá trị của thuộc tính `name` trong thẻ `<input>` vẫn cần phải tương tự nhau (Case-insensitive – không phân biệt chữ hoa chữ thường). 

**Ví dụ:** `name="name"` thì thuộc tính có thể là `Name`, `name`, `NAME`, ...

* Số dữ liệu từ View có thể ít hơn hoặc nhiều hơn số thuộc tính trong đối tượng (không gây ra lỗi). Nếu số dữ liệu nhiều hơn, ta hoàn toàn có thể khai báo thêm các tham số cho Action.

## Nguồn dữ liệu

Ở các ví dụ trên, ta đã làm việc với các dữ liệu từ biểu mẫu (HTML Form).
Mặc định, cơ chế Model Binding sẽ nhận các cặp khóa – giá trị (hay dữ liệu đầu vào) theo thứ tự sau đây:

* Form fields

* Request body

* Route data

* Query string parameters

* Uploaded files

Tức là nếu các dữ liệu từ View có cùng tên khóa (tên thuộc tính name, tên tham số trên URL, ...) thì Model Binding sẽ sử dụng dữ liệu từ nguồn ưu tiên hơn.

**Ví dụ:**

```html
    // dữ liệu gửi đến vừa có Form fields vừa có Query string parameters
    <form action="~/Home/Show?myId=10" method="post">
        <input type="number" name="myId" />
        @* các ô đầu vào khác *@
    </form>
```

Ở biểu mẫu trên, có 2 loại dữ liệu có cùng khóa là `myId`, như vậy theo như độ ưu tiên của nguồn dữ liệu thì dữ liệu từ biểu mẫu sẽ được sử dụng.

```cs
    public IActionResult Show(int myId) // dùng ô đầu vào của biểu mẫu
    {
		// xử lý ...
	}
```

> [!Note]
>
> Các tham số hoặc thuộc tính sẽ nhận dữ liệu theo danh sách ưu tiên trình bày phía trên, trừ trường hợp:
>
> * Route data và Query string parameters chỉ được dùng với các kiểu dữ liệu đơn giản.
>
> * Uploaded files sẽ được chuyển đổi thành kiểu `IFormFile` hoặc `IEnumerable<IFormFile>`.

Tuy nhiên, ASP.NET Core cho phép ta xác định nguồn dữ liệu mà tham số Action sẽ nhận, bao gồm các attribute sau:

* 
