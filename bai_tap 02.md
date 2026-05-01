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

# 



