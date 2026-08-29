# Danh sách ảnh Khôi cần bàn giao cho report

Duy viết report trên Prism; Khôi chạy toàn bộ phần Packet Tracer và cung cấp ảnh
thật. Tất cả ảnh đặt trong `report/images/`. Bộ ảnh cũ đã được xóa theo yêu cầu
ngày 29/08/2026, vì vậy **không có ảnh nào được mặc định PASS**. Mọi hàng dưới
đây đều phải chạy và chụp lại từ đầu trên `topology.pkt` cuối.

Không tạo ảnh giả, không dùng ảnh `before-fix` và không đánh PASS nếu chưa quan
sát trực tiếp kết quả. Nếu test lỗi, ảnh chẩn đoán phải có hậu tố `-debug` và
không được dùng làm ảnh nghiệm thu.

## 1. Thứ tự chụp bắt buộc

Thực hiện theo thứ tự: topology -> host tĩnh -> router/helper/backbone -> DHCP ->
DNS -> WEB -> Wi-Fi -> DHCP client -> ping -> DNS/HTTP -> Simulation. Trình tự
này tránh chụp một cấu hình còn bị thay đổi ở bước sau.

### A. Topology và thiết kế vật lý

| ID/file | Màn hình phải thấy | Điều kiện PASS | Trạng thái |
|---|---|---|---|
| `D01-topology.png` | Overview bố cục dọc | Từ trên xuống tầng 4, 3, 2, 1; bốn router nối SW-CORE | CẦN CHỤP |
| `D01-F1.png` | Zoom tầng 1 | ADMIN, STAFF, R1, SW-HC, AP, endpoint và label đúng | CẦN CHỤP |
| `D01-F2.png` | Zoom tầng 2 | TECH, MGMT, R2 và hai access switch | CẦN CHỤP |
| `D01-F3.png` | Zoom tầng 3 | MEETING, R3, AP và wireless client | CẦN CHỤP |
| `D01-F4.png` | Zoom tầng 4 | R4, SW-SRV, đúng 7 PC + 3 server | CẦN CHỤP |
| `C02-backbone.png` | SW-CORE và R1-R4 | Bốn link backbone đều xanh | CẦN CHỤP |

### B. Host tĩnh và dịch vụ

| ID/file | Màn hình phải thấy | Điều kiện PASS | Trạng thái |
|---|---|---|---|
| `C10A-SRV-PC01-static.png` | IP Configuration SRV-PC01 | `.40.5/28`, GW `.40.1`, DNS `.40.3` | CẦN CHỤP |
| `C10B-SRV-PC01-local-ping.png` | Command Prompt SRV-PC01 | Ping `.40.1`, `.40.2`, `.40.3`, `.40.4` thành công | CẦN CHỤP |
| `C05A-dhcp-server-ip.png` | Desktop/IP của DHCP-SRV | `.40.2/28`, GW `.40.1`, DNS `.40.3` | CẦN CHỤP |
| `C05B-dhcp-pools.png` | Services/DHCP | DHCP On, thấy đủ năm pool nghiệp vụ | CẦN CHỤP |
| `C05C-pool-staff.png` | Chi tiết POOL-STAFF | Start `.11.4`, GW `.11.1`, DNS `.40.3`, `/27`, max 20 | CẦN CHỤP |
| `C05D-pool-meeting.png` | Chi tiết POOL-MEETING | Start `.30.4`, GW `.30.1`, DNS `.40.3`, `/27`, max 20 | CẦN CHỤP |
| `C06A-dns-server-ip.png` | Desktop/IP của DNS-SRV | `.40.3/28`, GW `.40.1`, DNS `.40.3` | CẦN CHỤP |
| `C06B-dns-records.png` | Services/DNS | Service On; A `mmt-90.com` và CNAME `www` đúng | CẦN CHỤP |
| `C07A-web-server-ip.png` | Desktop/IP của WEB-SRV | `.40.4/28`, GW `.40.1`, DNS `.40.3` | CẦN CHỤP |
| `C07B-web-http.png` | Services/HTTP | HTTP On, `index.html` có nội dung nhóm | CẦN CHỤP |
| `C07C-web-content.png` | Browser qua domain | Trang nhóm mở bằng `www.mmt-90.com` | CẦN CHỤP |
| `C08-ap-staff.png` | AP tầng 1 | `MMT-90-STAFF`, WPA2-PSK/AES; không lộ passphrase | CẦN CHỤP |
| `C09-ap-meeting.png` | AP tầng 3 | `MMT-90-MEETING`, WPA2-PSK/AES; không lộ passphrase | CẦN CHỤP |

