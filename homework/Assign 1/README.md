# BÁO CÁO BÀI TẬP NHÓM

## Môn học: Công nghệ truyền thông đa phương tiện

**Buổi báo cáo:** Assignment 1  
**Tên chủ đề:** Cisco's VOIP basic configuration  
**GVHD:** Đỗ Thị Hương Lan  
**Ngày thực hiện:** 10/03/2025  

---

## THÔNG TIN CHUNG

**LỚP:** NT536.P12  

| STT | Họ và tên            | MSSV      | Email                      |
|----|------------------|----------|--------------------------|
| 1  | Đinh Huỳnh Gia Bảo | 22520101 | 22520101@gm.uit.edu.vn  |
| 2  | Dương Quốc Hưng    | 22520501 | 22520501@gm.uit.edu.vn  |
| 3  | Trương Duy Khôi    | 22520702 | 22520702@gm.uit.edu.vn  |
| 4  | Nguyễn Xuân Cường  | 22520178 | 22520178@gm.uit.edu.vn  |

---

## ĐÁNH GIÁ KHÁC

- **Tổng thời gian thực hiện:** 1 ngày  
- **Link Video thực hiện:** *Chưa có*  
- **Ý kiến:**  
  - Khó khăn  
  - Đề xuất  
- **Điểm tự đánh giá:** 10/10  

---

## BÁO CÁO CHI TIẾT

### Yêu cầu:

Triển khai mô hình cho phép gọi nội bộ giữa các điện thoại IP (IP Phone), đồng thời thu thập các gói dữ liệu liên quan đến các giao thức thường có trong một mô hình VOIP (*DHCP, TFTP, SCCP, SIP, RTP,...*).

![Mô hình yêu cầu](/asset/voip-topology.png)

### Bước 1: Chuẩn bị thiết bị

#### **Các thiết bị cần sử dụng:**

- 1 Router Cisco 2811 (*Call Manager Express*)
- 1 Switch Cisco 2960-24TT
- 1 Sniffer
- 4 IP Phones Cisco 7960
- **Dây cáp kết nối:**  
  - *Straight-through* (cáp thẳng) để kết nối các thiết bị khác loại

#### **Kết nối thiết bị:**

1. **Kết nối Router với Sniffer**

    - Cắm một đầu vào cổng FastEthernet 0/0 trên Router 2811.
    - Cắm đầu còn lại vào cổng Ethernet 0 trên Sniffer.


2. **Kết nối Sniffer với Switch**

    - Cắm một đầu vào cổng Ethernet 1 trên Sniffer.
    - Cắm đầu còn lại vào cổng FastEthernet 0/1 trên Switch 2960.


3. **Kết nối Switch với các IP Phones**

    - Kết nối từ cổng FastEthernet 0/2 - 0/5 trên Switch đến cổng FastEthernet trên từng điện thoại IP Phone.

---

### Bước 2: Cấu hình IP cho Router và DHCP Server

#### Cấu hình IP cho interface FastEthernet 0/0

- Chuyển sang chế độ Privileged EXEC, cho phép chúng ta có quyền
truy cập và cấu hình router.

```cisco
Router> enable
```
-  Chuyển vào chế độ Global Configuration. Đây là chế độ
mà trong đó toàn bộ cấu hình của bộ định tuyến được điều chỉnh

```cisco
Router# configure terminal
```

- Vào chế độ cấu hình của giao diện FastEthernet 0/0

```cisco
Router(config)# interface FastEthernet0/0
```

- Gán địa chỉ IP 192.168.1.1 với subnet mask 255.255.255.0 cho interface FastEthernet0/0

```cisco
Router(config-if)# ip address 192.168.1.1 255.255.255.0
```

- Bật interface
```cisco
Router(config-if)# no shutdown
```

- Thoát khỏi chế độ Global Configuration
```cisco
Router(config-if)# exit
```

-  Lưu lại cấu hình đang chạy vào NVRAM để không bị mất khi router khởi động lại
```cisco
Router(config)# do wr
```

#### Tiếp theo, ta cần xác định rằng DHCP Server trên router sẽ chịu trách nhiệm cấp phát địa chỉ IP cho các IP Phone kết nối vào mạng:

-  Kích hoạt dịch vụ DHCP trên router
```cisco
Router(config)# service dhcp
```

- Tạo một pool DHCP có tên là VOICE- POOL
```cisco
Router(config)# ip dhcp pool VOICE-POOL
```

- Xác định phạm vi địa chỉ IP mà DHCP sẽ cấp phát.
```cisco
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
```

- Chỉ định địa chỉ Default Gateway cho các thiết bị trong mạng
```cisco
Router(dhcp-config)# default-router 192.168.1.1
```

- Chỉ định địa chỉ IP của TFTP Server. Ở đây, Router Cisco 2811 cũng đóng vai trò TFTP Server
```cisco
Router(dhcp-config)# option 150 ip 192.168.1.1
```

- Quay lại chế độ Global Configuration
```cisco
Router(dhcp-config)# exit
```

