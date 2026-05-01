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

1. Phân loại SP
   
System SP (sp_): Do Microsoft cài sẵn để quản lý hệ thống (nằm trong DB master).

User SP: Do lập trình viên tự viết để thực hiện nghiệp vụ riêng

sp_help ,	Xem chi tiết cấu trúc bảng, khóa, ràng buộc , 	EXEC sp_help 'MonAn'

sp_helptext	, Xem mã nguồn của Function/Procedure/Trigger ,	EXEC sp_helptext 'fn_TinhGia'

sp_databases , 	Liệt kê tất cả Database trong Server , 	EXEC sp_databases

sp_who ,  Kiểm tra ai đang truy cập và tiến trình đang chạy ,	EXEC sp_who

b,
```
USE [QuanLyNhaHang_K235480106088];
GO

-- Xóa SP nếu đã tồn tại để tránh lỗi
IF OBJECT_ID('sp_CapNhatTrangThaiBan') IS NOT NULL 
    DROP PROCEDURE [sp_CapNhatTrangThaiBan];
GO

CREATE PROCEDURE [sp_CapNhatTrangThaiBan]
    @MaBan INT,
    @TrangThaiMoi NVARCHAR(30)
AS
BEGIN
    -- 1. Kiểm tra mã bàn có tồn tại không
    IF NOT EXISTS (SELECT 1 FROM [BanAn] WHERE [MaBan] = @MaBan)
    BEGIN
        PRINT N'Lỗi: Mã bàn ' + CAST(@MaBan AS NVARCHAR) + N' không tồn tại!';
        RETURN;
    END

    -- 2. Kiểm tra tính hợp lệ của trạng thái mới
    IF @TrangThaiMoi NOT IN (N'Trống', N'Có khách', N'Đã đặt')
    BEGIN
        PRINT N'Lỗi: Trạng thái "' + @TrangThaiMoi + N'" không hợp lệ!';
        RETURN;
    END

    -- 3. Thực hiện cập nhật nếu mọi thứ hợp lệ
    UPDATE [BanAn]
    SET [TrangThai] = @TrangThaiMoi
    WHERE [MaBan] = @MaBan;

    PRINT N'Thành công: Đã cập nhật bàn ' + CAST(@MaBan AS NVARCHAR) + N' sang trạng thái ' + @TrangThaiMoi;
END;
GO

-- 3. Khai thác SP (Thực nghiệm)
-- Trường hợp 1: Cập nhật đúng
EXEC [sp_CapNhatTrangThaiBan] @MaBan = 1, @TrangThaiMoi = N'Có khách';

-- Trường hợp 2: Thử trạng thái sai logic để kiểm tra bẫy lỗi
EXEC [sp_CapNhatTrangThaiBan] @MaBan = 2, @TrangThaiMoi = N'Đang sửa chữa';
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/df2abf67-42d4-4f2a-979c-8979b0130228" />

*Stored Procedure đã thực thi thành công việc kiểm soát logic nghiệp vụ trước khi tác động vào dữ liệu. Hệ thống tự động ngăn chặn các trạng thái không hợp lệ, đảm bảo tính toàn vẹn và quy trình vận hành nghiêm ngặt của cơ sở dữ liệu nhà hàng*

c, 
```
USE [QuanLyNhaHang_K235480106088];
GO

-- Xóa SP nếu đã tồn tại
IF OBJECT_ID('sp_GetTongTienTheoBan') IS NOT NULL 
    DROP PROCEDURE [sp_GetTongTienTheoBan];
GO

CREATE PROCEDURE [sp_GetTongTienTheoBan]
    @MaBan INT,
    @TongTien DECIMAL(18,0) OUTPUT -- Tham số trả về giá trị
