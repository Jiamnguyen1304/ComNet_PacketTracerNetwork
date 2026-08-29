# Sơ đồ logic theo topology hiện tại

## Kết nối hạ tầng đã xác nhận

| Thiết bị A | Port A | Thiết bị B | Port B | Segment |
|---|---|---|---|---|
| R1 | G0/0 | SW-HC | G0/1 | ADMIN |
| R1 | G0/1 | AP-STAFF | Port0 | STAFF |
| R1 | G0/2 | SW-CORE | Fa0/1 | BACKBONE |
| R2 | G0/0 | SW-KT | Fa0/6 | TECH |
| R2 | G0/1 | SW-LD | Fa0/6 | MGMT |
| R2 | G0/2 | SW-CORE | Fa0/4 | BACKBONE |
| R3 | G0/0 | AP-MEETING | Port0 | MEETING |
| R3 | G0/1 | SW-CORE | Fa0/3 | BACKBONE |
| R4 | G0/0 | SW-SRV | G0/1 | SERVER |
| SW-SRV | Fa0/1 | DHCP-SRV | Fa0 | SERVER |
| SW-SRV | Fa0/2 | DNS-SRV | Fa0 | SERVER |
| SW-SRV | Fa0/3 | WEB-SRV | Fa0 | SERVER |
| R4 | G0/1 | SW-CORE | Fa0/4 | BACKBONE |

Ba port phía switch của R2 được xác nhận sau lần đổi dây cuối bằng CDP. Các port
switch còn lại là allocation logic của topology; khi report không cần chứng minh
port access cụ thể thì chỉ dẫn chiếu interface router và segment để tránh tuyên
bố quá mức từ running-config không có description.

## Cụm endpoint đang thể hiện trên canvas

| Tầng | Nhánh | Thiết bị đầu cuối | Cấp IP |
|---:|---|---|---|
| 1 | `R1/G0/0 -> SW-HC` | Cụm PC hành chính có dây | DHCP, subnet ADMIN |
| 1 | `R1/G0/1 -> AP-STAFF` | Laptop/smartphone Wi-Fi dùng để kiểm thử | DHCP, subnet STAFF |
| 2 | `R2/G0/0 -> SW-KT` | Cụm PC kỹ thuật có dây | DHCP, subnet TECH |
| 2 | `R2/G0/1 -> SW-LD` | Cụm PC lãnh đạo có dây | DHCP, subnet MGMT |
| 3 | `R3/G0/0 -> AP-MEETING` | Laptop/smartphone phòng họp | DHCP, subnet MEETING |
| 4 | `R4/G0/0 -> SW-SRV` | Cụm PC/host có dây và ba Server-PT | Static, subnet SERVER |

Khi kiểm thử và viết caption, dùng display name cuối trong `DEVICE_LIST.md` cùng
IP thực tế hiển thị trên ảnh; không dùng các tên minh họa cũ `PC-HC-01`,
`PC-KT-01` hoặc `PC-LD-01`.

## Quy tắc vẽ bản chính

- Sơ đồ phải do nhóm tự vẽ, không dùng ảnh tham khảo trong đề làm bản nộp.
- Ghi hostname ngay dưới biểu tượng thiết bị.
- Ghi port ở hai đầu mỗi cable.
- Ghi network/prefix trên từng segment.
- Ghi IP router ở đầu router của segment.
- Tách trực quan bốn tầng bằng vùng hoặc nhãn, không dùng VLAN để mô phỏng vùng.
- Ghi domain, SSID và dịch vụ cạnh server/AP tương ứng.
- Vì bố cục hiện tại trải dài theo chiều dọc, dùng một ảnh overview cho cấu trúc
  và bốn ảnh zoom riêng cho tầng 1-4; không cố ép mọi label vào một ảnh nhỏ.