- Lưu lại cấu hình đang chạy vào NVRAM để không bị mất khi router khởi động lại
```cisco
Router(config)# do wr
```

---

### Bước 3: Cấu hình Call Manager Express

- Kích hoạt chế độ cấu hình dịch vụ điện thoại IP  (Call Manager Express) trên Router
```cisco
Router(config)# telephony-service
```

- Xác định tối đa 5 Directory Numbers (DN, số điện thoại nội bộ của hệ thống VoIP, mỗi DN tương ứng với một số điện thoại của IP Phone) có thể được sử dụng
```cisco
Router(config-telephony)# max-dn 5
```

- Xác định tối đa 5 IP Phones (ePhone) có thể đăng ký vào hệ thống.
```cisco
Router(config-telephony)# max-ephones 5
```

- Đặt địa chỉ 192.168.1.1 (địa chỉ của Router) để quản lý cuộc gọi cho các IP Phone. Cổng 2000 là cổng giao tiếp sử dụng SCCP (Skinny Client Control Protocol) để giao tiếp với IP Phone
```cisco
Router(config-telephony)# ip source-address 192.168.1.1 port 2000
```

-  Tự động gán các số điện thoại từ DN 1 đến DN 5 cho ePhone khi chúng đăng ký vào hệ thống
```cisco
Router(config-telephony)# auto assign 1 to 5
```
- Lưu lại cấu hình đang chạy vào NVRAM để không bị mất khi router khởi động lại
```cisco
Router(config)# do wr
```

---

### Bước 4: Thiết lập VLAN Voice trên Switch

Trong cách cấu hình này, chúng ta sẽ **tách biệt lưu lượng** thoại và dữ liệu trong các mạng VLAN khác nhau trên Switch Cisco 2960-24TT. Các gói dữ liệu sẽ được truyền trên mạng VLAN truy cập

- Chuyển từ chế độ EXEC người dùng sang Privileged EXEC để thực hiện cấu hình.
```cisco
Switch> enable
```

- Chuyển switch sang chế độ Global Configuration
```cisco
Switch# configure terminal
```

- Chọn các cổng từ FastEthernet 0/1 đến FastEthernet 0/5 để cấu hình đồng thời.
```cisco
Switch(config)# interface range fa0/1-5
```

- Cấu hình các cổng thành chế độ Access Mode (chỉ thuộc về một VLAN duy nhất).
```cisco
Switch(config-if-range)# switchport mode access
```

- Chỉ định VLAN 1 làm VLAN Voice trên các cổng này
```cisco
Switch(config-if-range)# switchport voice vlan 1
```

- Thoát switch khỏi chế độ Global Configuration.
```cisco
Switch(config-if-range)# exit
```

-  Lưu lại cấu hình đang chạy vào NVRAM để không bị mất khi switch khởi động lại.
```cisco
Switch(config)# do wr
```

---

### Bước 5: Cấu hình số điện thoại cho IP Phone trên Router Cisco 2811

- Tạo Directory Number số 1
```cisco
Router(config)# ephone-dn 1
```

- Gán số điện thoại 54001 cho ephone-dn 1
```cisco
Router(config-ephone-dn)# number 54001
```

- Thoát khỏi chế độ cấu hình DN và quay lại chế độ Global Configuration
```cisco
Router(config-ephone-dn)# exit
```

- Tương tự cho các phần phía sau
```cisco
Router(config)# ephone-dn 2
Router(config-ephone-dn)# number 54002
Router(config-ephone-dn)# exit

Router(config)# ephone-dn 3
Router(config-ephone-dn)# number 54003
Router(config-ephone-dn)# exit

Router(config)# ephone-dn 4
Router(config-ephone-dn)# number 54004
Router(config-ephone-dn)# exit
Router(config)# do wr
```

---

### Bước 6: Kiểm tra kết quả

#### **Danh sách IP và số điện thoại nội bộ của IP Phone**

| Thiết bị   | Địa chỉ IP      | Line Number |
|-----------|---------------|-------------|
| IP Phone 0 | 192.168.1.3   | 54004       |
| IP Phone 1 | 192.168.1.4   | 54001       |
| IP Phone 2 | 192.168.1.2   | 54002       |
| IP Phone 3 | 192.168.1.5   | 54003       |

#### **Thu thập gói tin từ Sniffer**

| STT | Giao thức | Ý nghĩa |
|----|---------|---------|
| 1  | SCCP    | Giao thức điều khiển IP Phone với Call Manager |
| 2  | DHCP    | Cung cấp địa chỉ IP động cho IP Phone |

- Minh chứng gói tin SCCP
![Minh chứng SCCP](/asset/sccp-packet.png)

- Minh chứng gói tin DHCP
![Minh chứng DHCP](/asset/dhcp-packet.png)

**Link báo cáo chi tiết:** [Xem trên Google Drive](https://drive.google.com/file/d/1YqRJB__jni3UAuQggY4ziK_KFhD6TtYr/view?usp=sharing)
