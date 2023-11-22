# ASP.NET Core MVC - Chapter 14: AJAX Request

**AJAX** (Asynchronous JavaScript And XML) là một trong các cách thức dùng để truyền dữ liệu từ View sang Controller trong ASP.NET.

Với AJAX, ta có thể:

* Đọc dữ liệu được gửi từ máy chủ sau khi trang đã được tải.

* Thực hiện cập nhật trang web mà không cần tải lại trang.

* Gửi yêu cầu (Request) cùng các dữ liệu đến máy chủ một cách ngầm định.
 
AJAX thường được sử dụng để truyền dữ liệu từ View sang Controller và nhận dữ liệu lại từ máy chủ mà không để người dùng không nhận ra các tiến trình này.

## JavaScript AJAX

Cú pháp thường dùng:

```js
    const xhttp = new XMLHttpRequest(); // có thể thay thế const thành var, let

    xhttp.onload = function() {
        // xử lý sau khi nhận phản hồi từ máy chủ
    }

    xhttp.open(method, url, async);
    xhttp.send();
```

Trong đó:

* `method` là phương thức truyền dữ liệu, có thể là `GET` hoặc `POST`.

* `url` là đường đẫn đến tài nguyên cần gửi yêu cầu ở máy chủ. Ở đây ta có thể hiểu là đường dẫn đến Action xử lý yêu cầu AJAX.

* `async` là tham số kiểu `bool` với giá trị mặc định là `true`. Tham số này sẽ quy định cách truyền dữ liệu bất đồng bộ hay không.

Trong đa phần các trường hợp, `GET` là giá trị thường dùng cho tham số `method` vì nó đơn giản và nhanh. Với phương thức `GET`, ta có thể gửi dữ liệu thông qua đường dẫn `url`.


Khuyến nghị nên sử dụng giá trị `true` (hoặc bỏ qua tham số `async`) để JavaScript có thể thực hiện các công việc khác trong khi đợi máy chủ phản hồi.

