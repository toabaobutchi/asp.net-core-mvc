# ASP.NET Core MVC - Chapter 3: Cú pháp Razor

## Sơ lược về Razor

Razor là cú pháp đánh dấu dùng để nhúng mã **.NET** vào trong trang web (điển hình là C#). Một trang Razor (Razor page) bao gồm cú pháp Razor, C# và HTML.
Phần mở rộng của trang Razor là `*.cshtml`, là định dạng mặc định của các trang View trong ASP.NET Core MVC.
Razor bao gồm 2 cú pháp chính:

* *Razor Expressions* (Biểu thức Razor): dùng để nhúng các biểu thức C#.
* *Razor Code Blocks* (Khối lệnh Razor): dùng để khai báo một khối lệnh có thể viết mã C# bên trong.

## Razor Expression
Biểu thức trong C# có thể là lời gọi đến biến, hằng hay phương thức, có trả về giá trị kể cả kiểu `void`.

Bắt đầu biểu thức Razor bằng ký tự `@`, tiếp sau đó là biểu thức cần trả về.

**Cú pháp:** `@expression`

**Ví dụ:**

```csharp, html
    <p> Now is @DateTime.Now </p>
    @Html.ActionLink("Home page", "Index", "Home")
    <h4> @message </h4> <!-- gọi biến 'message' -->
```
Việc đặt biểu thức ngay sau ký tự `@` được gọi là cú pháp biểu thức Razor ngầm định (Implicit Razor Expressions).

Cú pháp tường minh dành cho biểu thức Razor (Explicit Razor Expression) sẽ như sau, khi đó biểu thức sẽ được bao quanh bởi dấu ngoặc `()`:
`@(expression)`.

Cú pháp tường minh thường dùng cho các biểu thức có nhiều toán hạng, các biểu thức gây nhầm lẫn với các nội dung mã HTML hay các biểu thức dùng cú pháp của Generics với `<>`.

**Ví dụ:** Giả sử biến a = 10, b = 4:
```html, csharp
    <p> Result: @a - b </p> <!-- Result: 10 – b -->
    <p> Result: @a - @b </p> <!-- Result: 10 – 4 -->
    <p> Result: @(a - b) </p> <!-- Result: 6 -->
```
Ở phần sau, ta sẽ khai báo biến khi đã làm quen với khối lệnh Razor.

## Razor Code Blocks
Để chỉ định một khối lệnh dành cho mã C#, ta có thể sử dụng cú pháp sau:
```csharp
    @{
        // code here ...
    }
```
**Ví dụ:**
```csharp
    @{
        int num = 10;
        string sayHello = "Hello, I am ", name = "John";
        string message = sayHello + name;
    }
```
Các biến được khai báo trong khối lệnh Razor không có hạn chế bởi tầm vực, có thể sử dụng ở khối lệnh hoặc biểu thức khác trong cùng một trang View.

**Ví dụ:**
```csharp
    @{
        int a = 10, b = 4;
        string message = "Hello, I am learning ASP.NET Core MVC";
    }
    <p> Result: @(a - b)</p>
    @{
        // sử dụng biến của khối lệnh khác
        message = "I am learning Razor syntax";
    }
```
Mặc định, mã trong khối lệnh Razor là C#, nhưng vẫn có thể chuyển đổi về HTML. Một cách dễ hiểu thì ta có thể viết mã HTML trong khối lệnh Razor.

**Ví dụ:**
```csharp
    @{
        DateTime now = DateTime.Now;
        <p> Now is @now </p>
    }
```
Để hiển thị một đoạn văn bản thuần túy, ta có thể sử dụng thẻ `<text>` - một thẻ Razor mà không cần sử dụng thẻ HTML nào khác.

**Ví dụ:**
```csharp, html
    <div>
    @{
        string msg = "Hello";
        <text>Message is @msg</text>
    }
    </div>
```

## Các cú pháp điều khiển
Các cú pháp điều khiển như `if`, `switch`, `for`, `while`, ... đều được xem là các câu lệnh đơn và đã bao gồm việc khai báo tầm vực, vì vậy mà không cần khai báo thêm khối lệnh mà có thể bắt đầu ngay với ký tự `@`.

**Ví dụ:**
```csharp
    @{
        int num = 19;
    }
    @if(num <= 15)
    {
        num++;
    }
    else
    {
        num--;
    }
```
**Lưu ý:** Khối lệnh `else` và `else if` không cần chỉ định với ký tự Razor `@`. Một số trường hợp có thể bỏ qua ngoặc `{ }` của cú pháp lệnh.

## Razor Comments
Trang Razor hỗ trợ cả cú pháp Comment của C# và HTML.

**Ví dụ:**
```csharp, html
    @{
        /* C# Comment */
        // C# Comment
    }
    <! -- HTML Comment -->
```
Bên cạnh đó, trang Razor còn cung cấp một cú pháp Comment riêng bao quanh bởi `@*` và `*@`.

## Các cú pháp Razor khác

Tài liệu này chỉ giới thiệu các cú pháp Razor thường dùng và phổ biến nhất. Các cú pháp Razor khác sẽ được nhắc đến khi các vấn đề liên quan được nhắc đến.

Xem thêm các cú pháp Razor khác tại: [Razor syntax reference for ASP.NET Core | Microsoft Learn](https://learn.microsoft.com/en-us/aspnet/core/mvc/views/razor?view=aspnetcore-7.0)
