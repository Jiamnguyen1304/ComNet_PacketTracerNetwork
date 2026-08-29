# Nội dung report as-built để đưa lên Prism

Tài liệu này phản ánh topology và bộ ảnh nghiệm thu cuối ngày 30/08/2026. Các
marker `[[IMAGE: ...]]` là vị trí Prism cần thay bằng đúng ảnh đã tải lên, không
phải nội dung để giữ trong bản PDF cuối.

## 1. Thông tin dự án

- Đề tài: Thiết kế và mô phỏng mạng văn phòng bốn tầng.
- Nền tảng: Cisco Packet Tracer 9.0.1 64-bit, build 9.0.1.858.
- Khối địa chỉ: `172.90.0.0/16`, với `XX = 90` lấy từ MSSV 25127190.
- Domain: `mmt-90.com`, website `www.mmt-90.com`.
- Thành viên:
  - Nguyễn Vũ Đức Duy — 25127190.
  - Nguyễn Minh Khôi — 25127389.

## 2. Phân công và mức đóng góp

| Thành viên | Nhiệm vụ và mức đóng góp thực tế |
|---|---|
| Nguyễn Vũ Đức Duy — 25127190 | Phân tích yêu cầu; lập address plan; dựng topology; cấu hình router, switch, interface và static routing; export running-config; soạn report trên Prism. |
| Nguyễn Minh Khôi — 25127389 | Cấu hình/kiểm tra DHCP, DNS, WEB, Wi-Fi; phát hiện và sửa ánh xạ TECH/MGMT; chạy T01-T12, Simulation, smoke test; quản lý ảnh và context report. |

Hai thành viên review chéo. File `.pkt` chỉ do một người chỉnh tại một thời
điểm; running-config, website và tài liệu được review qua Git.

## 3. Phân tích yêu cầu

Văn phòng gồm bốn tầng và sáu khu vực nghiệp vụ. Tầng 1 có mạng có dây ADMIN và
WLAN STAFF; tầng 2 có TECH và MGMT; tầng 3 có WLAN MEETING; tầng 4 có SERVER
subnet với ba server dịch vụ. Năm mạng tầng 1-3 dùng DHCP tập trung; toàn bộ
thiết bị tầng 4 dùng địa chỉ tĩnh.

Mỗi subnet nối một cổng router vật lý riêng. Bốn router dùng một Ethernet
backbone qua SW-CORE. Thiết kế không sử dụng VLAN, serial link, RIP, OSPF,
EIGRP hoặc WRT300N. Hai WLAN dùng Access Point-PT với WPA2-PSK/AES. Website được
nghiệm thu bằng domain thay vì truy cập trực tiếp IP.

## 4. Thiết kế địa chỉ

| Khu vực | Network | Mask | Gateway | Phương thức |
|---|---|---|---|---|
| ADMIN | `172.90.10.0/28` | `255.255.255.240` | `172.90.10.1` | DHCP |
| STAFF | `172.90.11.0/27` | `255.255.255.224` | `172.90.11.1` | DHCP |
| TECH | `172.90.20.0/28` | `255.255.255.240` | `172.90.20.1` | DHCP |
| MGMT | `172.90.21.0/28` | `255.255.255.240` | `172.90.21.1` | DHCP |
| MEETING | `172.90.30.0/27` | `255.255.255.224` | `172.90.30.1` | DHCP |
| SERVER | `172.90.40.0/28` | `255.255.255.240` | `172.90.40.1` | Static |
| BACKBONE | `172.90.255.0/29` | `255.255.255.248` | Không áp dụng | Static |

### Lý do subnetting

`/28` có 14 địa chỉ usable, đủ cho ADMIN với 10 client và gateway, TECH/MGMT
với tối đa 5 client, và SERVER với một gateway cùng tối đa 10 host. Vì ba
Server-PT được tính là host, tầng 4 giữ bảy PC `.40.5-.40.11`; các địa chỉ
`.40.12-.40.14` để dự phòng. `/27` có 30 địa chỉ usable, đáp ứng tối đa 20
thiết bị cho STAFF và MEETING. Backbone `/29` có sáu địa chỉ usable, đủ cho bốn
router và hai địa chỉ dự phòng.

### Địa chỉ server

| Server | IP | Gateway | DNS |
|---|---|---|---|
| DHCP-SRV | `172.90.40.2/28` | `172.90.40.1` | `172.90.40.3` |
| DNS-SRV | `172.90.40.3/28` | `172.90.40.1` | `172.90.40.3` |
| WEB-SRV | `172.90.40.4/28` | `172.90.40.1` | `172.90.40.3` |

