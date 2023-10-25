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
mặc định. Nội dung [Chapter 7](/Chapter7.md) đã trình bày về cách thao tác với biểu mẫu thông thường.

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
| file_extension | Chỉ định phần mở rộng cụ thể của file (.png, .mp3, .docx, ...) |
| audio/* | Cho phép các tệp âm thanh |
| video/* | Cho phép các tệp video |
| image/* | Cho phép các tệp hình ảnh |
| media_type | Xem thêm về [**Media Type**](https://www.iana.org/assignments/media-types/media-types.xhtml) |

