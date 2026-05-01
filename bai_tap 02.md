**Họ Tên** : Nguyễn Thu Hằng 

**MSSV** : K235480106088

**Lớp** : K59KMT.K01

# Phần 1 : Thiết kế và Khởi tạo Cấu trúc Dữ liệu

```
-- Tạo Database mới
CREATE DATABASE [QuanLyNhaHang_K235480106088];
GO

-- Sử dụng Database vừa tạo
USE [QuanLyNhaHang_K235480106088];
GO
-- 1. Bảng Loại món ăn (Danh mục như: Khai vị, Món chính, Đồ uống)
CREATE TABLE [LoaiMonAn] (
    [MaLoai] [INT] IDENTITY(1,1) NOT NULL,
    [TenLoai] [NVARCHAR](100) NOT NULL,
    CONSTRAINT [PK_LoaiMonAn] PRIMARY KEY ([MaLoai])
);

-- 2. Bảng Món ăn (Danh sách thực đơn)
CREATE TABLE [MonAn] (
    [MaMon] [VARCHAR](15) NOT NULL,
    [TenMon] [NVARCHAR](200) NOT NULL,
    [MaLoai] [INT] NOT NULL,
    [GiaTien] [DECIMAL](18, 0) NOT NULL, -- Kiểu số thực/tiền tệ
    [MoTa] [NVARCHAR](MAX) NULL,
    CONSTRAINT [PK_MonAn] PRIMARY KEY ([MaMon]),
    CONSTRAINT [FK_MonAn_Loai] FOREIGN KEY ([MaLoai]) REFERENCES [LoaiMonAn]([MaLoai]),
    CONSTRAINT [CK_GiaTien] CHECK ([GiaTien] >= 1000) -- Ràng buộc giá món thấp nhất là 1000 VNĐ
);

-- 3. Bảng Bàn ăn (Quản lý vị trí và trạng thái bàn)
CREATE TABLE [BanAn] (
    [MaBan] [INT] NOT NULL,
    [TenBan] [NVARCHAR](50) NOT NULL,
    [SucChua] [INT] NOT NULL, -- Số người tối đa
    [TrangThai] [NVARCHAR](30) DEFAULT N'Trống', -- Trống, Có khách, Đã đặt
    CONSTRAINT [PK_BanAn] PRIMARY KEY ([MaBan]),
    CONSTRAINT [CK_SucChua] CHECK ([SucChua] BETWEEN 2 AND 20) -- Bàn ít nhất 2 người, tối đa 20 người
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/af56b297-b95d-4e66-bd02-488fd00085e1" />

=> Hoàn thành ạo một Database mới với tên [Tên dự án]_[MaSV]. //LƯU Ý PHẢI CÓ MÃ SV CÁ NHÂN Ở TÊN CỦA DB ĐÚNG NHƯ YÊU CẦU, vd: QuanLyKhachSan_K123456789

Tạo ít nhất 3 bảng có quan hệ với nhau. Yêu cầu:

Sử dụng đa dạng các kiểu dữ liệu (Số nguyên, số thực, chuỗi ký tự Unicode, ngày tháng, tiền tệ, ...).

Áp dụng đúng quy tắc đặt tên (BướuLạcĐà).

Sử dụng cặp ngoặc [ ] để bọc tên bảng và tên trường trong script khởi tạo.

Có giải thích chỗ nào là PK, chỗ nào là FK, trường nào có ràng buộc cứng CK (ví dụ điểm từ 0..10),.

# Phần 2: Xây dựng Function

a,

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/2bec0672-2b12-4ff2-a9bd-c1b78e560012" />

*Hệ thống đã nạp dữ liệu mẫu thành công và khai thác triệt để các hàm hệ thống (COALESCE, FORMAT, DATEDIFF) để làm sạch dữ liệu, định dạng tiền tệ và tính toán thời gian thực ngay trên kết quả truy vấn*

b, 

Hàm tự viết thường được dùng để:

Tái sử dụng mã nguồn: Viết một lần logic nghiệp vụ (như tính thuế, tính tổng hóa đơn) và gọi lại ở nhiều nơi.

Rút gọn câu lệnh SQL: Thay vì viết một đoạn logic dài dằng dặc trong lệnh SELECT, bạn chỉ cần gọi tên hàm.

Đóng gói logic nghiệp vụ: Đảm bảo các quy tắc tính toán được áp dụng thống nhất trên toàn hệ thống.

SQL Server chia UDF thành 3 loại chính:

Scalar Function (Hàm vô hướng)	Trả về duy nhất một giá trị (số, chuỗi, ngày tháng).	Dùng khi cần tính toán đơn giản trên từng dòng (ví dụ: Tính tổng tiền món ăn sau thuế).

Inline Table-Valued Function	Trả về một bảng dữ liệu từ một câu lệnh SELECT duy nhất.	Dùng để thay thế các View có tham số, giúp lọc dữ liệu nhanh.

Multi-Statement Table-Valued Function	Trả về một bảng dữ liệu nhưng có logic phức tạp, dùng nhiều lệnh trong thân hàm.	Dùng khi cần xử lý dữ liệu qua nhiều bước trung gian trước khi trả về kết quả cuối cùng.

Tại sao cần viết hàm riêng khi đã có System Function?

Mặc dù SQL Server có hàng trăm hàm sẵn có, nhưng chúng ta vẫn cần tự viết vì:

Nghiệp vụ đặc thù: System function không thể biết quy tắc giảm giá của riêng nhà hàng bạn hay cách tính mã số sinh viên của TNUT.

Tính toán phức tạp: Kết hợp nhiều hàm hệ thống lại thành một quy trình xử lý riêng biệt.

Khả năng bảo trì: Khi quy tắc tính toán thay đổi (ví dụ: thuế VAT tăng từ 8% lên 10%), bạn chỉ cần sửa code bên trong hàm thay vì đi tìm và sửa hàng trăm câu truy vấn.

c, 
```
USE [QuanLyNhaHang_K235480106088];
GO