## 5. Kiến trúc và thiết bị

Canvas được bố trí từ trên xuống là tầng 4, tầng 3, tầng 2 và tầng 1; SW-CORE
nằm bên phải trung tâm. R1 làm gateway ADMIN/STAFF; R2 làm gateway TECH/MGMT;
R3 làm gateway MEETING; R4 làm gateway SERVER. Bốn router nối SW-CORE bằng
backbone `172.90.255.0/29`, lần lượt dùng `.1`, `.2`, `.3`, `.4`.

Ánh xạ tầng 2 cuối là `R2/G0/0 -> SW-KT/TECH 172.90.20.0/28` và
`R2/G0/1 -> SW-LD/MGMT 172.90.21.0/28`.

[[IMAGE: D01-topology.png]]
[[IMAGE-GROUP: D01-F1.png, D01-F2.png, D01-F3.png, D01-F4.png]]
[[IMAGE: C02-backbone.png]]

Caption tổng quan: Topology văn phòng bốn tầng gồm bốn router 2911, năm switch
2960-24TT, hai Access Point-PT, ba Server-PT và bảy subnet độc lập.

`SRV-PC01` minh họa nhóm host tĩnh tầng 4 với IP `.40.5/28`, gateway `.40.1`
và DNS `.40.3`; host liên lạc được với gateway và ba server cùng subnet.

[[IMAGE-GROUP: C10A-SRV-PC01-static.png, C10B-SRV-PC01-local-ping.png]]

## 6. Static routing

Mỗi router có static route explicit tới mọi remote subnet. Next hop là địa chỉ
backbone của router trực tiếp sở hữu subnet đích. R1/R2 mỗi router có bốn static
route; R3/R4 mỗi router có năm, tổng cộng 18 route tĩnh.

Trong `show ip route`, `C` là connected network, `L` là local host route `/32`
của interface và `S` là static route. Dòng `Gateway of last resort is not set`
không phải lỗi: mô hình không có Internet và đã có route cụ thể tới mọi subnet.

[[IMAGE-GROUP: C01-T10A-R1-interface-route.png, C01-T10B-R2-interface-route.png, C01-T10C-R3-interface-route.png, C01-T10D-R4-interface-route.png]]
[[IMAGE: T11-r1-to-r4-backbone.png]]

Các interface đang dùng đều `up/up`; R1 ping R4 backbone `172.90.255.4` thành
công 5/5. Nhóm ảnh C01 đồng thời là bằng chứng T10.

## 7. DHCP tập trung và relay

DHCP-SRV `172.90.40.2` phục vụ năm custom pool. Vì DHCP Discover là broadcast,
năm LAN interface động trên R1-R3 cấu hình `ip helper-address 172.90.40.2`.
Router chuyển broadcast thành unicast và thêm thông tin gateway nguồn để server
chọn đúng pool.

[[IMAGE-GROUP: C04A-R1-dhcp-helper.png, C04B-R2-dhcp-helper.png, C04C-R3-dhcp-helper.png]]
[[IMAGE-GROUP: S01A-R1-to-dhcp.png, S01B-R2-to-dhcp.png, S01C-R3-to-dhcp.png, S01D-R4-to-dhcp.png]]

R1/R2 mỗi router có hai helper, R3 có một helper. Bốn router đều ping DHCP-SRV
thành công 5/5, xác nhận đường đi và đường về.

| Pool | Start–End | Mask | Gateway | DNS |
|---|---|---|---|---|
| POOL-ADMIN | `.10.4-.10.13` | `/28` | `.10.1` | `172.90.40.3` |
| POOL-STAFF | `.11.4-.11.23` | `/27` | `.11.1` | `172.90.40.3` |
| POOL-TECH | `.20.5-.20.9` | `/28` | `.20.1` | `172.90.40.3` |
| POOL-MGMT | `.21.5-.21.9` | `/28` | `.21.1` | `172.90.40.3` |
| POOL-MEETING | `.30.4-.30.23` | `/27` | `.30.1` | `172.90.40.3` |

`serverPool` là pool mặc định không xóa được của Server-PT nhưng không được dùng;
SERVER subnet chỉ có địa chỉ tĩnh.

[[IMAGE-GROUP: C05A-dhcp-server-ip.png, C05B-dhcp-pools.png, C05C-pool-staff.png, C05D-pool-meeting.png]]
[[IMAGE-GROUP: T06A-admin-dhcp.png, T06B-staff-dhcp.png, T06C-tech-dhcp.png, T06D-mgmt-dhcp.png, T06E-meeting-dhcp.png]]
[[IMAGE: SIM-DHCP.png]]

