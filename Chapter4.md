# ASP.NET Core MVC - Chapter 4: Truyền dữ liệu sang View

Trong mô hình MVC khi phát triển Web, thành phần View thường không chứa các logic xử lý và tương tác trực tiếp với dữ liệu nguồn. Do đó, để hiển thị giao diện động về dữ liệu, các trang View chỉ có nhiệm vụ nhận dữ liệu từ phía Controller cung cấp và tạo ra giao diện theo mẫu.

Vì vậy, nội dung này sẽ trình bày các cách để truyền dữ liệu mà Controller đã truy vấn, xử xý đến cho View.

## Strongly-typed data (Viewmodel)
**Strongly-typed** hay **Strong typing** là cách truyền dữ liệu cho phép ta kiểm soát kiểu mà dữ liệu được gửi qua View. Bất kỳ biến hay hằng phải có khai báo kiểu dữ liệu một cách tường minh. Quá trình kiểm tra kiểu dữ liệu sẽ được thực hiện ở thời điểm biên dịch.
Các truyền dữ liệu này thường được biết đến với tên là *Viewmodel*. Để truyền dữ liệu theo kiểu này, ta sẽ sử dụng phương thức `View()`. 

Vì phương thức `View()` sẽ được trình bày chi tiết ở [Chapter 5](/Chapter5.md), vì vậy ta sẽ xét cú pháp đơn giản nhất như sau:

```csharp
  View(object? model)
```
Trong đó, `model` là dữ liệu cần truyền sang trang View tương ứng.

Viewmodel thường được dùng để truyền một đối tượng, một danh sách cho các tác vụ lớn. Nhưng các kiểu dữ liệu đơn giản vẫn có thể dùng Viewmodel để truyền.

**Ví dụ:**
```csharp
  public IActionResult Index() {
    List<string> names = new List<string>() { "Harry", "Bob", "John", "Mary" };
    return View(names); // truyền danh sách 'names' sang cho View
  }
```
Để trang View tương ứng nhận được dữ liệu mà Controller truyền sang, ta sử dụng chỉ thị `@model` với cú pháp:

```csharp
  @model <kiểu dữ liệu>
```
Chỉ thị `@model` thường sẽ đặt ở đầu trang View. Chỉ thị này được dùng để khai báo dữ liệu nhận từ Controller có kiểu dữ liệu là gì.

Nếu `<kiểu dữ liệu>` là kiểu do lập trình viên định nghĩa (User-defined Datatype), ta phải chỉ định đầy đủ tên của kiểu bao gồm: Tên project, tên các namespace và tên kiểu dữ liệu. 
Mặc dù vậy, trong một số tình huống Visual Stuido có thể tự nhận biết hoặc gợi ý sử dụng.

**Ví dụ:**
```csharp
  // Nếu lớp 'Student' nằm trong namespace 'Models' trong project 'WebApp'
  @model Student // không tìm thấy
  @model WebApp.Models.Student // tìm thấy
```
Nhưng với các kiểu dữ liệu dựng sẵn (Built-in Datatype), ta không cần phải chỉ định đầy đủ.

**Ví dụ:**
```csharp
  @model List<string>
```
Sau khi đã khai báo kiểu dữ liệu với chỉ thị `@model`, ta đã có thể sử dụng dữ liệu mà Controller truyền sang bằng cách gọi thông qua thuộc tính `Model`. Thuộc tính `Model` lúc này sẽ nhận dữ liệu và có kiểu dữ liệu là kiểu đã khai báo.

**Ví dụ:**
```csharp, html
  @model List<string>
	
  <p> Name count: @Model.Count </p>
  @foreach(string name in Model)
  {
    <p> @name <p>
  }
```

## Weakly-typed data
**Weakly typed** hay còn gọi là **Loosely typed** là cách truyền dữ liệu sang View không cần khai báo kiểu dữ liệu một cách tường minh khi sử dụng.

Cách truyền dữ liệu này cung cấp các cú pháp đơn giản và nhanh chóng nên thường được dùng để gửi một lượng dữ liệu không lớn cho các tác vụ đơn giản.
Có 2 cách đơn giản để truyền dữ liệu sang View theo cách Weakly typed:
* Sử dụng đối tượng `ViewData` hoặc attribute `[ViewData]`.
* Sử dụng đối tượng `ViewBag`.

### ViewData và [ViewData]
`ViewData` là đối tượng kiểu `ViewDataDictionary` sử dụng khóa truy cập kiểu `string` và lưu giá trị kiểu `object` (vì vậy không hạn chế giá trị lưu trữ).
```csharp
  ViewData[name] = value;
```
Dữ liệu được truyền sang View có thể được truy cập một cách trực tiếp bằng tên khóa tương ứng. Ta cần phải thực hiện ép kiểu khi muốn sử dụng giá trị từ `ViewData`, nhưng trừ các giá trị kiểu dữ liệu nguyên thủy.

**Ví dụ:**
* Truyền dữ liệu từ Controller:
```csharp
  public IActionResult Index()
  {
    ViewData["Name"] = "John";
    ViewData["Age"] = 17;
    return View();
  }
```
* Gọi ra sử dụng ở View:
```html
  <p> Name: @ViewData["Name"] </p>
  <p> Age: @ViewData["Age"] </p>
```
Không những dùng tương tác giữa Controller và View mà còn giữa các trang View, bao gồm Partial View và Layout Page ([Đường dẫn sẽ được cập nhật sau]()).

Do lưu giá trị kiểu `object`, nên giá trị của các kiểu dữ liệu phức tạp cần phải thông qua ép kiểu trước khi sử dụng.

**Ví dụ:**
* Controller:
```csharp
  public IActionResult Index()
  {
    ViewData["Emp"] = new Employee() { Name = "David", Salary = 1000 };
    return View();
  }
```
* View:
```html
  @{ var emp = (WebApp.Models.Employee)ViewData["Emp"]; }
  <p> Name: @emp.Name </p>
  <p> Salary: @emp.Salary</p>
```
