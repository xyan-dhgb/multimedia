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

```cisco
Router> enable
Router# configure terminal
Router(config)# interface FastEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
Router(config)# do wr
```

#### **Cấu hình DHCP Server trên Router Cisco 2811**

```cisco
Router(config)# service dhcp
Router(config)# ip dhcp pool VOICE-POOL
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.1.1
Router(dhcp-config)# option 150 ip 192.168.1.1
Router(dhcp-config)# exit
Router(config)# do wr
```

---

### Bước 3: Cấu hình Call Manager Express

```cisco
Router(config)# telephony-service
Router(config-telephony)# max-dn 5
Router(config-telephony)# max-ephones 5
Router(config-telephony)# ip source-address 192.168.1.1 port 2000
Router(config-telephony)# auto assign 1 to 5
Router(config)# do wr
```

---

### Bước 4: Thiết lập VLAN Voice trên Switch

```cisco
Switch> enable
Switch# configure terminal
Switch(config)# interface range fa0/1-5
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport voice vlan 1
Switch(config-if-range)# exit
Switch(config)# do wr
```

---

### Bước 5: Cấu hình số điện thoại cho IP Phone trên Router Cisco 2811

```cisco
Router(config)# ephone-dn 1
Router(config-ephone-dn)# number 54001
Router(config-ephone-dn)# exit

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

**Link báo cáo chi tiết:** [Xem trên Google Drive](https://drive.google.com/file/d/1YqRJB__jni3UAuQggY4ziK_KFhD6TtYr/view?usp=sharing)
