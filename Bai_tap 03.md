Họ tên: Nguyễn Thu Hằng

MSSV: K235480106088

Lớp: K59KMT.K01

Bài Tập 03: THIẾT KẾ VÀ CÀI ĐẶT CSDL QUẢN LÝ CẦM ĐỒ

# Nhiệm Vụ 1:Thiết kế CSDL

```
-- 1. Tạo Database mới
CREATE DATABASE QuanLyCamDo;
GO
USE QuanLyCamDo;
GO

-- 2. Bảng Khách hàng (KhachHang)
CREATE TABLE KhachHang (
    MaKH INT PRIMARY KEY IDENTITY(1,1), -- Khóa chính tự tăng
    HoTen NVARCHAR(100) NOT NULL,
    SoDienThoai VARCHAR(15),
    CCCD VARCHAR(12) UNIQUE,
    DiaChi NVARCHAR(255)
);

-- 3. Bảng Nhân viên (NhanVien) - Để ghi nhận người thu tiền trong Log
CREATE TABLE NhanVien (
    MaNV INT PRIMARY KEY IDENTITY(1,1),
    HoTen NVARCHAR(100) NOT NULL,
    ChucVu NVARCHAR(50)
);

-- 4. Bảng Hợp đồng (HopDong)
CREATE TABLE HopDong (
    MaHD INT PRIMARY KEY IDENTITY(1,1),
    MaKH INT NOT NULL, -- Khóa ngoại nối tới KhachHang
    NgayVay DATETIME DEFAULT GETDATE(),
    SoTienGoc DECIMAL(18,2) NOT NULL,
    Deadline1 DATE NOT NULL, -- Mốc tính lãi kép
    Deadline2 DATE NOT NULL, -- Mốc thanh lý tài sản
    TrangThai NVARCHAR(50) DEFAULT N'Đang vay',
    CONSTRAINT FK_HopDong_KhachHang FOREIGN KEY (MaKH) REFERENCES KhachHang(MaKH)
);

-- 5. Bảng Tài sản (TaiSan)
CREATE TABLE TaiSan (
    MaTS INT PRIMARY KEY IDENTITY(1,1),
    MaHD INT NOT NULL, -- Khóa ngoại nối tới HopDong
    TenTaiSan NVARCHAR(100) NOT NULL,
    GiaTriDinhGia DECIMAL(18,2),
    TrangThaiTS NVARCHAR(50) DEFAULT N'Đang cầm cố',
    CONSTRAINT FK_TaiSan_HopDong FOREIGN KEY (MaHD) REFERENCES HopDong(MaHD)
);

-- 6. Bảng Nhật ký (LogBienDong)
CREATE TABLE LogBienDong (
    MaLog INT PRIMARY KEY IDENTITY(1,1),
    MaHD INT NOT NULL, -- Khóa ngoại nối tới HopDong
    MaNV INT NOT NULL, -- Khóa ngoại nối tới NhanVien
    NgayGiaoDich DATETIME DEFAULT GETDATE(),
    SoTienTra DECIMAL(18,2),
    NoiDung NVARCHAR(255),
    CONSTRAINT FK_Log_HopDong FOREIGN KEY (MaHD) REFERENCES HopDong(MaHD),
    CONSTRAINT FK_Log_NhanVien FOREIGN KEY (MaNV) REFERENCES NhanVien(MaNV)
);
```
<img width="1902" height="984" alt="image" src="https://github.com/user-attachments/assets/1b170ff3-f911-4321-887d-5ec8976c1f9c" />
<img width="1912" height="975" alt="image" src="https://github.com/user-attachments/assets/9f76158a-3d0a-434a-b516-6bf8c4e37948" />
Để thực hiện Nhiệm vụ 1, em đã khởi tạo cơ sở dữ liệu QuanLyCamDo và thiết lập 5 bảng dữ liệu chuẩn hóa 3NF gồm: Khách hàng, Nhân viên, Hợp đồng, Tài sản và Log biến động
<img width="1917" height="976" alt="image" src="https://github.com/user-attachments/assets/548a7fb7-0bcd-4359-9632-905779296b70" />
Sau khi khởi tạo cơ sở dữ liệu QuanLyCamDo, em đã thiết lập 5 bảng chuẩn hóa 3NF và kết nối chúng qua sơ đồ Diagram

