# ASP.NET Core MVC - Chapter 10: Partial View

## Partial view

Partial view trong ASP.NET Core là một trang Razor `*.cshtml` chứa nội dung có thể tái sử dụng ở nhiều trang View khác nhau.

Về cơ bản, Partial view chỉ chứa nội dung mà không phải toàn bộ cấu trúc HTML. Phần nội dung đó sẽ được trang View gọi đến và nhúng vào vị trí chỉ định.

Các ứng dụng của Partial view:

* Chia các nội dung lớn thành các thành phần nhỏ hơn.

* Giúp nội dung trong trang View chính trở nên dễ quản lý hơn, tập trung vào nội dung chính.

* Hạn chế các nội dung trùng lặp ở các trang View. Partial không nên được sử dụng như một giao diện chung cho các trang. Hãy sử dụng Razor Layout Page thay thế.

## Thêm Partial view

Partial view là thành phần có thể tái sử dụng, vì vậy nên được tạo trong thư mục `Shared`.

* Right-click vào thư mục **Shared > Add > View ...**

* Chọn **Razor View – Empty** và đặt tên cho file.

Sau khi tạo view, hãy chỉ định một nội dung đơn giản, ví dụ như:

```html
    <h3 style="color:red;"> Content from partial view </h1>
```

