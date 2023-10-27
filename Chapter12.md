# ASP.NET Core MVC - Chapter 12: Area

Area là một chức năng của ASP.NET dùng để tổ chức các chức năng có liên quan với nhau thành một nhóm riêng biệt. Mỗi Area đều có các Controller, View và Model riêng, tách biệt với các thành phần khác.

## Thêm Area cho dự án ASP.NET Core

* Để tạo một Area, right-click vào **tên project > Add > New Scaffolded Item...**.

* Chọn vào **MVC Area**.

* Đặt tên cho Area sau đó chọn **Add**.

* Thư mục `Areas` được tạo ra với cấu trúc thư mục như sau:

```
    .
    └── Areas
        └── [Area name]
                ├── Controllers
                ├── Data
                ├── Models
                └── Views
```

Các thư mục trong Area vừa tạo đều có đặc điểm tương tự các thư mục cùng tên bên ngoài cấu trúc MVC.

## Area Routing