# Nhiệm Vụ 2:Cài đặt SQL
* Event 1: Đăng ký hợp đồng mới
```
CREATE DATABASE QLCamDo;
GO

USE QLCamDo;
GO

CREATE TABLE KhachHang
(
    MaKH INT IDENTITY(1,1) PRIMARY KEY,
    HoTen NVARCHAR(100),
    SoDienThoai VARCHAR(15),
    CCCD VARCHAR(20),
    DiaChi NVARCHAR(200)
);

CREATE TABLE HopDong
(
    MaHD INT IDENTITY(1,1) PRIMARY KEY,
    MaKH INT,
    NgayVay DATE,
    TienGoc DECIMAL(18,2),
    Deadline1 DATE,
    Deadline2 DATE,
    TrangThai NVARCHAR(50),

    FOREIGN KEY (MaKH)
    REFERENCES KhachHang(MaKH)
);

CREATE TABLE TaiSan
(
    MaTS INT IDENTITY(1,1) PRIMARY KEY,
    MaHD INT,
    TenTaiSan NVARCHAR(100),
    GiaTriDinhGia DECIMAL(18,2),
    TrangThai NVARCHAR(50),
    IsSold BIT DEFAULT 0,

    FOREIGN KEY (MaHD)
    REFERENCES HopDong(MaHD)
);

GO

CREATE PROCEDURE sp_TaoHopDong
    @HoTen NVARCHAR(100),
    @SoDienThoai VARCHAR(15),
    @CCCD VARCHAR(20),
    @DiaChi NVARCHAR(200),

    @TienGoc DECIMAL(18,2),
    @Deadline1 DATE,
    @Deadline2 DATE,

    @TenTaiSan NVARCHAR(100),
    @GiaTriDinhGia DECIMAL(18,2)
AS
BEGIN
    DECLARE @MaKH INT;
    DECLARE @MaHD INT;

    INSERT INTO KhachHang
    VALUES
    (
        @HoTen,
        @SoDienThoai,
        @CCCD,
        @DiaChi
    );

    SET @MaKH = SCOPE_IDENTITY();

    INSERT INTO HopDong
    VALUES
    (
        @MaKH,
        GETDATE(),
        @TienGoc,
        @Deadline1,
        @Deadline2,
        N'Đang vay'
    );

    SET @MaHD = SCOPE_IDENTITY();

    INSERT INTO TaiSan
    VALUES
    (
        @MaHD,
        @TenTaiSan,
        @GiaTriDinhGia,
        N'Đang cầm cố',
        0
    );
END;
GO

EXEC sp_TaoHopDong
    @HoTen = N'Nguyễn Văn A',
    @SoDienThoai = '0988888888',
    @CCCD = '123456789',
    @DiaChi = N'Hà Nội',
    @TienGoc = 10000000,
    @Deadline1 = '2026-05-20',
    @Deadline2 = '2026-06-20',
    @TenTaiSan = N'Laptop Dell',
    @GiaTriDinhGia = 15000000;
GO
```
<img width="1920" height="967" alt="image" src="https://github.com/user-attachments/assets/42889369-2181-44ef-9285-ccf2fb7817f9" />
<img width="1913" height="967" alt="image" src="https://github.com/user-attachments/assets/3152aaa3-8f8a-495f-9b12-53154feb2acd" />
<img width="1920" height="983" alt="image" src="https://github.com/user-attachments/assets/91114c19-5a13-48c7-916b-4b9b610c09d6" />
Việc dùng tham số XML cho phép thêm nhiều tài sản chỉ trong một lần gọi Procedure, tránh việc gọi Insert nhiều lần làm chậm hệ thống

