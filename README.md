# Đồ án mạng văn phòng 4 tầng

Đồ án môn Mạng máy tính sử dụng Cisco Packet Tracer 8.2 trở lên. Nhóm thiết kế
mạng cho một văn phòng 4 tầng, triển khai static routing, DHCP tập trung, DNS,
WEB và hai mạng Wi-Fi bảo mật WPA2-PSK.

## Bắt đầu từ đây

1. Điền thông tin nhóm và giá trị `XX` trong
   [PROJECT_INFO.md](docs/PROJECT_INFO.md).
2. Đọc [IMPLEMENTATION_GUIDE.md](docs/IMPLEMENTATION_GUIDE.md).
3. Chốt [ADDRESSING_PLAN.md](docs/ADDRESSING_PLAN.md).
4. Tạo `packet-tracer/topology.pkt` bằng Packet Tracer.
5. Export running-config vào `packet-tracer/configs/`.
6. Hoàn thiện báo cáo và đặt bản cuối tại `report/Report.pdf`.
7. Thay nội dung `demo/demo-link.txt` bằng link video thật.

## Cấu trúc thư mục

```text
Packet_tracer_project/
|-- README.md
|-- ARCHITECTURE.md
|-- docs/
|   |-- IMPLEMENTATION_GUIDE.md    Hướng dẫn chi tiết hơn 3.000 dòng
|   |-- PROJECT_INFO.md            Thông tin nhóm, XX, domain và phiên bản
|   |-- PROJECT_ANALYSIS.md        Phân tích yêu cầu từ đề bài
|   |-- ADDRESSING_PLAN.md         Bảng subnet và IP
|   |-- DEVICE_LIST.md             Danh sách thiết bị
|   |-- LOGICAL_TOPOLOGY.md        Bảng kết nối và port
|   |-- TEST_MATRIX.md             Ma trận 12 bài kiểm thử
|   |-- WORK_PLAN.md               Phân công cho 2 thành viên
|   `-- ISSUES.md                  Lỗi gặp phải và cách xử lý
|-- packet-tracer/
|   |-- topology.pkt               Tạo khi bắt đầu dựng mô hình
|   |-- configs/                   Running-config của router và switch
|   `-- web/index.html             Nội dung website trên WEB-SRV
|-- report/
|   |-- REPORT_TEMPLATE.md         Khung nội dung báo cáo
|   |-- SCREENSHOT_INDEX.md        Danh sách ảnh cần chụp
|   |-- images/                    Sơ đồ và ảnh minh chứng
|   `-- Report.pdf                 Bản báo cáo cuối
`-- demo/
    |-- SCRIPT.md                  Kịch bản video 6-10 phút
    `-- demo-link.txt              Link video demo
```

`topology.pkt` và `Report.pdf` chưa được tạo ở lần khởi tạo vì đây phải là file
thật xuất từ Packet Tracer và phần mềm soạn báo cáo, không dùng file rỗng giả.

## Thiết kế chính

| Khu vực | Network | Gateway | Cấp IP |
|---|---|---|---|
| Hành chính tầng 1 | `172.XX.10.0/27` | `172.XX.10.1` | DHCP |
| Wi-Fi tầng 1 | `172.XX.20.0/27` | `172.XX.20.1` | DHCP |
| Kỹ thuật tầng 2 | `172.XX.30.0/28` | `172.XX.30.1` | DHCP |
| Lãnh đạo tầng 2 | `172.XX.40.0/28` | `172.XX.40.1` | DHCP |
| Wi-Fi phòng họp tầng 3 | `172.XX.50.0/27` | `172.XX.50.1` | DHCP |
| Server tầng 4 | `172.XX.60.0/28` | `172.XX.60.1` | Static |
| Backbone | `172.XX.255.248/29` | Không áp dụng | Static |

## Quy tắc bắt buộc

- Không dùng VLAN.
- Không dùng RIP, OSPF hoặc dynamic routing.
- Mỗi subnet nối vào một cổng router riêng.
- Client tầng 1-3 phải nhận IP bằng DHCP.
- DHCP đặt tập trung ở tầng 4 và phục vụ 5 pool qua `ip helper-address`.
- Website phải truy cập bằng `www.mmt-XX.com`, không dùng IP khi nghiệm thu.
- Hai SSID phải dùng Access Point-PT và WPA2-PSK.
- Không dùng Home Wireless Router/WRT300N.

## File cần hoàn thiện trước khi nộp

- `packet-tracer/topology.pkt`.
- Chín file trong `packet-tracer/configs/` không còn dòng Placeholder.
- `report/Report.pdf`.
- Ảnh minh chứng trong `report/images/`.
- `demo/demo-link.txt` chứa link xem được.
- Mọi `TODO` và `XX` trong bản nộp đã được thay đúng.

Repository đã được khởi tạo trên nhánh `main` và có thể dùng cho lần push GitHub
đầu tiên sau khi nhóm kiểm tra nội dung.