Năm client đại diện nhận đúng subnet, mask, gateway và DNS. T06E đồng thời là
bằng chứng T12 cho MEETING-LAPTOP01. Simulation thể hiện đúng DHCP Discover,
Offer, Request và ACK qua relay.

## 8. DNS, WEB và Wi-Fi

DNS-SRV có A record `mmt-90.com -> 172.90.40.4` và CNAME
`www.mmt-90.com -> mmt-90.com`. Client nhận DNS `172.90.40.3` từ DHCP.

[[IMAGE-GROUP: C06A-dns-server-ip.png, C06B-dns-records.png]]
[[IMAGE-GROUP: T07-dns-a.png, T08-dns-cname.png]]

WEB-SRV `172.90.40.4/28` bật HTTP; `index.html` hiển thị XX = 90, domain, họ tên
và MSSV hai thành viên.

[[IMAGE-GROUP: C07A-web-server-ip.png, C07B-web-http_1.png, C07B-web-http_2.png]]

AP-STAFF phát `MMT-90-STAFF`; AP-MEETING phát `MMT-90-MEETING`. Hai AP bridge
Layer 2, dùng WPA2-PSK/AES và không chạy NAT/routing/DHCP.

[[IMAGE-GROUP: C08-ap-staff.png, C09-ap-meeting.png]]
[[IMAGE: SIM-DNS-HTTP.png]]

Simulation từ TECH thể hiện DNS tới `.40.3`, sau đó TCP/HTTP tới `.40.4`.

## 9. Kiểm thử nghiệm thu

[[IMAGE-GROUP: T01-admin-to-gateway.png, T02-admin-to-tech.png, T03-mgmt-to-web.png, T04-meeting-to-admin.png, T05-staff-to-admin.png]]
[[IMAGE-GROUP: T09A-web-admin.png, T09B-web-staff.png, T09C-web-tech.png, T09D-web-mgmt.png, T09E-web-meeting.png]]
[[IMAGE: S02-final-smoke.png]]

T01-T05 xác nhận local gateway, inter-subnet routing và hai WLAN. T09A-E xác
nhận website `www.mmt-90.com` mở thành công từ ADMIN, STAFF, TECH, MGMT và
MEETING. `T09C-web-tech.png` đồng thời là minh chứng nội dung website; không có
file C07C riêng. Smoke test sau khi save, đóng và mở lại file vẫn thành công.

Chèn bảng cuối từ `docs/TEST_MATRIX.md`. T01-T12, hai luồng Simulation và smoke
test đều có evidence và trạng thái PASS.

## 10. Issues and Resolutions

Issue kỹ thuật nổi bật xảy ra tại tầng 2: client TECH từng nhận `.21.x` vì
SW-KT nối `R2/G0/1`, trong khi gateway TECH `.20.1` nằm trên G0/0. Sau khi đổi
uplink, yêu cầu DHCP đầu tiên failed vì năm lease trong mỗi pool vẫn bị client
nhánh đối diện giữ. Nhóm giữ nguyên address plan, nối `G0/0 -> SW-KT`,
`G0/1 -> SW-LD`, reset lease và cấp DHCP lại. Retest xác nhận TECH nhận `.20.x`,
MGMT nhận `.21.x`; T02, T03, DNS và HTTP đều PASS.

Hai issue nên trình bày thêm là tài liệu cũ lệch address plan as-built và
`serverPool` mặc định không thể xóa nhưng không ảnh hưởng năm custom pool. Nội
dung chi tiết nằm trong `docs/ISSUES.md`; khi dẫn chứng, cross-reference ảnh đã
chèn thay vì chèn trùng.

## 11. Kết luận và hướng phát triển

Topology cuối đáp ứng kiến trúc bốn tầng bằng bảy subnet vật lý riêng và static
routing. Bốn router có 18 static route, R1-R3 có năm DHCP helper, năm custom
pool cấp đúng địa chỉ, DNS A/CNAME và HTTP hoạt động, hai WLAN dùng WPA2-PSK/AES.
T01-T12, Simulation DHCP, Simulation DNS/HTTP và smoke test sau khi mở lại đều
có bằng chứng nghiệm thu.

Hướng phát triển có thể gồm redundancy, ACL, monitoring và IPv6. Đây chỉ là đề
xuất tương lai; không thêm VLAN hoặc dynamic routing vào topology hiện tại vì
trái ràng buộc của đề bài.
