# LAB 2 – MÃ HÓA ĐỐI XỨNG HIỆN ĐẠI

**Họ tên:** Trần Minh Nhật
**MSSV:** 1150080068
**Lớp:** 11CNPM1
**Học phần:** An toàn và Bảo mật Thông tin

Chương trình mã hóa / giải mã dữ liệu bằng các thuật toán đối xứng **DES, TripleDES, AES-GCM, RC4, Twofish** và hai bài nâng cao **AES-CTR (Java)**, **AES-CBC (Python)**. Giao diện Java Swing (NetBeans + Maven), quản lý tài khoản và lưu lịch sử thao tác trên **SQL Server**.

---

## 1. Cấu trúc thư mục

```
LAB2_1150080068_TranMinhNhat/
├── LAB2_MaHoaHienDai/            # Project Java NetBeans (Maven)
│   ├── pom.xml
│   └── src/main/java/week_02/
│       ├── CipherUtil.java       # Tiện ích: UTF-8, Base64, HEX, số ngẫu nhiên, PBKDF2
│       ├── FileTextIO.java       # Mở / lưu tệp .txt
│       ├── DbConfig.java         # Thông tin kết nối SQL Server
│       ├── HistoryDao.java       # Ghi / đọc bảng dbo.crypto_history
│       ├── UserDao.java          # Đăng ký / đăng nhập (dbo.app_users)
│       ├── DESCipher.java        # DES/CBC/PKCS5Padding
│       ├── TripleDESCipher.java  # DESede/CBC/PKCS5Padding
│       ├── AESCipher.java        # AES-256-GCM, khóa từ PBKDF2
│       ├── RC4Cipher.java        # RC4, bản mã dạng HEX
│       ├── TwofishCipher.java    # Twofish/CBC/PKCS7 (BouncyCastle)
│       ├── AESCTRCipher.java     # AES-256-CTR (bài nâng cao 01)
│       └── frm_*.java / frm_*.form   # Các JFrame Form
├── SQL/
│   ├── 01_Tao_CSDL.sql           # Tạo CSDL LAB2_MaHoaHienDai và các bảng
│   ├── 02_Tao_Tai_Khoan_SQL.sql  # Tạo login lab2_user và cấp quyền
│   ├── 03_Kiem_Tra_Du_Lieu.sql   # Kiểm tra dữ liệu app_users, crypto_history
│   └── database.sql              # script database đầy đủ
│
├── LAB2_Python/
│   └── aes_cbc_gui.py            # AES-256-CBC, giao diện Tkinter (bài nâng cao 02)
└── README.md
```

| Form | Chức năng |
|---|---|
| `frm_Menu` | Cửa sổ chính, mở các bài |
| `frm_KetNoiSQL` | Kết nối SQL Server |
| `frm_LichSu` | Xem lịch sử thao tác |
| `frm_DES`, `frm_TripleDES` | Bài 2.1, 2.2 |
| `frm_AES` | Bài 2.3 – Đăng ký, đăng nhập, mã hóa AES-GCM |
| `frm_RC4`, `frm_Twofish` | Bài 2.4, 2.5 |
| `frm_AES_CTR` | Bài 2.6 – Nâng cao 01 |

## 2. Yêu cầu phần mềm

- JDK 17 hoặc 21
- Apache NetBeans 20 trở lên (lần build đầu cần Internet để Maven tải thư viện `mssql-jdbc`, `bcprov-jdk18on`)
- SQL Server Express + SSMS
- Python 3.10 trở lên, thư viện `cryptography`

## 3. Chuẩn bị SQL Server

1. **SQL Server Configuration Manager** → Protocols for SQLEXPRESS → bật **TCP/IP**, mục IPAll đặt **TCP Port = 14333** (để trống TCP Dynamic Ports) → Restart dịch vụ SQL Server (SQLEXPRESS).
2. **SSMS** → Server Properties → Security → chọn **SQL Server and Windows Authentication mode**.
3. Kết nối SSMS bằng Windows Authentication, lần lượt chạy:
   - `SQL/01_Tao_CSDL.sql`
   - `SQL/02_Tao_Tai_Khoan_SQL.sql`: trước khi chạy, **thay mật khẩu trong script bằng mật khẩu do giảng viên cung cấp**. Mật khẩu không được đưa lên GitHub.

## 4. Chạy chương trình Java

1. NetBeans → **File → Open Project** → chọn thư mục `LAB2_MaHoaHienDai`.
2. Nhấp phải project → **Clean and Build** (Shift+F11), chờ `BUILD SUCCESS`.
3. Nhấp phải project → **Run** (F6). Cửa sổ `frm_Menu` hiện ra.
4. Bấm **Kết nối SQL Server** → nhập Server `127.0.0.1`, Port `14333`, Database `LAB2_MaHoaHienDai`, User `lab2_user` và mật khẩu → **Test Connection**.
5. Mở từng bài để mã hóa / giải mã. Bấm **Xem lịch sử** để xem các thao tác đã lưu.

> Bài 2.3 (AES): **Register** tài khoản mới → **Login** (nhận Ticket) → nhập Key AES → Encrypt / Decrypt.

## 5. Chạy chương trình Python (bài nâng cao 02)

```bash
py -m pip install cryptography
cd LAB2_Python
py aes_cbc_gui.py               # mở giao diện Tkinter
py aes_cbc_gui.py --self-test   # tự kiểm tra, kết quả: SELF-TEST OK
```

## 6. Dữ liệu kiểm thử

| Bài | Dữ liệu thử | Kết quả |
|---|---|---|
| 2.1 DES | "Xin chào Lab 2 - Mã hóa hiện đại!", Key `12345678` | Giải mã đúng; mỗi lần mã hóa ra bản mã khác (IV ngẫu nhiên) |
| 2.2 TripleDES | Key `123456789012345678901234` (24 ký tự) | Giải mã đúng; key 23 ký tự bị báo lỗi |
| 2.3 AES | Register + Login `sinhvien01` | Login có Ticket; `dbo.app_users`, `dbo.crypto_history` có dòng mới |
| 2.4 RC4 | Plaintext `Plaintext`, Key `Key` | Bản mã HEX `BBF316E8D940AF0AD3` |
| 2.5 Twofish | Key `Twofish@Lab2`, IV từ Generate IV | Giải mã đúng; Save/Open File khôi phục IV |
| 2.6 AES-CTR | Key + Nonce ngẫu nhiên | Độ dài bản mã = độ dài bản rõ |
| 2.6 AES-CBC (Python) | Văn bản dài | Giải mã đúng; `SELF-TEST OK` |

Ảnh minh chứng nằm trong thư mục `images/` và báo cáo Word.

## 7. Lỗi thường gặp

| Lỗi | Cách sửa |
|---|---|
| `Login failed for user 'lab2_user'` | Kiểm tra mật khẩu, bật Mixed Mode rồi Restart dịch vụ |
| `The TCP/IP connection ... port 14333 has failed` | Bật TCP/IP, đặt cổng 14333, Restart dịch vụ |
| `No suitable driver found` / `package org.bouncycastle does not exist` | Kiểm tra `pom.xml`, Clean and Build lại |
| `ModuleNotFoundError: No module named 'cryptography'` | `py -m pip install cryptography` |