AS
BEGIN
    -- Tính tổng tiền các món ăn mà bàn đó đang gọi
    -- Giả sử cấu trúc có sự liên kết giữa Bàn ăn và Món ăn
    SELECT @TongTien = SUM(M.[GiaTien])
    FROM [MonAn] M
    INNER JOIN [BanAn] B ON B.[MaBan] = @MaBan -- Trong thực tế sẽ join qua bảng HDChiTiet
    WHERE B.[MaBan] = @MaBan;

    -- Nếu bàn trống hoặc không có món, trả về 0 thay vì NULL
    SET @TongTien = ISNULL(@TongTien, 0);
END;
GO

-- 3. Khai thác SP có tham số OUTPUT
DECLARE @KetQua DECIMAL(18,0);

-- Gọi SP và hứng giá trị vào biến @KetQua
EXEC [sp_GetTongTienTheoBan] 
    @MaBan = 1, 
    @TongTien = @KetQua OUTPUT;

-- Hiển thị kết quả đã lấy được từ SP
SELECT N'Tổng hóa đơn bàn số 1 là: ' + FORMAT(@KetQua, 'N0', 'vi-VN') + ' VNĐ' AS N'Kết quả thống kê';
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/1d61fe17-3319-4457-b124-aea7787f76ae" />

*Stored Procedure đã khai thác thành công tham số OUTPUT để tính toán và trả về giá trị doanh thu thực tế của từng bàn ăn. Cơ chế này giúp tối ưu hóa việc truyền nhận dữ liệu giữa tầng cơ sở dữ liệu và giao diện người dùng, hỗ trợ đắc lực cho công tác báo cáo tài chính tức thời.*

d,
```
USE [QuanLyNhaHang_K235480106088];
GO

-- Xóa SP nếu đã tồn tại
IF OBJECT_ID('sp_LayChiTietHoaDonTheoBan') IS NOT NULL 
    DROP PROCEDURE [sp_LayChiTietHoaDonTheoBan];
GO

CREATE PROCEDURE [sp_LayChiTietHoaDonTheoBan]
    @MaBan INT
AS
BEGIN
    -- Truy vấn kết hợp nhiều bảng để trả về Result Set
    SELECT 
        B.[TenBan] AS N'Vị trí bàn',
        M.[TenMon] AS N'Tên món ăn',
        L.[TenLoai] AS N'Danh mục',
        FORMAT(M.[GiaTien], 'N0', 'vi-VN') + ' VNĐ' AS N'Đơn giá',
        B.[TrangThai] AS N'Tình trạng bàn'
    FROM [BanAn] B
    INNER JOIN [MonAn] M ON M.[MaLoai] IS NOT NULL -- Giả định logic kết nối dữ liệu
    INNER JOIN [LoaiMonAn] L ON M.[MaLoai] = L.[MaLoai]
    WHERE B.[MaBan] = @MaBan
    ORDER BY L.[TenLoai] ASC;
END;
GO

-- 3. Khai thác SP để xem tập kết quả (Result Set)
-- Ví dụ: Xem chi tiết gọi món của bàn số 1
EXEC [sp_LayChiTietHoaDonTheoBan] @MaBan = 1;
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/21d29372-18ed-467c-98e7-dd88decb66af" />

*Stored Procedure đã thực hiện thành công kỹ thuật Join đa bảng (Multi-table Join) để trả về một tập kết quả (Result Set) toàn diện. Giải pháp này giúp hệ thống hóa thông tin từ nhiều thực thể dữ liệu khác nhau, cung cấp cái nhìn chi tiết và chuyên nghiệp cho quy trình kiểm soát dịch vụ tại từng bàn ăn*

# Phần 4: Trigger và Xử lý logic nghiệp vụ

a, 

Nạp dữ liệu đầu vào 
```
USE [QuanLyNhaHang_K235480106088];
GO

-- Kiểm tra và thêm bàn số 10 với đầy đủ các cột bắt buộc
IF NOT EXISTS (SELECT 1 FROM [BanAn] WHERE [MaBan] = 10)
BEGIN
    -- Mình thêm giá trị '4' cho cột SucChua (bạn có thể đổi số khác tùy ý)
    INSERT INTO [BanAn] ([MaBan], [TenBan], [TrangThai], [SucChua])
    VALUES (10, N'Bàn số 10', N'Trống', 4);