-- 1. Định nghĩa hàm Scalar Function
CREATE FUNCTION [fn_TinhGiamGia] (
    @MaBan INT, 
    @DonGia DECIMAL(18,0)
)
RETURNS DECIMAL(18,0)
AS
BEGIN
    DECLARE @SoTienGiam DECIMAL(18,0);
    DECLARE @TenBan NVARCHAR(50);

    -- Lấy tên bàn từ mã bàn truyền vào
    SELECT @TenBan = [TenBan] FROM [BanAn] WHERE [MaBan] = @MaBan;

    -- Kiểm tra điều kiện để tính mức giảm giá
    IF (@TenBan LIKE N'%VIP%')
        SET @SoTienGiam = @DonGia * 0.15; -- Giảm 15% cho bàn VIP
    ELSE
        SET @SoTienGiam = @DonGia * 0.05; -- Giảm 5% cho bàn thường

    RETURN @SoTienGiam;
END;
GO

-- 2. Câu lệnh SQL khai thác hàm
-- Giả sử tính thử mức giảm giá cho tất cả món ăn nếu ngồi tại các bàn khác nhau
SELECT 
    B.[TenBan] AS N'Vị trí bàn',
    M.[TenMon] AS N'Tên món',
    FORMAT(M.[GiaTien], 'N0', 'vi-VN') AS N'Giá niêm yết',
    FORMAT(dbo.[fn_TinhGiamGia](B.[MaBan], M.[GiaTien]), 'N0', 'vi-VN') AS N'Số tiền được giảm'
FROM [BanAn] B, [MonAn] M
WHERE B.[TrangThai] = N'Có khách';
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/eb4a7892-4782-4d9f-81df-7a053c178f33" />

*Hệ thống đã nạp dữ liệu mẫu thành công và khai thác triệt để các hàm hệ thống (COALESCE, FORMAT, DATEDIFF) để làm sạch dữ liệu, định dạng tiền tệ và tính toán thời gian thực ngay trên kết quả truy vấn.*

d, 
```
USE [QuanLyNhaHang_K235480106088];
GO

-- 1. Định nghĩa Inline Table-Valued Function
CREATE FUNCTION [fn_LocMonAnTheoGia] (
    @GiaMin DECIMAL(18,0),
    @GiaMax DECIMAL(18,0)
)
RETURNS TABLE
AS
RETURN (
    SELECT 
        M.[MaMon],
        M.[TenMon],
        L.[TenLoai],
        M.[GiaTien]
    FROM [MonAn] M
    INNER JOIN [LoaiMonAn] L ON M.[MaLoai] = L.[MaLoai]
    WHERE M.[GiaTien] BETWEEN @GiaMin AND @GiaMax
);
GO

-- 2. Câu lệnh SQL khai thác hàm
-- Ví dụ: Tìm các món ăn có giá từ 30.000 VNĐ đến 100.000 VNĐ
SELECT 
    [TenMon] AS N'Tên món ăn',
    [TenLoai] AS N'Phân loại',
    FORMAT([GiaTien], 'N0', 'vi-VN') + ' VNĐ' AS N'Giá bán'
FROM dbo.[fn_LocMonAnTheoGia](30000, 100000);
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/1093e94c-4f6c-4a96-bdb4-54c859fdb865" />

*Hệ thống đã nạp dữ liệu mẫu thành công và khai thác triệt để các hàm hệ thống (COALESCE, FORMAT, DATEDIFF) để làm sạch dữ liệu, định dạng tiền tệ và tính toán thời gian thực ngay trên kết quả truy vấn.*

e, 
```
IF OBJECT_ID('fn_BaoCaoTrangThaiBan') IS NOT NULL DROP FUNCTION [fn_BaoCaoTrangThaiBan];
GO

CREATE FUNCTION [fn_BaoCaoTrangThaiBan]()
RETURNS @BangTam TABLE (
    [MaBan] INT,
    [TenBan] NVARCHAR(50),
    [TrangThaiHienTai] NVARCHAR(30),
    [GhiChuPhucVu] NVARCHAR(100)
)
AS
BEGIN
    INSERT INTO @BangTam
    SELECT [MaBan], [TenBan], [TrangThai],
        CASE 
            WHEN [TrangThai] = N'Có khách' THEN N'Ưu tiên phục vụ món'
            WHEN [TrangThai] = N'Trống' THEN N'Sẵn sàng đón khách mới'
            ELSE N'Kiểm tra vệ sinh bàn'
        END
    FROM [BanAn];
    RETURN;
END;
GO

-- Khai thác Multi-Statement Function
SELECT * FROM dbo.[fn_BaoCaoTrangThaiBan]();
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/066a1139-df92-4e6e-ac14-0b0147361f9c" />

*Hàm đa câu lệnh đã khởi tạo cấu trúc bảng tạm và xử lý logic phân loại phức tạp, chuyển đổi dữ liệu trạng thái khô khan thành các chỉ dẫn vận hành cụ thể cho nhân viên nhà hàng*

# Phần 3: Xây dựng Store Procedure (Kiến thức 10)

a, 