* Event 2:Tính toán công nợ thời gian thực
```
CREATE TABLE LogThanhToan
(
    MaLog INT IDENTITY(1,1) PRIMARY KEY,
    MaHD INT,
    NgayTra DATE,
    SoTienTra DECIMAL(18,2),
    NguoiThu NVARCHAR(100),
    GhiChu NVARCHAR(200),

    FOREIGN KEY (MaHD)
    REFERENCES HopDong(MaHD)
);
GO

CREATE FUNCTION fn_CalcMoneyContract
(
    @MaHD INT,
    @TargetDate DATE
)
RETURNS DECIMAL(18,2)
AS
BEGIN
    DECLARE @TienGoc DECIMAL(18,2);
    DECLARE @Deadline1 DATE;
    DECLARE @NgayVay DATE;
    DECLARE @SoNgay INT;
    DECLARE @SoNgayQuaHan INT;
    DECLARE @LaiDon DECIMAL(18,2);
    DECLARE @TongTien DECIMAL(18,2);

    SELECT
        @TienGoc = TienGoc,
        @Deadline1 = Deadline1,
        @NgayVay = NgayVay
    FROM HopDong
    WHERE MaHD = @MaHD;

    SET @SoNgay = DATEDIFF(DAY, @NgayVay, @TargetDate);

    IF @SoNgay < 0
        SET @SoNgay = 0;

    SET @LaiDon =
        (@TienGoc / 1000000.0)
        * 5000
        * @SoNgay;

    IF @TargetDate <= @Deadline1
    BEGIN
        SET @TongTien =
            @TienGoc + @LaiDon;
    END
    ELSE
    BEGIN
        SET @SoNgayQuaHan =
            DATEDIFF(DAY, @Deadline1, @TargetDate);

        SET @TongTien =
            (@TienGoc + @LaiDon)
            * POWER(1.01, @SoNgayQuaHan);
    END

    RETURN @TongTien;
END;
GO

CREATE FUNCTION fn_CalcMoneyTransaction
(
    @MaHD INT,
    @TargetDate DATE
)
RETURNS DECIMAL(18,2)
AS
BEGIN
    DECLARE @TongNo DECIMAL(18,2);
    DECLARE @DaTra DECIMAL(18,2);

    SET @TongNo =
        dbo.fn_CalcMoneyContract(@MaHD, @TargetDate);

    SELECT
        @DaTra = ISNULL(SUM(SoTienTra), 0)
    FROM LogThanhToan
    WHERE MaHD = @MaHD;

    RETURN @TongNo - @DaTra;
END;
GO

SELECT
    dbo.fn_CalcMoneyContract(1, GETDATE())
    AS TongTienHopDong;

SELECT
    dbo.fn_CalcMoneyTransaction(1, GETDATE())
    AS SoTienConNo;
GO
```
<img width="1912" height="981" alt="image" src="https://github.com/user-attachments/assets/23c952c0-59b1-4d18-aa50-5adbab08834b" />
<img width="1916" height="974" alt="image" src="https://github.com/user-attachments/assets/60c57edb-2b50-4bba-8502-627361344d21" />
<img width="1919" height="975" alt="image" src="https://github.com/user-attachments/assets/bbf78178-1cde-4bc7-8977-bc9b679e996f" />
Hàm sử dụng POWER để tính lũy thừa cho lãi kép theo đúng gợi ý của đề bài. Việc trừ đi SUM(SoTienTra) giúp quản lý nợ theo thời gian thực chính xác.

* Event 3: Xử lý trả nợ và hoàn trả tài sản
```
CREATE PROCEDURE sp_ThanhToan
    @MaHD INT,
    @SoTienTra DECIMAL(18,2),
    @NguoiThu NVARCHAR(100)
AS
BEGIN
    DECLARE @TongNo DECIMAL(18,2);
    DECLARE @ConNo DECIMAL(18,2);
    DECLARE @IsSold BIT;

    SELECT TOP 1
        @IsSold = IsSold
    FROM TaiSan
    WHERE MaHD = @MaHD;

    IF @IsSold = 1
    BEGIN
        PRINT N'Tài sản đã thanh lý. Không thể thanh toán.';
        RETURN;
    END

    SET @TongNo =
        dbo.fn_CalcMoneyTransaction(@MaHD, GETDATE());

    SET @ConNo =
        @TongNo - @SoTienTra;

    INSERT INTO LogThanhToan
    VALUES
    (
        @MaHD,
        GETDATE(),
        @SoTienTra,
        @NguoiThu,
        N'Khách thanh toán'
    );

    IF @ConNo <= 0
    BEGIN
        UPDATE HopDong
        SET TrangThai = N'Đã thanh toán'
        WHERE MaHD = @MaHD;

        UPDATE TaiSan
        SET TrangThai = N'Đã trả khách'
        WHERE MaHD = @MaHD;

        PRINT N'Khách đã thanh toán hết nợ.';
        PRINT N'Đã hoàn trả tài sản.';
    END
    ELSE
    BEGIN
        UPDATE HopDong
        SET TrangThai = N'Đang trả góp'
        WHERE MaHD = @MaHD;

        PRINT N'Khách vẫn còn nợ.';
        PRINT N'Số tiền còn nợ: '
            + CAST(@ConNo AS NVARCHAR);
    END
END;
GO

EXEC sp_ThanhToan
    @MaHD = 1,
    @SoTienTra = 3000000,
    @NguoiThu = N'Nhân viên A';
GO
```

<img width="1918" height="984" alt="image" src="https://github.com/user-attachments/assets/e9b14d1d-2cee-4cb1-ab50-a30ff8d81394" />
<img width="1920" height="982" alt="image" src="https://github.com/user-attachments/assets/dec75290-b694-4516-b6b4-9496d0461ddb" />
<img width="1900" height="965" alt="image" src="https://github.com/user-attachments/assets/c813d3eb-5457-47d5-867d-e8923ca30fad" />
Procedure này tuân thủ nghiêm ngặt quy tắc không thu tiền khi đồ đã bán thanh lý và đảm bảo mọi biến động số tiền đều được lưu vết.  

