# Screenshot Index cho report

Ảnh đã có được đặt trong `report/images/`. Trạng thái `CẦN CHỤP` là phần bắt
buộc con người thực hiện trong Packet Tracer; không tạo ảnh giả.

## 1. Thiết kế và cấu hình

| ID | Nội dung | File/evidence | Owner | Trạng thái |
|---|---|---|---|---|
| D01 | Toàn cảnh topology có label bốn tầng | `D01-topology.png` | Duy | CẦN CHỤP |
| C01A | R1 interface IP và trạng thái | `C01-T10A-R1-interface-route.png` | Duy | ĐÃ CÓ |
| C01B | R2 interface IP và trạng thái | `C01-T10B-R2-interface-route.png` | Duy | ĐÃ CÓ |
| C01C | R3 interface IP và trạng thái | `C01-T10C-R3-interface-route.png` | Duy | ĐÃ CÓ |
| C01D | R4 interface IP và trạng thái | `C01-T10D-R4-interface-route.png` | Duy | ĐÃ CÓ |
| C02 | Toàn cảnh bốn router nối SW-CORE, link xanh | `C02-backbone.png` | Duy | CẦN CHỤP |
| C03A-D | Static route R1-R4 | Dùng lại bốn ảnh C01/T10A-D | Duy | ĐÃ CÓ |
| C04A | Hai DHCP helper của R1 | `C04A-R1-dhcp-helper.png` | Khôi | ĐÃ CÓ |
| C04B | Hai DHCP helper của R2 | `C04B-R2-dhcp-helper.png` | Khôi | ĐÃ CÓ |
| C04C | Một DHCP helper của R3 | `C04C-R3-dhcp-helper.png` | Khôi | ĐÃ CÓ |
| C05A | Static IP của DHCP-SRV | `C05A-dhcp-server-ip.png` | Khôi | ĐÃ CÓ |
| C05B | Danh sách năm DHCP pool | `C05B-dhcp-pools.png` | Khôi | ĐÃ CÓ |
| C05C | Chi tiết POOL-STAFF: start, mask, max | `C05C-pool-staff.png` | Khôi | CẦN CHỤP |
| C05D | Chi tiết POOL-MEETING: start, mask, max | `C05D-pool-meeting.png` | Khôi | CẦN CHỤP |
| C06A | Static IP của DNS-SRV | `C06A-dns-server-ip.png` | Khôi | CẦN CHỤP |
| C06B | DNS A record và CNAME | `C06B-dns-records.png` | Khôi | CẦN CHỤP |
| C07A | Static IP và HTTP On của WEB-SRV | `C07A-web-server.png` | Khôi | CẦN CHỤP |
| C07B | Website hiển thị qua domain | `C07B-web-content.png` | Khôi | CẦN CHỤP |
| C08 | SSID và WPA2 của AP-STAFF | `C08-ap-staff.png` | Khôi | CẦN CHỤP |
| C09 | SSID và WPA2 của AP-MEETING | `C09-ap-meeting.png` | Khôi | CẦN CHỤP |

## 2. Reachability đã có

| ID | Nội dung | File | Trạng thái |
|---|---|---|---|
| S01A | R1 ping DHCP-SRV `172.90.40.2`, 5/5 | `S01A-R1-to-dhcp.png` | ĐÃ CÓ |
| S01B | R2 ping DHCP-SRV `172.90.40.2`, 5/5 | `S01B-R2-to-dhcp.png` | ĐÃ CÓ |
| S01C | R3 ping DHCP-SRV `172.90.40.2`, 5/5 | `S01C-R3-to-dhcp.png` | ĐÃ CÓ |
| S01D | R4 ping DHCP-SRV `172.90.40.2`, 5/5 | `S01D-R4-to-dhcp.png` | ĐÃ CÓ |

## 3. Nghiệm thu T01-T12

