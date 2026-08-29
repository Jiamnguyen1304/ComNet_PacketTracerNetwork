# Ma trận kiểm thử cuối

Ma trận được cập nhật sau lần retest topology cuối ngày 30/08/2026. Trạng thái
PASS dựa trên kết quả người dùng đã xác nhận và ảnh thật trong `report/images/`.

| ID | Source | Destination/chức năng | Thao tác | Kết quả thực tế | Evidence | Status |
|---|---|---|---|---|---|---|
| T01 | ADMIN-PC01 | `172.90.10.1` | Ping local gateway | Nhận reply, không mất gói | `T01-admin-to-gateway.png` | PASS |
| T02 | ADMIN-PC01 | TECH-PC01 thuộc `172.90.20.0/28` | Ping liên subnet | Nhận reply sau khi sửa ánh xạ cổng R2 | `T02-admin-to-tech.png` | PASS |
| T03 | MGMT-PC01 thuộc `172.90.21.0/28` | WEB-SRV `172.90.40.4` | Ping liên subnet | Nhận reply, không mất gói | `T03-mgmt-to-web.png` | PASS |
| T04 | MEETING-LAPTOP01 | ADMIN-PC01 | Ping từ WLAN qua routing | Nhận reply, không mất gói | `T04-meeting-to-admin.png` | PASS |
| T05 | STAFF-PHONE01 | ADMIN-PC01 | ICMP/Simple PDU từ WLAN | PDU thành công | `T05-staff-to-admin.png` | PASS |
| T06 | Client đại diện ADMIN, STAFF, TECH, MGMT, MEETING | DHCP tập trung | `ipconfig /all` hoặc IP Configuration | Đúng subnet, mask, gateway và DNS `172.90.40.3` | `T06A-admin-dhcp.png` đến `T06E-meeting-dhcp.png` | PASS |
| T07 | TECH-PC01 | `mmt-90.com` | DNS lookup | Phân giải về `172.90.40.4` | `T07-dns-a.png` | PASS |
| T08 | TECH-PC01 | `www.mmt-90.com` | DNS lookup CNAME | Phân giải cuối về `172.90.40.4` | `T08-dns-cname.png` | PASS |
| T09 | Client đại diện năm mạng động | `http://www.mmt-90.com` | Web Browser bằng domain | Trang nhóm hiển thị từ cả năm subnet | `T09A-web-admin.png` đến `T09E-web-meeting.png` | PASS |
| T10 | R1-R4 | Bảng định tuyến | `show ip interface brief`, `show ip route` | Interface dùng đều `up/up`; R1/R2 có 4 dòng S, R3/R4 có 5 dòng S | `C01-T10A-R1-interface-route.png` đến `C01-T10D-R4-interface-route.png` | PASS |
| T11 | R1 | R4 backbone `172.90.255.4` | Ping backbone | Thành công 5/5 | `T11-r1-to-r4-backbone.png` | PASS |
| T12 | MEETING-LAPTOP01 | POOL-MEETING | DHCP | Nhận IP thuộc `172.90.30.4-.23/27`, gateway `.30.1`, DNS `.40.3` | `T06E-meeting-dhcp.png` | PASS |

## Simulation Mode và kiểm tra lưu trạng thái

| ID | Nội dung | Kết quả | Evidence | Status |
|---|---|---|---|---|
| SIM-DHCP | Discover, Offer, Request, ACK từ ADMIN qua R1 tới DHCP-SRV | Quan sát được luồng DHCP relay | `SIM-DHCP.png` | PASS |
| SIM-DNS-HTTP | DNS tới `.40.3`, sau đó TCP/HTTP tới `.40.4` từ TECH | Quan sát được đúng thứ tự dịch vụ | `SIM-DNS-HTTP.png` | PASS |
| S02 | Smoke test sau khi save, đóng và mở lại `topology.pkt` | Dịch vụ bằng domain vẫn hoạt động | `S02-final-smoke.png` | PASS |

## Kết luận nghiệm thu

- TECH nhận `172.90.20.x/28` qua `R2/G0/0 -> SW-KT`.
- MGMT nhận `172.90.21.x/28` qua `R2/G0/1 -> SW-LD`.
- Năm subnet động nhận đúng DHCP và DNS.
- Static routing, DNS, HTTP và hai WLAN hoạt động xuyên suốt.
- T01-T12, hai luồng Simulation và smoke test cuối đều có ảnh minh chứng.