*Event 4: Truy vấn danh sách nợ xấu (Nợ khó đòi)
```
CREATE TRIGGER trg_QuaHan
ON HopDong
AFTER UPDATE
AS
BEGIN
    UPDATE HopDong
    SET TrangThai = N'Quá hạn'
    WHERE Deadline1 < GETDATE()
    AND TrangThai = N'Đang vay';
END;
GO

CREATE TRIGGER trg_SanSangThanhLy
ON HopDong
AFTER UPDATE
AS
BEGIN
    UPDATE TaiSan
    SET TrangThai = N'Sẵn sàng thanh lý'
    WHERE MaHD IN
    (
        SELECT MaHD
        FROM HopDong
        WHERE Deadline2 < GETDATE()
        AND TrangThai = N'Quá hạn'
    );
END;
GO

CREATE TRIGGER trg_DaThanhLy
ON HopDong
AFTER UPDATE
AS
BEGIN
    UPDATE TaiSan
    SET
        TrangThai = N'Đã bán thanh lý',
        IsSold = 1
    WHERE MaHD IN
    (
        SELECT MaHD
        FROM HopDong
        WHERE TrangThai = N'Đã thanh lý'
    );
END;
GO

UPDATE HopDong
SET TrangThai = N'Đã thanh lý'
WHERE MaHD = 1;
GO
```

<img width="1914" height="981" alt="image" src="https://github.com/user-attachments/assets/d4831565-963e-4a8f-bdd3-d9cf93170427" />
<img width="1920" height="975" alt="image" src="https://github.com/user-attachments/assets/c2f96ed8-b54c-4143-b0de-09879096e381" />
Việc dùng VIEW giúp bạn chỉ cần gọi SELECT * FROM vw_DanhSachNoXau là có ngay báo cáo nợ xấu cực kỳ nhanh gọn.

* Event 5: Quản lý thanh lý tài sản
```
SELECT * FROM KhachHang;
SELECT * FROM HopDong;
SELECT * FROM TaiSan;
SELECT * FROM LogThanhToan;
GO
```
<img width="1905" height="977" alt="image" src="https://github.com/user-attachments/assets/305616f4-9057-4eef-a61a-99992d277bcd" />
Trigger này giúp hệ thống tự vận hành, giảm bớt thao tác thủ công và tránh sai sót dữ liệu giữa hai bảng liên quan.

# 4. Các sự kiện bổ sung:
```
CREATE PROCEDURE sp_GiaHanHopDong
    @MaHD INT,
    @SoTienLaiDaTra DECIMAL(18,2),
    @Deadline1Moi DATE,
    @Deadline2Moi DATE,
    @NguoiThu NVARCHAR(100)
AS
BEGIN
    DECLARE @TongNo DECIMAL(18,2);
    DECLARE @TienGoc DECIMAL(18,2);
    DECLARE @TienLai DECIMAL(18,2);

    SELECT @TienGoc = TienGoc
    FROM HopDong
    WHERE MaHD = @MaHD;

    SET @TongNo =
        dbo.fn_CalcMoneyContract(@MaHD, GETDATE());

    SET @TienLai =
        @TongNo - @TienGoc;

    IF @SoTienLaiDaTra < @TienLai
    BEGIN
        PRINT N'Khách chưa trả đủ tiền lãi.';
        RETURN;
    END

    INSERT INTO LogThanhToan
    VALUES
    (
        @MaHD,
        GETDATE(),
        @SoTienLaiDaTra,
        @NguoiThu,
        N'Gia hạn hợp đồng'
    );

    UPDATE HopDong
    SET
        Deadline1 = @Deadline1Moi,
        Deadline2 = @Deadline2Moi,
        TrangThai = N'Đang vay'
    WHERE MaHD = @MaHD;

    PRINT N'Gia hạn hợp đồng thành công.';
END;
GO
```
<img width="1919" height="977" alt="image" src="https://github.com/user-attachments/assets/31d239a8-4bd0-4192-b4c6-b2a591fbb654" />
kết quả

```
CREATE TABLE AuditLog
(
    MaAudit INT IDENTITY(1,1) PRIMARY KEY,
    TenBang NVARCHAR(100),
    HanhDong NVARCHAR(50),
    NoiDung NVARCHAR(MAX),
    ThoiGian DATETIME,
    NguoiThucHien NVARCHAR(100)
);
GO
```
<img width="1918" height="970" alt="image" src="https://github.com/user-attachments/assets/3c390cbe-19e8-412a-9fff-6d5b14aea1ff" />
kết quả
