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

Tức là nếu các dữ liệu từ View có cùng tên khóa (tên thuộc tính `name`, tên tham số trên URL, ...) thì Model Binding sẽ sử dụng dữ liệu từ nguồn ưu tiên hơn.

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

* `[FromQuery]` - lấy giá trị từ Query string (chuối truy vấn)

* `[FromRoute]` - lấy giá trị từ Route data (có cả thông tin về Controller, Action, ...)

* `[FromForm]` - lấy từ HTML Form

* `[FromBody]` - lấy dữ liệu từ phần thân của yêu cầu (Request body)

* `[FromHeader]` - lấy dữ liệu từ HTTP Header

**Ví dụ:**

```cs
    // dùng dữ liệu từ query string
    public IActionResult Show([FromQuery] int myId)
    { 
        // xử lý ...
    }
```

Bên cạnh việc chỉ định cho tham số của Action, ta có thể dùng các attribute trên cho thuộc tính của đối tượng.

**Ví dụ:**

```cs
    public class Product
    {	
        [FromQuery] // khi binding sử dụng dữ liệu từ Query string
        public string Name { get; set; }

        [FromRoute] // khi binding sử dụng dữ liệu từ Route data
        public double Price { get; set; }

        public string Description { get; set; }
    }
```

Các attribute trên tham số của Action không ghi đè các attribute trên thuộc tính của đối tượng, trừ `[FromBody]`.

## Attribute [Bind], [BindNever] và [BindRequired]

Có 3 attribute thường dùng để kiểm soát cơ chế Model Binding trên các thuộc tính của lớp: `[Bind]`, `[BindNerver]`, `[BindRequired]`.

* Attribute `[Bind]` dùng để chỉ định các thuộc tính dùng cơ chế Model Binding.

**Ví dụ:**

```cs
    [Bind("Name, Description")]
	public class Product
    {	
        public string Name { get; set; } 
        public double Price { get; set; } // không được binding
        public string Description { get; set; }
	}
```
Attribute này còn có thể chỉ định trước tham số của Action.

**Ví dụ:**

```cs
    public IActionResult Show([Bind("Name, Price")] Product pro)
    {
        // xử lý ...
    }
```

* Attribute `[BindNever]` được sử dụng để chỉ định thuộc tính nào không sử dụng khi binding.

* Attribute `[BindRequired]` yêu cầu dữ liệu cho thuộc tính được chỉ định. Nếu không một lỗi vi phạm xác thực sẽ đưa ra với thuộc tính tương ứng. Xem thêm về [**ASP.NET Core Validation**]().

## Custom Model Binder

Việc ánh xạ giữa các dữ liệu request và lớp thực thể trong chương trình được thực hiện bởi Model Binder (có thể tạm dịch là _Trình ánh xạ thực thể_).

Model Binder mặc định hỗ trợ hầu hết các kiểu dữ liệu .NET Core và có thể thoả mãn hầu hết các nhu cầu của lập trình viên. Tuy nhiên, một số đầu vào dữ liệu không được ánh xạ theo cách thông thường.

**Ví dụ:** Ta muốn quy định rằng thuộc tính `string Resolution { get; set; }` của lớp `Laptop` là tổng hợp của 2 ô đầu vào `<input name="width" />` và `<input name="height" />`. Tuy nhiên, với Model Binder mặc định, các ô đầu vào này sẽ tìm đến các thuộc tính cùng tên (không phân biệt text-case). Như vậy, ta sẽ tạo một logic ánh xạ mới cho nhu cầu hiện tại.