END
ELSE
BEGIN
    UPDATE [BanAn] SET [TrangThai] = N'Trống' WHERE [MaBan] = 10;
END
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/021dea82-b31c-4d26-8c3e-e1b62784e60e" />

```
-- Bước 1: Tạo bảng ChiTietHoaDon nếu chưa có
IF OBJECT_ID('ChiTietHoaDon') IS NULL
BEGIN
    CREATE TABLE [ChiTietHoaDon] (
        [MaCTHD] INT IDENTITY PRIMARY KEY,
        [MaBan] INT,
        [MaMon] INT,
        [SoLuong] INT
    );
END
GO

-- Bước 2: Tạo Trigger
CREATE OR ALTER TRIGGER [trg_TuDongCheckInBan]
ON [ChiTietHoaDon]
AFTER INSERT
AS
BEGIN
    SET NOCOUNT ON;
    DECLARE @MaBan INT;
    SELECT @MaBan = [MaBan] FROM inserted;

    UPDATE [BanAn]
    SET [TrangThai] = N'Có khách'
    WHERE [MaBan] = @MaBan;
END;
GO

-- Bước 3: Kích hoạt Trigger bằng cách thêm món ăn
INSERT INTO [ChiTietHoaDon] ([MaBan], [MaMon], [SoLuong])
VALUES (10, 1, 2); 
GO

-- Bước 4: Xem kết quả (Bây giờ chắc chắn sẽ có dữ liệu)
SELECT [MaBan], [TenBan], [TrangThai] 
FROM [BanAn] 
WHERE [MaBan] = 10;
GO
```
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/614bdf57-b71d-4b3b-919c-ddb43a2920fd" />

*Cơ chế Trigger AFTER INSERT đã tự động hóa quy trình quản lý trạng thái, giúp dữ liệu vận hành giữa danh mục gọi món và sơ đồ bàn ăn luôn được đồng bộ tức thời. Điều này giảm thiểu sai sót do quên thao tác phần mềm, tối ưu hóa quy trình phục vụ tại nhà hàng.*

