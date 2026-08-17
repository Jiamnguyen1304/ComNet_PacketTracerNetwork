# Sơ đồ logic

## Bản nháp kết nối

| Thiết bị A | Port A | Thiết bị B | Port B | Segment |
|---|---|---|---|---|
| R1 | G0/0 | SW-HC | G0/1 | ADMIN |
| SW-HC | Fa0/1 | PC-HC-01 | Fa0 | ADMIN |
| SW-HC | Fa0/2 | PC-HC-02 | Fa0 | ADMIN |
| R1 | G0/1 | AP-STAFF | Port0 | STAFF |
| R1 | G0/2 | SW-CORE | Fa0/1 | BACKBONE |
| R2 | G0/0 | SW-KT | G0/1 | TECH |
| SW-KT | Fa0/1 | PC-KT-01 | Fa0 | TECH |
| SW-KT | Fa0/2 | PC-KT-02 | Fa0 | TECH |
| R2 | G0/1 | SW-LD | G0/1 | MGMT |
| SW-LD | Fa0/1 | PC-LD-01 | Fa0 | MGMT |
| R2 | G0/2 | SW-CORE | Fa0/2 | BACKBONE |
| R3 | G0/0 | AP-MEETING | Port0 | MEETING |
| R3 | G0/1 | SW-CORE | Fa0/3 | BACKBONE |
| R4 | G0/0 | SW-SRV | G0/1 | SERVER |
| SW-SRV | Fa0/1 | DHCP-SRV | Fa0 | SERVER |
| SW-SRV | Fa0/2 | DNS-SRV | Fa0 | SERVER |
| SW-SRV | Fa0/3 | WEB-SRV | Fa0 | SERVER |
| R4 | G0/1 | SW-CORE | Fa0/4 | BACKBONE |

## Quy tắc vẽ bản chính

- Sơ đồ phải do nhóm tự vẽ, không dùng ảnh tham khảo trong đề làm bản nộp.
- Ghi hostname ngay dưới biểu tượng thiết bị.
- Ghi port ở hai đầu mỗi cable.
- Ghi network/prefix trên từng segment.
- Ghi IP router ở đầu router của segment.
- Tách trực quan bốn tầng bằng vùng hoặc nhãn, không dùng VLAN để mô phỏng vùng.
- Ghi domain, SSID và dịch vụ cạnh server/AP tương ứng.
- Xuất ảnh độ phân giải đủ đọc khi chèn vào PDF.
