# BÁO CÁO BÀI TẬP NHÓM

## Môn học: Công nghệ truyền thông đa phương tiện

**Buổi báo cáo:** Assignment 2  
**Tên chủ đề:** Analyzing SIP Protocol  
**GVHD:** Đỗ Thị Hương Lan  
**Ngày thực hiện:** 17/03/2025  

## THÔNG TIN CHUNG:

**LỚP:** NT536.P12  

| STT | Họ và tên            | MSSV      | Email                     |
|----|-----------------|----------|--------------------------|
| 1  | Đinh Huỳnh Gia Bảo | 22520101 | 22520101@gm.uit.edu.vn  |
| 2  | Dương Quốc Hưng   | 22520501 | 22520501@gm.uit.edu.vn  |
| 3  | Trương Duy Khôi   | 22520702 | 22520702@gm.uit.edu.vn  |
| 4  | Nguyễn Xuân Cường | 22520178 | 22520178@gm.uit.edu.vn  |

## ĐÁNH GIÁ KHÁC:

| **Nội dung** | **Kết quả** |
|--------------|-------------|
| Tổng thời gian thực hiện bài thực hành trung bình | 2 ngày (17/03/2025 – 18/03/2025) |
| Link Video thực hiện (nếu có) | |
| Ý kiến (nếu có) | |
| + Khó khăn | |
| + Đề xuất | |
| Điểm tự đánh giá | 10/10 |

---

# BÁO CÁO CHI TIẾT

## Câu 1: Thiết lập mô hình Tổng đài thoại (Có SIP Server, SIP Phones)

### Chuẩn bị:
- **VMware Workstation Pro 17**: Phần mềm ảo hóa và quản lý máy ảo.
- **FreePBX**: Giao diện đồ họa mã nguồn mở dựa trên web, dùng để quản lý Asterisk.
- **ZoiPer**: Softphone miễn phí để thực hiện cuộc gọi VoIP thông qua PBX.

### Cài đặt FreePBX:
- Truy cập trang web chính thức của FreePBX thông qua đường dẫn [FreePBX](https://www.freepbx.org/)

- Nhấn vào tab Download → Chọn file `SNG7-PBX16-64bit-2302-1.iso` để tải về bản cập nhật mới nhất.

- Mở VMware Workstation → New Virtual Machine → Custom (advanced)

- Ở hộp thoại **New Virtual Machine Wizard**, chọn **Workstation 17.x** ở mục Hardware.

- Nhấn Next, chọn **I will install the operating system later**.

- Chọn hệ điều hành:
  - **Guest operating system**: Linux  
  - **Version**: CentOS 8 64bit  

- Nhấn Next, đặt tên và chọn nơi lưu trữ thông tin máy ảo:
  - **Virtual machine name**: FreePBX  
  - **Location**: `D:\FREEPBX`  

- Nhấn Next, cấu hình bộ xử lý:
  - **Số lượng bộ xử lý**: 1  
  - **Số lõi trên mỗi bộ xử lý**: 2  

- Nhấn Next, chọn bộ nhớ RAM cho máy ảo. Ở đây mình chọn 2048MB, các bạn có thể chọn số lượng tùy theo nhu cầu của mình.

- Nhấn Next, chọn cấu hình mạng **Bridged**.

- Nhấn Next, chọn bộ xử lý input/output làlà **LSI Logic**.

- Nhấn Next, chọn loại đĩa **NVMe**.

- Nhấn Next, chọn dung lượng bộ nhớ **25GB**.

- Sau khi cấu hình xong, VMware sẽ hiển thị một hộp thoại cho phép chúng ta
xem lại các thông tin cấu hình của máy ảo. Nhấn vào Custom Hardware để
nạp file .iso và tùy chỉnh thêm về cấu hình mạng.
    - Chọn **New CD/DVD (IDE)** → **Use ISO image file** → Chọn file `.iso` đã tải về.
    - Chuyển sang **Network Adapter**, chọn **Bridged** và **Replicate physical network connection state**.

- Sau khi hoàn tất mọi cấu hình và cài đặt về mật khẩu, tiến hành đăng nhập vào FreePBX. Truy cập FreePBX qua trình duyệt tại `https://10.45.94.166`. (Tùy thuộc vào lớp mạng của bạn mà truy cập đúng địa chỉ IP)

### Cài đặt Zoiper:

- Truy cập trang web [Zoiper](https://www.zoiper.com/en/voip-softphone/download/current)

- Chọn phiên bản **Windows/Linux**
- **Windows**: Chạy trình cài đặt bình thường.
- **Linux**:
    -  Gỡ nén file:
  ```bash
  tar -xvf Zoiper5_5.6.6_x86_64.tar.xz
  ```
    -  Di chuyển vào Zoiper5:
  ```bash
  cd Zoiper5_5
  ```
    - Cấp quyền chạy cho file thực thi:
  ```bashbash
  chmod +x zoiper
  ```

    - Sau đó, chạy Zoiper:
   ```bashbash
  ./zoiper
  ```

## Câu 2: Thực hiện các thao tác phát sinh traffic

### Tạo SIP Extension trong FreePBX:
- **Đăng nhập FreePBX** → **Applications** → **Extensions**
- Thêm **New SIP [chan_pjsip] Extension**:
  - **Extension 1002**
  - **Extension 1003**

## Câu 3: Thu thập và phân tích gói tin bằng Wireshark

### Các loại thông điệp và cấu trúc:

- Chúng ta có 2 loại thông điệp: Yêu cầu và Phản hồi.

- Cấu trúc tổng quát của chúng được thể hiện như sau
    - Dòng khởi đầu (Start Line): Request-Line / Status-Line
    - Phần mào đầu/tiêu đề (Headers): Mô tả về phiên truyền thông
    - Phần thân (Message Body – Tùy chọn): SDP, ký tự, XML

#### **Yêu cầu (Request):**
- **Start Line**: `Request-Line`
- **Headers**:
  - `From`: Người gửi
  - `To`: Người nhận
  - `Call-ID`: Định danh duy nhất
  - `Contact`: Cách liên lạc
  - `CSeq`: Số thứ tự yêu cầu
  - `Max-Forwards`: Giới hạn số lần chuyển tiếp
  - `User-Agent`: Phiên bản FreePBX
  - `Content-Length`: Độ dài dữ liệu

#### **Phản hồi (Response):**
- **Start Line**: `Status-Line`
- **Headers**:
  - `Via`: Thông tin định tuyến
  - `Contact`: Cách liên lạc
  - `To`: Người nhận
  - `From`: Người gửi
  - `Call-ID`: Định danh cuộc đối thoại
  - `CSeq`: Số thứ tự yêu cầu
  - `Allow`: Phương thức SIP hỗ trợ
  - `Supported`: Các tính năng mở rộng
  - `User-Agent`: Phiên bản FreePBX
  - `Content-Length`: Độ dài dữ liệu

### Các loại bản tin SIP:
- **INVITE**: Bắt đầu cuộc gọi
- **BYE**: Kết thúc cuộc gọi
- **ACK**: Xác nhận cuộc gọi
- **OPTIONS**: Kiểm tra khả năng của server
- **REGISTER**: Đăng ký SIP với server
- **CANCEL**: Hủy yêu cầu INVITE

---