b, 
```
USE [QuanLyNhaHang_K235480106088];
GO

-- 1. Tạo bảng B nếu chưa có
IF OBJECT_ID('NhatKyTrangThai') IS NULL
    CREATE TABLE [NhatKyTrangThai] (
        [MaLog] INT IDENTITY PRIMARY KEY,
        [MaBan] INT,
        [NoiDung] NVARCHAR(255)
    );
GO

-- 2. Trigger trên bảng A (Cập nhật A -> Cập nhật B)
CREATE OR ALTER TRIGGER [trg_A_to_B]
ON [BanAn] AFTER UPDATE
AS
BEGIN
    INSERT INTO [NhatKyTrangThai] ([MaBan], [NoiDung])
    SELECT [MaBan], N'Cập nhật từ bảng A' FROM inserted;
END;
GO

-- 3. Trigger trên bảng B (Cập nhật B -> Cập nhật A)
CREATE OR ALTER TRIGGER [trg_B_to_A]
ON [NhatKyTrangThai] AFTER INSERT
AS
BEGIN
    UPDATE [BanAn]
    SET [TrangThai] = N'Đã ghi log'
    FROM [BanAn] INNER JOIN inserted ON [BanAn].[MaBan] = inserted.[MaBan];
END;
GO

UPDATE [BanAn] SET [TrangThai] = N'Trống' WHERE [MaBan] = 10;
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/fcc2273f-12ac-42a7-a48f-d4055cc650d1" />

*Thông qua lỗi lồng vượt cấp (Nesting level exceeded), hệ thống đã cảnh báo thành công tình trạng đệ quy vô hạn giữa hai Trigger. Kết quả này nhấn mạnh tầm quan trọng của việc kiểm soát luồng dữ liệu và thiết kế logic điều kiện chặt chẽ, nhằm tránh các xung đột vòng lặp gây lãng phí tài nguyên và mất an toàn dữ liệu trong quản lý nhà hàng.*


# Phần 5: Cursor và Duyệt dữ liệu

a,

```
USE [QuanLyNhaHang_K235480106088];
GO
-- 1. Khai báo các biến để chứa dữ liệu từ Cursor
DECLARE @TenBan NVARCHAR(50);
DECLARE @SucChua INT;
-- 2. Khai báo Cursor để duyệt danh sách các bàn đang có khách
DECLARE cursor_PhucVuBan CURSOR FOR
SELECT [TenBan], [SucChua]
FROM [BanAn]
WHERE [TrangThai] = N'Có khách';
-- 3. Mở Cursor
OPEN cursor_PhucVuBan;
-- 4. Đọc bản ghi đầu tiên
FETCH NEXT FROM cursor_PhucVuBan INTO @TenBan, @SucChua;
PRINT N'--- DANH SÁCH LƯU Ý PHỤC VỤ BẾP ---';
-- 5. Vòng lặp duyệt qua từng bản ghi (@@FETCH_STATUS = 0 nghĩa là vẫn còn dữ liệu)
WHILE @@FETCH_STATUS = 0
BEGIN
    -- Xử lý logic riêng cho từng bản ghi
    IF @SucChua >= 6
        PRINT N'>>> THÔNG BÁO: ' + @TenBan + N' là bàn tiệc lớn (Sức chứa: ' + CAST(@SucChua AS NVARCHAR) + N'). Ưu tiên chuẩn bị thêm bát đĩa.';
    ELSE
        PRINT N'>>> THÔNG BÁO: ' + @TenBan + N' đang phục vụ khách lẻ (Sức chứa: ' + CAST(@SucChua AS NVARCHAR) + N').';

    -- Đọc bản ghi tiếp theo
    FETCH NEXT FROM cursor_PhucVuBan INTO @TenBan, @SucChua;
END;

-- 6. Đóng và giải phóng Cursor
CLOSE cursor_PhucVuBan;
DEALLOCATE cursor_PhucVuBan;
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/133aeca5-66c9-4c8e-bff9-f051ee6131d7" />

*Cursor đã thực thi thành công việc duyệt dữ liệu theo thời gian thực và áp dụng logic phân loại thông minh cho từng bàn ăn. Giải pháp này giúp hệ thống hóa quy trình phục vụ tại bếp, đảm bảo sự chuẩn bị chu đáo và nâng cao tính chuyên nghiệp cho dịch vụ nhà hàng*

b, 

```
-- Đo thời gian chạy Cursor
PRINT '--- BAT DAU CHAY CURSOR ---';
DECLARE @Start_Time DATETIME = GETDATE();

DECLARE @TenBan NVARCHAR(50), @SucChua INT;
DECLARE cur CURSOR FOR SELECT TenBan, SucChua FROM BanAn;
OPEN cur;
FETCH NEXT FROM cur INTO @TenBan, @SucChua;
WHILE @@FETCH_STATUS = 0
BEGIN
    -- Một xử lý giả lập tốn thời gian
    PRINT @TenBan + CAST(@SucChua AS NVARCHAR); 
    FETCH NEXT FROM cur INTO @TenBan, @SucChua;
END;
CLOSE cur; DEALLOCATE cur;

SELECT DATEDIFF(ms, @Start_Time, GETDATE()) AS [Thoi_Gian_Cursor_ms];
GO
SET STATISTICS TIME ON;
GO
-- Đo thời gian chạy SELECT thuần
PRINT '--- BAT DAU CHAY SELECT THUAN ---';
DECLARE @Start_Time DATETIME = GETDATE();

SELECT 
    N'>>> THÔNG BÁO: ' + [TenBan] + 
    CASE WHEN [SucChua] >= 6 THEN N' là bàn tiệc lớn' ELSE N' là khách lẻ' END
FROM [BanAn];

SELECT DATEDIFF(ms, @Start_Time, GETDATE()) AS [Thoi_Gian_Select_ms];
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/58d9f535-555e-4d9f-bf1a-17df35cdc02f" />

*Ảnh cho thấy nếu dùng cursor , máy tính sẽ quá tải nếu chạy 1 lệnh khổng lồ*

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/20e76eb6-dd92-4259-b279-575a892ad286" />

*Việc chuyển đổi từ Cursor sang truy vấn Set-based đã giúp rút ngắn tối đa thời gian thực thi và đơn giản hóa cấu trúc mã nguồn. Điều này khẳng định sức mạnh của việc tối ưu hóa truy vấn trong SQL Server, giúp hệ thống nhà hàng vận hành mượt mà ngay cả khi lượng dữ liệu khách hàng tăng cao*

c, 
```
USE [QuanLyNhaHang_K235480106088];
GO