### C. Router, route, helper và backbone

| ID/file | Màn hình phải thấy | Điều kiện PASS | Trạng thái |
|---|---|---|---|
| `C01-T10A-R1-interface-route.png` | R1 `show ip interface brief` + `show ip route` | Interface dùng `up/up`, 4 static route | CẦN CHỤP |
| `C01-T10B-R2-interface-route.png` | R2 hai lệnh trên | Interface dùng `up/up`, 4 static route | CẦN CHỤP |
| `C01-T10C-R3-interface-route.png` | R3 hai lệnh trên | Interface dùng `up/up`, 5 static route | CẦN CHỤP |
| `C01-T10D-R4-interface-route.png` | R4 hai lệnh trên | Interface dùng `up/up`, 5 static route | CẦN CHỤP |
| `C04A-R1-dhcp-helper.png` | R1 helper output | Hai helper đều về `172.90.40.2` | CẦN CHỤP |
| `C04B-R2-dhcp-helper.png` | R2 helper output | Hai helper đều về `172.90.40.2` | CẦN CHỤP |
| `C04C-R3-dhcp-helper.png` | R3 helper output | Một helper về `172.90.40.2` | CẦN CHỤP |
| `T11-r1-to-r4-backbone.png` | R1 ping `172.90.255.4` | Thành công 5/5 | CẦN CHỤP |
| `S01A-R1-to-dhcp.png` | R1 ping `172.90.40.2` | Thành công 5/5 | CẦN CHỤP |
| `S01B-R2-to-dhcp.png` | R2 ping `172.90.40.2` | Thành công 5/5 | CẦN CHỤP |
| `S01C-R3-to-dhcp.png` | R3 ping `172.90.40.2` | Thành công 5/5 | CẦN CHỤP |
| `S01D-R4-to-dhcp.png` | R4 ping `172.90.40.2` | Thành công 5/5 | CẦN CHỤP |

Nếu interface và route không vừa một ảnh, dùng hậu tố `-1`, `-2`; vẫn giữ ID
gốc trong caption. Không crop mất hostname router hoặc câu lệnh.

### D. DHCP client và kiểm thử T01-T12

| ID/file | Kết quả phải thấy | Trạng thái |
|---|---|---|
| `T06A-admin-dhcp.png` | ADMIN: `172.90.10.4-.13/28`, GW `.10.1`, DNS `.40.3` | CẦN CHỤP |
| `T06B-staff-dhcp.png` | STAFF: `172.90.11.4-.23/27`, GW `.11.1`, DNS `.40.3` | CẦN CHỤP |
| `T06C-tech-dhcp.png` | TECH: `172.90.20.5-.9/28`, GW `.20.1`, DNS `.40.3` | CẦN CHỤP |
| `T06D-mgmt-dhcp.png` | MGMT: `172.90.21.5-.9/28`, GW `.21.1`, DNS `.40.3` | CẦN CHỤP |
| `T06E-meeting-dhcp.png` | MEETING: `172.90.30.4-.23/27`, GW `.30.1`, DNS `.40.3`; dùng lại cho T12 | CẦN CHỤP |
| `T01-admin-to-gateway.png` | ADMIN ping `172.90.10.1` thành công | CẦN CHỤP |
| `T02-admin-to-tech.png` | ADMIN ping IP DHCP thật của TECH thành công | CẦN CHỤP |
| `T03-mgmt-to-web.png` | MGMT ping `172.90.40.4` thành công | CẦN CHỤP |
| `T04-meeting-to-admin.png` | MEETING wireless ping ADMIN thành công | CẦN CHỤP |
| `T05-staff-to-admin.png` | STAFF wireless ping ADMIN thành công | CẦN CHỤP |
| `T07-dns-a.png` | `nslookup mmt-90.com` trả `172.90.40.4` | CẦN CHỤP |
| `T08-dns-cname.png` | `nslookup www.mmt-90.com` trả `172.90.40.4` | CẦN CHỤP |
| `T09A-web-admin.png` | Website bằng domain từ ADMIN | CẦN CHỤP |
| `T09B-web-staff.png` | Website bằng domain từ STAFF | CẦN CHỤP |
| `T09C-web-tech.png` | Website bằng domain từ TECH | CẦN CHỤP |
| `T09D-web-mgmt.png` | Website bằng domain từ MGMT | CẦN CHỤP |
| `T09E-web-meeting.png` | Website bằng domain từ MEETING | CẦN CHỤP |

