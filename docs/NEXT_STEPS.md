# Việc còn lại sau khi Duy hoàn tất topology

Nguồn chuẩn: `topology.pkt` tại merge commit `62181f9` và chín running-config
trong `packet-tracer/configs/`.

## Đã hoàn thành và có bằng chứng

- [x] Topology vật lý bốn tầng đã dựng và lưu bằng Packet Tracer 9.0.1.
- [x] Bốn router có đúng 10 interface IP; tất cả cổng đang dùng đều `up/up`.
- [x] Backbone dùng `172.90.255.0/29`, R1-R4 lần lượt dùng `.1-.4`.
- [x] Có đủ 18 static route: R1/R2 mỗi router 4, R3/R4 mỗi router 5.
- [x] Không còn RIP/OSPF/EIGRP trong running-config.
- [x] Có năm DHCP helper cùng trỏ đến `172.90.40.2`.
- [x] Cả bốn router ping DHCP-SRV thành công 5/5.
- [x] DHCP-SRV dùng `172.90.40.2/28`, gateway `.40.1`, DNS `.40.3`.
- [x] DHCP Service On và có đủ năm pool.
- [x] Client đã nhận DHCP thành công theo xác nhận của Khôi.
- [x] Website source có đủ họ tên, MSSV và XX = 90.
- [x] Chín running-config đã export và hai nhánh DuyVu/Khoi đã đồng bộ artifact.
- [x] 13 ảnh router/helper/DHCP đã được đưa vào `report/images/`.

## Ưu tiên 1 - Xác nhận nốt thông số service

Owner: Khôi. Bắt buộc thao tác trong Packet Tracer.

- [ ] DHCP-SRV > Services > DHCP > chọn `POOL-STAFF`, chụp C05C và ghi Start IP.
- [ ] Chọn `POOL-MEETING`, chụp C05D và ghi Start IP.
- [ ] DNS-SRV > Desktop > IP Configuration, chụp C06A; phải là
  `172.90.40.3/28`, gateway `172.90.40.1`.
- [ ] DNS-SRV > Services > DNS, chụp C06B; cần A record `mmt-90.com ->
  172.90.40.4` và CNAME `www.mmt-90.com -> mmt-90.com`.
- [ ] WEB-SRV > Desktop > IP Configuration và Services > HTTP, chụp C07A; phải
  dùng `172.90.40.4/28`, gateway `.40.1`, DNS `.40.3`, HTTP On.
- [ ] AP-STAFF > Config > wireless interface, chụp C08; cần SSID
  `MMT-90-STAFF`, WPA2-PSK/AES.
- [ ] AP-MEETING > Config > wireless interface, chụp C09; cần SSID
  `MMT-90-MEETING`, WPA2-PSK/AES.

Không cần để lộ passphrase trong report; ảnh chỉ cần chứng minh security mode.

## Ưu tiên 2 - Chụp DHCP của năm subnet

Trên một client đại diện mỗi mạng, chọn DHCP rồi chạy `ipconfig /all`.

| Evidence | Mạng đúng | Mask | Gateway | DNS |
|---|---|---|---|---|
| T06A | `172.90.10.0/28` | `255.255.255.240` | `172.90.10.1` | `172.90.40.3` |
| T06B | `172.90.11.0/27` | `255.255.255.224` | `172.90.11.1` | `172.90.40.3` |
| T06C | `172.90.20.0/28` | `255.255.255.240` | `172.90.20.1` | `172.90.40.3` |
| T06D | `172.90.21.0/28` | `255.255.255.240` | `172.90.21.1` | `172.90.40.3` |
| T06E/T12 | `172.90.30.0/27` | `255.255.255.224` | `172.90.30.1` | `172.90.40.3` |

Nếu một client nhận `169.254.x.x`, DHCP chưa thành công. Nếu nhận `172.90.x.x`
nhưng sai third octet/gateway, client đang ở sai segment hoặc pool chọn sai.

## Ưu tiên 3 - Chạy test chức năng T01-T12

Thực hiện đúng bảng `docs/TEST_MATRIX.md` và đặt ảnh theo
`report/SCREENSHOT_INDEX.md`.

Thứ tự nhanh nhất:

1. T01: ADMIN ping `172.90.10.1`.
2. T02: ADMIN ping một TECH client.
3. T03: MGMT ping WEB-SRV `172.90.40.4`.
4. T04: MEETING wireless client ping ADMIN client.
5. T05: STAFF wireless client ping ADMIN client.
6. T06A-E/T12: năm ảnh `ipconfig /all` ở mục trên.
7. T07: `nslookup mmt-90.com` phải trả `172.90.40.4`.
8. T08: `nslookup www.mmt-90.com` phải trả `172.90.40.4`.
9. T09A-E: mở `http://www.mmt-90.com` từ một client của mỗi subnet.
10. T10A-D đã có và PASS.
11. T11: trên R1 chạy `ping 172.90.255.4`.

Ping đầu tiên đôi khi timeout do ARP; chạy lại trước khi kết luận FAIL.

## Ưu tiên 4 - Ảnh overview và Simulation Mode

- [ ] D01: toàn cảnh topology đủ label tầng, hostname và subnet.
- [ ] C02: zoom phần SW-CORE cho thấy bốn link router màu xanh.
- [ ] SIM-DHCP: chuyển Simulation, lọc DHCP, renew một ADMIN client và chụp
  chuỗi Discover/Offer/Request/ACK.
- [ ] SIM-DNS-HTTP: lọc DNS/ARP/TCP/HTTP, mở `www.mmt-90.com` và chụp event list.

## Ưu tiên 5 - Hoàn thiện dữ liệu report trên Prism

- [ ] Copy bảng địa chỉ từ `docs/ADDRESSING_PLAN.md`.
- [ ] Copy phần giải thích routing/DHCP/DNS từ `report/REPORT_DRAFT_CONTENT.md`.
- [ ] Chèn ảnh theo thứ tự trong `report/SCREENSHOT_INDEX.md` và dùng caption có
  sẵn.
- [ ] Điền Actual/Status thật vào `docs/TEST_MATRIX.md` sau mỗi test.
- [ ] Dùng ba issue thật trong `docs/ISSUES.md`; bổ sung timestamp/ảnh nếu có.
- [ ] Ghi Packet Tracer version `9.0.1.858` trong report.
- [ ] Xuất PDF và mở lại kiểm tra ảnh, font, domain, MSSV.

## Ưu tiên 6 - Demo và nộp

- [ ] Quay video 6-10 phút theo `demo/SCRIPT.md`; cả Duy và Khôi đều có audio.
- [ ] Upload video và thử link bằng cửa sổ ẩn danh.
- [ ] Điền link vào `demo/demo-link.txt`.
- [ ] Save, đóng và mở lại `topology.pkt`; chạy T01, T07 và T09 làm smoke test.
- [ ] Xác nhận deadline và quy tắc tên zip với LMS/giảng viên.
- [ ] Đảm bảo bản nộp không còn `HUMAN_REQUIRED`, `Chưa chạy` hoặc `CẦN CHỤP`.

## Việc Codex không thể tự làm

- Thao tác click trong Packet Tracer và save trạng thái GUI.
- Chạy/quan sát client DHCP, DNS, Browser và Simulation Mode thay người dùng.
- Chụp ảnh màn hình chưa tồn tại hoặc tạo bằng chứng giả.
- Truy cập report Prism, quay giọng hai thành viên hoặc upload video.
- Xác nhận deadline/quy tắc tên zip nằm ngoài repository.