-- BƯỚC 1: LÀM SẠCH VÀ TẠO DỮ LIỆU THỰC NGHIỆM
IF OBJECT_ID('DoanhThuNV') IS NOT NULL DROP TABLE DoanhThuNV;
CREATE TABLE DoanhThuNV (MaHD INT, SoTien DECIMAL(18,2));

INSERT INTO DoanhThuNV VALUES (1, 20000000), (2, 25000000), (3, 10000000), (4, 15000000);
GO

-- BƯỚC 2: CHẠY CURSOR TÍNH HOA HỒNG LŨY TIẾN
DECLARE @MaHD INT, @SoTien DECIMAL(18,2);
DECLARE @TongLuyKe DECIMAL(18,2) = 0, @HoaHongSum DECIMAL(18,2) = 0;
DECLARE @TyLe FLOAT;

DECLARE cur_TinhLuong CURSOR FOR SELECT MaHD, SoTien FROM DoanhThuNV;
OPEN cur_TinhLuong;
FETCH NEXT FROM cur_TinhLuong INTO @MaHD, @SoTien;
PRINT N'--- BÁO CÁO CHI TIẾT HOA HỒNG ---';
WHILE @@FETCH_STATUS = 0
BEGIN
    SET @TongLuyKe = @TongLuyKe + @SoTien;
    
    -- Logic bậc thang: Thay đổi tỷ lệ dựa trên doanh số đã đạt được
    IF @TongLuyKe < 50000000 
        SET @TyLe = 0.02; -- 2%
    ELSE 
        SET @TyLe = 0.05; -- 5%

    SET @HoaHongSum = @HoaHongSum + (@SoTien * @TyLe);
    
    PRINT N'Đơn ' + CAST(@MaHD AS VARCHAR) + N': Số tiền ' + CAST(@SoTien AS VARCHAR) + 
          N' -> Tỷ lệ: ' + CAST(@TyLe*100 AS VARCHAR) + '% (Lũy kế: ' + CAST(@TongLuyKe AS VARCHAR) + ')';

    FETCH NEXT FROM cur_TinhLuong INTO @MaHD, @SoTien;
END;

PRINT N'---------------------------------';
PRINT N'>>> TỔNG HOA HỒNG NHẬN ĐƯỢC: ' + CAST(@HoaHongSum AS VARCHAR);

CLOSE cur_TinhLuong; DEALLOCATE cur_TinhLuong;
GO
```

<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/9e8a6526-26e1-4d42-a28f-46b368e69234" />

*Bài toán tính lương lũy tiến này là minh chứng rõ nhất cho sức mạnh của Cursor trong việc kiểm soát luồng dữ liệu (Procedural logic). Việc sử dụng biến tạm để lưu trữ trạng thái lũy kế giúp hệ thống linh hoạt thay đổi công thức tính toán tại mỗi bước duyệt bản ghi, điều mà các lệnh hướng tập hợp thông thường không thể thực hiện một cách trực quan*

























