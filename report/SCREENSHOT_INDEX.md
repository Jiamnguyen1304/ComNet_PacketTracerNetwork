# Manifest ảnh nghiệm thu cuối

Bộ ảnh được chụp từ `topology.pkt` cuối ngày 29-30/08/2026. Có 51 ảnh PNG duy
nhất sau khi loại bản sao `T06E-meeting-dhcp - Copy.png`. Prism phải dùng tên
file để ánh xạ, không hiển thị tên file thô trong caption cuối.

## 1. Topology và host tĩnh

| File | Nội dung | Vị trí report | Trạng thái |
|---|---|---|---|
| `D01-topology.png` | Overview bốn tầng và SW-CORE | Kiến trúc tổng thể | Có/PASS |
| `D01-F1.png` | ADMIN và STAFF tầng 1 | Chi tiết topology | Có/PASS |
| `D01-F2.png` | TECH, MGMT và R2 tầng 2 sau sửa port | Chi tiết topology; Issue TECH/MGMT | Có/PASS |
| `D01-F3.png` | MEETING WLAN tầng 3 | Chi tiết topology | Có/PASS |
| `D01-F4.png` | 7 PC + 3 server tầng 4 | Chi tiết topology | Có/PASS |
| `C02-backbone.png` | Bốn nhánh router về SW-CORE | Thiết kế backbone | Có/PASS |
| `C10A-SRV-PC01-static.png` | SRV-PC01 `.40.5/28`, GW `.40.1`, DNS `.40.3` | SERVER subnet | Có/PASS |
| `C10B-SRV-PC01-local-ping.png` | Ping gateway và ba server cùng subnet | SERVER subnet | Có/PASS |

## 2. Router, static route và DHCP relay

| File | Nội dung | Dùng cho | Trạng thái |
|---|---|---|---|
| `C01-T10A-R1-interface-route.png` | R1 interface `up/up`, 4 static route | Static routing, T10 | Có/PASS |
| `C01-T10B-R2-interface-route.png` | R2 interface `up/up`, 4 static route | Static routing, T10 | Có/PASS |
| `C01-T10C-R3-interface-route.png` | R3 interface `up/up`, 5 static route | Static routing, T10 | Có/PASS |
| `C01-T10D-R4-interface-route.png` | R4 interface `up/up`, 5 static route | Static routing, T10 | Có/PASS |
| `C04A-R1-dhcp-helper.png` | Hai helper R1 về `.40.2` | DHCP relay | Có/PASS |
| `C04B-R2-dhcp-helper.png` | Hai helper R2 về `.40.2` | DHCP relay | Có/PASS |
| `C04C-R3-dhcp-helper.png` | Một helper R3 về `.40.2` | DHCP relay | Có/PASS |
| `T11-r1-to-r4-backbone.png` | R1 ping R4 backbone `.255.4` 5/5 | T11 | Có/PASS |
| `S01A-R1-to-dhcp.png` | R1 ping DHCP-SRV 5/5 | Routing tới dịch vụ | Có/PASS |
| `S01B-R2-to-dhcp.png` | R2 ping DHCP-SRV 5/5 | Routing tới dịch vụ | Có/PASS |
| `S01C-R3-to-dhcp.png` | R3 ping DHCP-SRV 5/5 | Routing tới dịch vụ | Có/PASS |
| `S01D-R4-to-dhcp.png` | R4 ping DHCP-SRV 5/5 | Routing tới dịch vụ | Có/PASS |

## 3. DHCP, DNS, WEB và Wi-Fi