> Xem chi tiết về JavaScript AJAX tại: [**AJAX Introduction (w3schools.com)**](https://www.w3schools.com/js/js_ajax_intro.asp)

**Ví dụ:** Hiển thị một số ngẫu nhiên, trong đó số ngẫu nhiên được lấy từ máy chủ và gửi yêu cầu bằng AJAX.

* View:

```cs
    <p id="result"></p>
    <button onclick="getRandom()"> Get Random Number </button>

    <script>
        function getRandom() {
            const xhttp = new XMLHttpRequest();
            xhttp.onload = function () {
                var res = document.querySelector('#result');
                res.innerHTML = this.responseText;
            }
            xhttp.open('GET', '/Home/GenerateRandom', true); // gửi yêu cầu đến action `GenerateRandom` của `Home`
            xhttp.send();
        }
    </script>
```

Khi ấn vào `<button>`, số ngẫu nhiên sẽ được lấy từ máy chủ và hiển thị mà không cần tải lại trang.

Khi sử dụng JavaScript AJAX, ta cần quan tâm đến thuộc tính `responseText`, đại diện cho nội dung mà máy chủ phản hồi cho yêu cầu được gửi. Máy chủ có thể gửi trực tiếp một đoạn mã HTML, giá trị, ... **dưới dạng chuỗi**.

Ta cũng có thể gửi dữ liệu bằng chuỗi truy vấn (Query string) thông qua tham số `url` bằng AJAX, ví dụ như sau:

```js
    xhttp.open('GET', '/Home/GetProductById?id=15');
```
Các tham số - chuỗi truy vấn gửi qua AJAX sẽ không hiển thị trên thanh địa chỉ của trình duyệt như khi truyền theo phương thức `GET` như thông thường.

Ta cũng có thể dùng Route để chỉ định đường dẫn cùng các tham số.

**Ví dụ:**

```cs
    // với route như sau
    [Route("/tong/{a}/{b}")]

    // ta có thể chỉ định các tham số đường dẫn cho AJAX
    xhttp.open('GET', '/tong/15/10');
```

Các Action thông thường sẽ trả về kiểu `string` để xử lý và có thể nhúng nội dung vào giao diện hiện tại mà không cần tải trang.

Tuy nhiên nếu như vì một lý do nào đó mà không thể trả về kiểu `string` mà phải là `IActionResult`, hãy sử dụng phương thức `Content()`.

```cs
    ContentResult Content(string content)
```

**Ví dụ:**

```cs
    public IActionResult GenerateRandom()
    {
        if ( /* condition */ )
            return Content(new Random().Next() + "<br />");
        else // do something or redirect to somewhere ...
    }
```

Phương thức `Content()` trả về kiểu dữ liệu `ContentResult` – một lớp dẫn xuất của `IActionResult`.

Dữ liệu mà thuộc tính `responseText` nhận được cũng có thể là **JSON** (JavaScript Object Notation) – một dạng định dạng dữ liệu dạng chuỗi. Ta sẽ sử dụng phương thức `Json()` để phản hồi chuỗi Json đến AJAX và xử lý.

> Xem chi tiết về phương thức `Json()` tại: [**Controller.Json method**](https://learn.microsoft.com/en-us/dotnet/api/system.web.mvc.controller.json?view=aspnet-mvc-5.2)

Một định dạng dữ liệu khác mà AJAX có thể nhận lấy là **XML**, thông qua thuộc tính `responseXML` (Nội dung về XML sẽ không được đề cập tại đây).

## JQuery AJAX

Tương tự JavaScript AJAX, JQuery AJAX chỉ tinh chỉnh về mặt cú pháp nhằm trở nên đơn giản và trực quan hơn.

Có 4 phương thức ta có thể sử dụng để gửi yêu cầu với JQuery AJAX: `load()`, `get()`, `post()` và `ajax()`.

### Phương thức load()

Phương thức `load()` cung cấp một cú pháp đơn giản để tải dữ liệu từ máy chủ và nhúng dữ liệu đó vào phần tử được chọn `$(selector)`. 

Cú pháp của phương thức `load()`:

```js
    $(selector).load(url, data, callback_function)
```

Trong đó:

* `selector`: bộ chọn đến phần tử cần tương tác, ví dụ: `p`, `h1`, `#id`, `.class`, ...

* `url`: **tham số bắt buộc**, dùng để chỉ định đường dẫn đến tài nguyên sẽ nhận yêu cầu.

* `data`: là _tham số tuỳ chọn kiểu_ `object` (**Plain object**, **String** hoặc **Array**), dùng để chỉ định các dữ liệu được gửi đến máy chủ cùng với yêu cầu.

* `callback_function` (_tham số tùy chọn_): nhận một **Callback function** thực thi khi phương thức `load()` hoàn tất[^1] có dạng `function(response, status, xhr)` với:

    * `response`: chứa dữ liệu nhận từ máy chủ phản hồi về.

    * `status`: chứa trạng thái của yêu cầu (`success`, `notmodified`, `error`, `timeout` hoặc `parsererror`).
  
    * `xhr`: chứa đối tượng `XMLHttpRequest`.

[^1]: Sau khi phương thức `load()` hoàn tất lấy dữ liệu từ máy chủ, nhưng chưa nhúng vào phần tử được chỉ định.

> [!Note]
> * Phương thức `load()` sử dụng cả yêu cầu `GET` và `POST`.
>
> * Yêu cầu `POST` sẽ được sử dụng để gửi dữ liệu nếu như tham số `data` được chỉ định, ngược lại là `GET`. Do đó, muốn gửi dữ liệu mà vẫn giữ ở phương thức `GET`, hãy truyền trên đường dẫn `url` (**Ví dụ:** `url?a=10&b=19`).

**Ví dụ 1:** Sử dụng lại action `GenerateRandom`, dùng JQuery AJAX để lấy dữ liệu.

```js
    <p id="result"></p>
    <button id="btn"> Get Random Number </button>

    <script>
        $(document).ready(function() {
            $('#btn').click(function() {
                // lấy số ngẫu nhiên chèn vào phần tử có id='result'
                $('#result').load('/Home/GenerateRandom');
            });
        });
    </script>
```

**Ví dụ 2:** Sử dụng JQuery AJAX để gửi dữ liệu theo phương thức `POST`.

* View:

```js
    <input type="number" />
    <p id="result"></p>

    <script>
        $(document).ready(function() {
            $('input').keyup(function() {
                let value = $(this).val();
                // có tham số 'data' nên gửi theo phương thức POST
                $('#result').load('/Home/GetSquare', { data : value });
            });
        });
    </script>
```

* Controller:

```cs
    [HttpPost]
    public IActionResult GetSquare(int data)
    {
        return Content(data * data + "");
    }
```

### Phương thức get() và post()

Phương thức `get()` và `post()` đều được dùng để gửi yêu cầu đến máy chủ (có thể đi kèm dữ liệu) với phương thức GET và POST tương ứng.

Cú pháp:

* Phương thức `get()`:

```js
    $.get(url, callback)
```

* Phương thức `post()`:

```js
    $.post(url, data, callback)
```

Khi sử dụng phương thức `get()` hoặc `post()`, vì không có phần tử được chỉ định do đó logic chính xử lý dữ liệu nhận được từ máy chủ thường do hàm `callback` định nghĩa.

Cả 2 phương thức này đều có thể nhận callback tùy chọn như sau: `function(data, status)`.

Với `data` là dữ liệu nhận được từ máy chủ, `status` là trạng thái của yêu cầu.

**Ví dụ:**

```js
    <input type="number" id="n1" />
    <input type="number" id="n2" />
    <p id="result"></p>
    <button> Tính tổng </button>

    <script>
        $(document).ready(function() {
            $('button').click(function() {
                let n1 = $('#n1').val();
                let n2 = $('#n2').val();
                $.get(`/Home/TinhTong?a=${n1}&b=${n2}`, function(data) {
                    $('#result').html(data);
                });
            });
        });
    </script>
```

### Phương thức ajax()

Phương thức `ajax()` là phương thức tổng quát dùng để gửi yêu cầu AJAX cho phép cấu hình chi tiết trước khi gửi yêu cầu.

> Xem thêm về phương thức `ajax()` tại:
> * [**jQuery ajax() Method (w3schools.com)**](https://www.w3schools.com/jquery/ajax_ajax.asp)
>
> * [**jQuery.ajax() | jQuery API Documentation**](https://api.jquery.com/jquery.ajax/)
