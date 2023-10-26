# ASP.NET Core MVC - Chapter 11: Upload files

## Thuộc tính enctype

Để xác định cách mã hóa dữ liệu khi gửi lên máy chủ, ta sẽ sử dụng thuộc tính `enctype` của thẻ `<form>` trong HTML.

Thuộc tính `enctype` có thể nhận 3 giá trị sau:

| Giá trị | Mô tả |
| --- | --- |
| `application/x-www-form-urlencoded` | Mặc định. Mã hóa các ký tự trước khi gửi |
| `multipart/form-data` | Dành cho việc gửi file lên máy chủ |
| `text/plain` | Không mã hóa dữ liệu. **Không khuyến nghị sử dụng** |

Đối với các biểu mẫu đầu vào thông thường, ta không cần chỉ định thuộc tính `enctype` và sử dụng giá trị
mặc định. Nội dung [**Chapter 7**](/Chapter7.md) đã trình bày về cách thao tác với biểu mẫu thông thường.

Ở nội dung này, ta sẽ tìm hiểu cách thao tác với biểu mẫu với thuộc tính `enctype="multipart/form-data"`, 
đặc trưng là việc gửi file đến máy chủ.

> [!Warning]
> Thuộc tính `enctype` chỉ có thể dùng với biểu mẫu sử dụng phương thức **HTTP POST**.


## Chọn định dạng tệp

Để người dùng có thể chọn tệp trong máy, ta sử dụng thẻ `<input type="file" />`.

Tuy nhiên người dùng có thể chọn bất cứ loại tệp nào từ máy của họ nếu như ta không hạn chế lại định 
dạng mà người dùng có thể chọn.

Để chỉ định loại tệp mà người dùng có thể chọn, ta sử dụng thuộc tính `accept` với cú pháp sau:

```html
    <input accept="file_extension | audio/* | video/* | image/* | media_type" />
```

Trong đó:

| Giá trị | Mô tả |
| --- | --- |
| `file_extension` | Chỉ định phần mở rộng cụ thể của file (.png, .mp3, .docx, ...) |
| `audio/*` | Cho phép các tệp âm thanh |
| `video/*` | Cho phép các tệp video |
| `image/*` | Cho phép các tệp hình ảnh |
| `media_type` | Xem thêm về [**Media Type**](https://www.iana.org/assignments/media-types/media-types.xhtml) |

**Ví dụ:**

```html
    <input type="file" accept="image/*" />
```
Ta có thể chỉ định nhiều giá trị và phân cách chúng bằng dấu phẩy (Comma) `,`.

> [!Warning]
> Không nên sử dụng thuộc tính `accept` như một công cụ kiểm tra đầu vào. Hãy thực hiện điều đó ở máy chủ.

## Nhận tệp được gửi

Như cách nhận dữ liệu từ biểu mẫu thông thường, ta sẽ khai báo các tham số cho Action được chỉ định xử
lý.

Với các giá trị đầu vào phổ biến, kiểu dữ liệu cho tham số có thể là `string`, `int`, `bool`, ... Nhưng để lấy dữ
liệu dạng tệp, ta sẽ sử dụng kiểu `IFormFile` cho tham số cần nhận.

**Ví dụ:**
* Form gửi file

```html
    <form action="~/Home/GetImage" method="post" enctype="multipart/form-data">
        <input type="file" accept="image/*" name="file" />
        <input type="submit" value="Submit" />
</form>
```
* Action nhận dữ liệu

```cs
    public IActionResult GetImage(IFormFile file)
    {
        // xử lý tệp
    }
```

Một số thuộc tính và phương thức của interface [**`IFormFile`**](https://learn.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.http.iformfile?view=aspnetcore-7.0) thường dùng như sau:

| Thuộc tính / Phương thức | Mô tả |
| --- | --- |
| `string FileName { get; }` | Tên file được gửi từ người dùng (bao gồm phần mở rộng file)
| `long Length { get; }` | Kích thước của tệp (tính theo đơn vị byte)
| `Stream CopyTo(Stream target)` | Lưu tệp vào luồng `target`
| `Stream CopyToAsync(Stream target)` | Lưu tệp bất đồng bộ vào luồng `target`

**Ví dụ:**

```cs
    public IActionResult GetImage(IFormFile file)
    {
        if(file == null || file.Length == 0)
        {
            // xử lý file rỗng
        }
        else
        {
            string path = Directory.GetCurrentDirectory() + "\\Uploads\\";
            Directory.CreateDirectory(path); // tạo thư mục Uploads nếu chưa tồn tại
            using(var stream = System.IO.File.Create(path + file.FileName)) {
                file.CopyTo(stream);
            }
            // chuyển hướng ...
        }
    }
```

## Xử lý với nhiều tệp

Để cho phép người dùng chọn nhiều file, ta có thể dùng thuộc tính `multiple` cho thẻ `<input>`.

**Ví dụ:**

```html
    <input type="file" accept="image/*" multiple />
```

Thuộc tính `multiple` chỉ có thể chỉ định cho đầu vào là `email` và `file`. Để chọn nhiều `file`, ta ấn giữ `Ctrl`
hoặc `Shift` khi chọn lựa.

Tham số cho Action dành để nhận nhiều file sẽ là một trong 3 kiểu dữ liệu sau:

* `IFormFileCollection`

* `IEnumerable<IFormFile>`

* `List<IFormFile>`

Về cơ bản cả 3 kiểu dữ liệu trên không khác nhau quá nhiều. Để thao tác với từng file ta có thể sử dụng vòng lặp `foreach`.

Bên cạnh đó khi thực hiện lưu nhiều file ta nên sử dụng phương thức bất đồng bộ `CopyToAsync()` nhằm tăng hiệu suất của ứng dụng.

**Ví dụ:**

```cs
    public async Task<IActionResult> GetImages(List<IFormFile> files)
    {
        // xử lý các trường hợp file lỗi
        string path = Directory.GetCurrentDirectory() + "\\Uploads\\";
        Directory.CreateDirectory(path);
        foreach(var file in files)
        {
            using(var stream = System.IO.File.Create(path + file.FileName))
            {
                await file.CopyToAsync(stream);
            }
        }
        // chuyển hướng
    }
```

## Các vấn đề khác

Xem thêm các vấn đề khi upload file tại: [**Upload files in ASP.NET Core**](https://learn.microsoft.com/en-us/aspnet/core/mvc/models/file-uploads?view=aspnetcore-7.0).