Với smartphone không có Command Prompt, T06B dùng màn hình
`Config > Wireless0`/IP Configuration có đủ IP, mask, gateway và DNS. Với T05,
dùng Add Simple PDU và chụp Event List báo Successful; không thêm laptop mới
chỉ để có output ping.

### E. Simulation Mode

| ID/file | Event bắt buộc | Trạng thái |
|---|---|---|
| `SIM-DHCP.png` | Discover, Offer, Request, ACK qua R1 tới `172.90.40.2` | CẦN CHỤP |
| `SIM-DNS-HTTP.png` | DNS query/response tới `.40.3`, sau đó TCP/HTTP tới `.40.4` | CẦN CHỤP |
| `S02-final-smoke.png` | Một test PASS sau khi đóng/mở lại file; ưu tiên website/domain | KHUYẾN NGHỊ |

## 2. Quy tắc chụp để dùng thẳng trên Prism

- Để tiêu đề cửa sổ hoặc tên thiết bị nguồn xuất hiện trong ảnh.
- Không crop mất lệnh, URL, IP đích, gateway, DNS hoặc dòng success.
- Chụp ở độ phân giải đủ đọc; không chụp cả desktop nếu nội dung quá nhỏ.
- Không để lộ WPA2 passphrase, tài khoản cá nhân hoặc thông báo riêng tư.
- Khi ping lần đầu timeout vì ARP, chạy lại rồi chỉ chụp lần thành công.
- D01 là overview; D01-F1 đến D01-F4 vẫn bắt buộc để tên thiết bị đủ rõ.
- C07C có thể dùng lại làm một ảnh T09 nếu source/subnet nhìn rõ, nhưng vẫn nên
  chụp đủ T09A-E để chứng minh truy cập từ năm mạng.
- Sau khi lưu, mở lại ảnh ngay để kiểm tra không mờ và không bị cắt output.

## 3. Caption mẫu

- **C01A-D/T10A-D:** Các interface router đang sử dụng đều `up/up`; R1/R2 có
  bốn static route và R3/R4 có năm static route tới các subnet từ xa.
- **C04A-C:** R1, R2 và R3 có tổng cộng năm
  `ip helper-address 172.90.40.2`, chuyển DHCP broadcast tới server tập trung.
- **C05A-D:** DHCP-SRV có địa chỉ tĩnh đúng và năm pool cấp đúng gateway, DNS,
  mask và dải địa chỉ cho từng mạng động.
- **S01A-D:** Bốn router đều ping DHCP-SRV thành công 5/5, chứng minh đường đi
  và đường về giữa backbone và server subnet hoạt động.
- **T06A-E:** Client đại diện của năm mạng nhận DHCP đúng subnet, gateway và DNS.
- **T09A-E:** Client ở năm subnet truy cập website nội bộ thành công bằng domain.