| ID | Ảnh cần thể hiện | File | Trạng thái |
|---|---|---|---|
| T01 | PC ADMIN ping gateway `172.90.10.1` | `T01-admin-to-gateway.png` | CẦN CHỤP |
| T02 | PC ADMIN ping PC TECH | `T02-admin-to-tech.png` | CẦN CHỤP |
| T03 | PC MGMT ping WEB-SRV `172.90.40.4` | `T03-mgmt-to-web.png` | CẦN CHỤP |
| T04 | Laptop MEETING ping PC ADMIN | `T04-meeting-to-admin.png` | CẦN CHỤP |
| T05 | Laptop STAFF ping PC ADMIN | `T05-staff-to-admin.png` | CẦN CHỤP |
| T06A | `ipconfig /all` của ADMIN | `T06A-admin-dhcp.png` | CẦN CHỤP |
| T06B | `ipconfig /all` của STAFF | `T06B-staff-dhcp.png` | CẦN CHỤP |
| T06C | `ipconfig /all` của TECH | `T06C-tech-dhcp.png` | CẦN CHỤP |
| T06D | `ipconfig /all` của MGMT | `T06D-mgmt-dhcp.png` | CẦN CHỤP |
| T06E | `ipconfig /all` của MEETING | `T06E-meeting-dhcp.png` | CẦN CHỤP |
| T07 | `nslookup mmt-90.com` trả `172.90.40.4` | `T07-dns-a.png` | CẦN CHỤP |
| T08 | `nslookup www.mmt-90.com` trả `172.90.40.4` | `T08-dns-cname.png` | CẦN CHỤP |
| T09A-E | Website mở bằng `http://www.mmt-90.com` từ năm subnet | `T09A-E-web-*.png` | CẦN CHỤP |
| T10A-D | `show ip route` trên R1-R4 | Dùng bốn ảnh C01/T10A-D | ĐÃ CÓ/PASS |
| T11 | R1 ping backbone R4 `172.90.255.4` | `T11-r1-to-r4-backbone.png` | CẦN CHỤP |
| T12 | Laptop MEETING nhận IP `172.90.30.0/27` | Có thể dùng lại T06E | CẦN CHỤP |

## 4. Simulation Mode

| ID | Luồng cần thấy | File | Trạng thái |
|---|---|---|---|
| SIM-DHCP | Discover, Offer, Request, ACK qua R1 tới `172.90.40.2` | `SIM-DHCP.png` | CẦN CHỤP |
| SIM-DNS-HTTP | DNS query/response rồi TCP/HTTP tới `172.90.40.4` | `SIM-DNS-HTTP.png` | CẦN CHỤP |

## 5. Caption sẵn dùng cho ảnh đã có

- **C01A/T10A:** R1 có ba interface đang dùng ở trạng thái `up/up`; route table
  chứa hai connected LAN, backbone và bốn static route tới các subnet từ xa.
- **C01B/T10B:** R2 có ba interface `up/up` và bốn static route tới hai LAN tầng
  1, mạng phòng họp và server room.
- **C01C/T10C:** R3 có hai interface `up/up`, cổng G0/2 không dùng được shutdown
  và có năm static route tới các LAN còn lại.
- **C01D/T10D:** R4 có hai interface `up/up`, năm static route quay về toàn bộ
  client subnet và không dùng dynamic routing.
- **C04A-C:** R1, R2 và R3 có tổng cộng năm `ip helper-address 172.90.40.2`, cho
  phép chuyển DHCP broadcast của năm client subnet tới DHCP-SRV tập trung.
- **C05A:** DHCP-SRV dùng IP tĩnh `172.90.40.2/28`, gateway `172.90.40.1` và DNS
  `172.90.40.3`.
- **C05B:** DHCP Service đang On và có đủ năm pool ADMIN, STAFF, TECH, MGMT và
  MEETING với gateway/DNS riêng tương ứng.
- **S01A-D:** Cả bốn router ping DHCP-SRV thành công 5/5, chứng minh đường đi và
  đường về giữa backbone và server subnet hoạt động.
