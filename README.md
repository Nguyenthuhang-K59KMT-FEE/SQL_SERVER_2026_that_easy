**Họ và tên** : Nguyễn Thu Hằng 

**Lớp**  K59 KMT.01

**MSSV** : K235480106088

-truy cập đường link https://www.microsoft.com/vi-vn/sql-server/sql-server-downloads
- sau khi giải nén thì kích đúp vào file
  
<img width="1913" height="1063" alt="image" src="https://github.com/user-attachments/assets/7dd4ca47-e09c-41c9-8b85-c9edc9fd87de" />

-Chọn Download Media để cài dặt trên nhiều máy tính khác nhau mà không cần phải tải lại và có thể cài đặt khi không có kết nối internet.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/784bb3a4-bcc3-4206-85e7-593e595b3c98" />

Đây là bước chọn file cài đặt cho Microsoft SQL Server 2019 (bản Developer):

Select Language: Chọn ngôn ngữ cài đặt (mặc định English).
ISO (1367 MB): File ảnh đĩa, tải về rồi mount để cài → khuyên dùng.
CAB (1416 MB): Dạng nén, phức tạp hơn, ít dùng.
Download Location: Nơi lưu file tải về trong máy.
Nhấn Download để bắt đầu tải.

<img width="1915" height="1062" alt="image" src="https://github.com/user-attachments/assets/0f0684a3-5b4e-4ab5-8f44-31e02633bc4b" />

Downloading phương tiện 

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/0f62681a-039d-473e-8fcc-ec4adecb6abc" />

Cấu hình cho SQL Server làm việc ở cổng động (Dynamic Port),
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a5370a77-751b-437e-9773-e1522c73a3d1" />

Kiểm tra xem service SQL Server bằng Command Prompt
<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/813387c1-412c-4580-a12e-aa8a7a6c4ee6" />

Cài đặt SQL Server Management Studio
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/04f7b22e-95c5-4a1e-b059-f1859843af60" />

Chạy phần mềm ssms để Đăng nhập vào SQL Server bằng 2 cách: Windows Authentication và SQL Server Authentication.
>Windows Authentication
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3ce98abd-4c43-4789-8954-502d29ac9e70" />

>SQL Server Authentication.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6c3692cf-2515-4cd3-a4b9-135ca222d95f" />


Đường dẫn 2 file đã tạo ra khi tạo ra database
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/622ff183-cebf-452a-b637-9ce97860b3d4" />

Tạo bảng dữ liệu với khoá chính (Primary Key) là trường masv
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/dbf40bb2-8c75-4b29-aa89-e4c1f31bdff0" />

Import dữ liệu từ file csv mẫu vào trong bảng vừa tạo
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/09c72303-b349-4af9-9388-f9c88e64e30a" />

>Import thành công dữ liệu vào trong table
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a3a45c4c-142e-4a4d-95a3-9229efbec409" />

Kiểm tra xem số dòng của bảng dữ liệu sau khi import: gần 12020 dòng
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/a0a44d9f-2ea3-41b4-8d13-647116e7dff8" />

Thêm 1 row vào bảng với dữ liệu là thông tin cá nhân.
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/720ce9b5-bd8e-41e9-a79b-38de1e3d6fee" />

Cập nhật(update) trường noisinh thành 'Sao Hoả' cho những dòng có noisinh và diachi đều là NULL
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/18f6dbe0-0248-4581-80c9-087a58206349" />

Tạo bảng SaoHoa gồm những sinh viên có nơi sinh ở 'Sao Hoả'
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/43685c3f-628c-4cab-ac6f-f2707c9caf16" />

Xoá (delete) trong bảng SaoHoa những sinh viên cùng họ: (Họ Nguyễn)
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6d38f156-9be2-4630-a2cc-d901a5164d96" />

Xuất toàn bộ kết quả của các bước 6,7,8,9,10,11,12,13 ra file dulieu.sql
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/82b085b9-7349-4c22-9f86-1afb867fdf6f" />

Xoá cơ sở dữ liệu đã tạo, kiểm tra tại path
> Xóa CSDL
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/18a91eaa-94c0-4785-861f-e4c684be9e0d" />

> Kiểm tra lại đường dẫn
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2c0cb137-ee65-41b3-af1e-6de071d1d2f2" />

Mở file dulieu.sql của bước 14, chạy toàn bộ các lệnh kết quả được tạo ra tương đương với các bước 6, 7, 8, 9, 10, 11, 12, 13.
> Tiến hành chạy file dulieu.sql
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/29c29237-751a-447b-9631-b965b405ab97" />

> Kiểm tra dữ liệu các bảng khi tạo lại
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8ec3aef3-2cf6-494f-82c1-cb191741ddf5" />

Upload file dulieu.sql lên github repository