| File | Nội dung | Vị trí report | Trạng thái |
|---|---|---|---|
| `C05A-dhcp-server-ip.png` | DHCP-SRV `.40.2/28` | DHCP Server | Có/PASS |
| `C05B-dhcp-pools.png` | DHCP On và năm custom pool | DHCP Server | Có/PASS |
| `C05C-pool-staff.png` | Chi tiết POOL-STAFF | DHCP pool | Có/PASS |
| `C05D-pool-meeting.png` | Chi tiết POOL-MEETING | DHCP pool | Có/PASS |
| `C06A-dns-server-ip.png` | DNS-SRV `.40.3/28` | DNS | Có/PASS |
| `C06B-dns-records.png` | A record và CNAME | DNS | Có/PASS |
| `C07A-web-server-ip.png` | WEB-SRV `.40.4/28` | WEB | Có/PASS |
| `C07B-web-http_1.png` | Một phần cấu hình HTTP/index | WEB | Có/PASS |
| `C07B-web-http_2.png` | Phần còn lại của HTTP/index | WEB | Có/PASS |
| `C08-ap-staff.png` | SSID STAFF, WPA2-PSK/AES | Wi-Fi | Có/PASS |
| `C09-ap-meeting.png` | SSID MEETING, WPA2-PSK/AES | Wi-Fi | Có/PASS |

Không có file C07C riêng. Dẫn chiếu `T09C-web-tech.png` để chứng minh nội dung
website được truy cập bằng domain.

## 4. Test matrix T01-T12

| File | Nội dung | Test | Trạng thái |
|---|---|---|---|
| `T01-admin-to-gateway.png` | ADMIN ping `.10.1` | T01 | Có/PASS |
| `T02-admin-to-tech.png` | ADMIN ping TECH `.20.x` | T02 | Có/PASS |
| `T03-mgmt-to-web.png` | MGMT `.21.x` ping WEB `.40.4` | T03 | Có/PASS |
| `T04-meeting-to-admin.png` | MEETING ping ADMIN | T04 | Có/PASS |
| `T05-staff-to-admin.png` | STAFF gửi ICMP/PDU tới ADMIN | T05 | Có/PASS |
| `T06A-admin-dhcp.png` | ADMIN nhận `.10.x/28` | T06 | Có/PASS |
| `T06B-staff-dhcp.png` | STAFF nhận `.11.x/27` | T06 | Có/PASS |
| `T06C-tech-dhcp.png` | TECH nhận `.20.x/28` | T06; retest port R2 | Có/PASS |
| `T06D-mgmt-dhcp.png` | MGMT nhận `.21.x/28` | T06; retest port R2 | Có/PASS |
| `T06E-meeting-dhcp.png` | MEETING nhận `.30.x/27` | T06 và T12 | Có/PASS |
| `T07-dns-a.png` | `mmt-90.com -> .40.4` | T07 | Có/PASS |
| `T08-dns-cname.png` | `www.mmt-90.com -> .40.4` | T08 | Có/PASS |
| `T09A-web-admin.png` | Website từ ADMIN | T09 | Có/PASS |
| `T09B-web-staff.png` | Website từ STAFF | T09 | Có/PASS |
| `T09C-web-tech.png` | Website từ TECH | T09; nội dung WEB | Có/PASS |
| `T09D-web-mgmt.png` | Website từ MGMT | T09 | Có/PASS |
| `T09E-web-meeting.png` | Website từ MEETING | T09 | Có/PASS |

T10 dùng bốn ảnh C01; T11 dùng `T11-r1-to-r4-backbone.png`; T12 dùng lại
`T06E-meeting-dhcp.png`.

## 5. Simulation và smoke test

| File | Nội dung | Trạng thái |
|---|---|---|
| `SIM-DHCP.png` | DHCP Discover, Offer, Request, ACK qua relay | Có/PASS |
| `SIM-DNS-HTTP.png` | DNS tới `.40.3`, sau đó TCP/HTTP tới `.40.4` | Có/PASS |
| `S02-final-smoke.png` | Dịch vụ vẫn hoạt động sau khi mở lại topology | Có/PASS |

## 6. Quy tắc đưa vào report

- Mỗi ảnh duy nhất chèn một lần; nơi khác dùng cross-reference.
- Nhóm D01-F1..F4, C01A-D, S01A-D, T06A-E và T09A-E thành subfigure/lưới.
- Ảnh CLI phải đủ lớn để đọc; không kéo méo tỷ lệ.
- Crop passphrase ở C08/C09 nếu đang hiển thị nhưng giữ SSID và security mode.
- Caption nêu thao tác, kết quả và ý nghĩa; không chỉ lặp lại tên file.
- Không đưa `.gitkeep`, ảnh debug hoặc bản sao vào Prism.
