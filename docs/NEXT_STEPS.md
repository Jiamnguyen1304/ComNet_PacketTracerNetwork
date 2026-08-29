# Checklist hoàn tất phần Packet Tracer - Nguyễn Minh Khôi

## Phạm vi bàn giao hiện tại

- Khôi thực hiện toàn bộ phần Packet Tracer còn lại, kể cả phần kiểm tra trước đây
  giao cho Duy.
- Duy chuyển sang viết report trên Prism. Checklist này **không** yêu cầu Khôi
  soạn, định dạng hoặc xuất report.
- Khôi chỉ cần tạo đủ kết quả và ảnh thật để Duy chèn vào report.
- File chuẩn duy nhất là `topology.pkt` ở thư mục gốc. Địa chỉ chuẩn nằm trong
  `docs/ADDRESSING_PLAN.md`.

## 1. Trạng thái đã hoàn thành - không cần làm lại

- [x] Topology bốn tầng đã được dựng bằng Packet Tracer 9.0.1.
- [x] Có 4 router Cisco 2911, 5 switch 2960-24TT, 2 Access Point-PT và 3
  Server-PT theo thiết kế.
- [x] Mười interface router đang dùng có địa chỉ đúng và đều `up/up`.
- [x] Backbone `172.90.255.0/29`: R1-R4 lần lượt là `.1`, `.2`, `.3`, `.4`.
- [x] Có đủ 18 static route: R1/R2 mỗi router 4; R3/R4 mỗi router 5.
- [x] Không dùng VLAN, serial link, WRT300N hay RIP/OSPF/EIGRP.
- [x] Năm LAN động có `ip helper-address 172.90.40.2`.
- [x] R1-R4 đều ping DHCP-SRV `172.90.40.2` thành công 5/5.
- [x] DHCP-SRV có IP `172.90.40.2/28`, gateway `172.90.40.1`, DNS
  `172.90.40.3` và DHCP Service đang On.
- [x] Danh sách có đủ năm pool ADMIN, STAFF, TECH, MGMT và MEETING. Pool mặc
  định `serverPool` không xóa được thì để nguyên; không dùng pool này để cấp IP.
- [x] Đã có 13 ảnh router, route, helper, DHCP server và ping trong
  `report/images/`.

Không chụp lại các ảnh đã có trừ khi topology bị sửa sau thời điểm này.

## 2. Chuẩn bị một phiên kiểm thử sạch

1. Đóng các cửa sổ hoặc bản Packet Tracer cũ để tránh save nhầm.
2. Mở đúng `topology.pkt` tại thư mục gốc và chờ link chuyển xanh ở Realtime.
3. Dùng `File > Save As` tạo một bản checkpoint ngoài thư mục Git nếu muốn giữ
   khả năng quay lại; tiếp tục làm trên file chuẩn sau khi chắc chắn đã mở đúng.
4. Không đổi address plan hoặc static route nếu các test hiện tại vẫn PASS.
5. Chọn một client đại diện cho mỗi mạng và ghi tên thiết bị/IP vào giấy nháp:

| Mạng | Network | Gateway | Mask | Client đại diện |
|---|---|---|---|---|
| ADMIN | `172.90.10.0/28` | `172.90.10.1` | `255.255.255.240` | Tự ghi theo topology |
| STAFF | `172.90.11.0/27` | `172.90.11.1` | `255.255.255.224` | Tự ghi theo topology |
| TECH | `172.90.20.0/28` | `172.90.20.1` | `255.255.255.240` | Tự ghi theo topology |
| MGMT | `172.90.21.0/28` | `172.90.21.1` | `255.255.255.240` | Tự ghi theo topology |
| MEETING | `172.90.30.0/27` | `172.90.30.1` | `255.255.255.224` | Tự ghi theo topology |

## 3. Kiểm tra phần topology và cấu hình của Duy

Phần này là audit, không phải cấu hình lại từ đầu.

- [ ] Chụp `D01-topology.png`: toàn bộ topology, bốn vùng tầng, hostname và
  đường nối đều đọc được.
- [ ] Chụp `C02-backbone.png`: zoom SW-CORE cùng bốn router, bốn link đều xanh.
- [ ] Đếm trực tiếp: 4 router 2911, 5 switch 2960-24TT, 2 Access Point-PT, 3
  Server-PT; không có WRT300N và không có serial link.
- [ ] R1 CLI chạy `ping 172.90.255.4`; chụp `T11-r1-to-r4-backbone.png` khi
  thành công 5/5.
- [ ] Nếu bất kỳ link hoặc test nào fail, chẩn đoán trước khi đụng đến route:
  kiểm tra đúng file, cổng `On`, cable, IP/mask và default gateway.

Bốn ảnh C01/T10A-D hiện có đã chứng minh interface và static route, nên không
cần chạy lại `show ip route` nếu topology chưa đổi.

## 4. Xác nhận DHCP, DNS, WEB và Wi-Fi

### 4.1. DHCP

- [ ] DHCP-SRV > Services > DHCP > chọn `POOL-STAFF`; xác nhận gateway
  `172.90.11.1`, DNS `172.90.40.3`, mask `255.255.255.224`, Maximum Users `20`;
  chụp `C05C-pool-staff.png` và ghi lại Start IP thật.
- [ ] Chọn `POOL-MEETING`; xác nhận gateway `172.90.30.1`, DNS
  `172.90.40.3`, mask `255.255.255.224`, Maximum Users `20`; chụp
  `C05D-pool-meeting.png` và ghi Start IP thật.
- [ ] Không dùng hoặc chỉnh `serverPool` trừ khi nó đang cấp nhầm cho client.

Các pool còn lại phải giữ đúng giá trị đã nhìn thấy: ADMIN bắt đầu `.10.4`, tối
đa 10; TECH bắt đầu `.20.5`, tối đa 5; MGMT bắt đầu `.21.5`, tối đa 5.

### 4.2. DNS

- [ ] DNS-SRV > Desktop > IP Configuration: `172.90.40.3/28`, gateway
  `172.90.40.1`, DNS `172.90.40.3`; chụp `C06A-dns-server-ip.png`.
- [ ] DNS-SRV > Services > DNS: Service On; A record
  `mmt-90.com -> 172.90.40.4`; CNAME
  `www.mmt-90.com -> mmt-90.com`; chụp `C06B-dns-records.png`.

### 4.3. WEB

- [ ] WEB-SRV > Desktop > IP Configuration: `172.90.40.4/28`, gateway
  `172.90.40.1`, DNS `172.90.40.3`; chụp `C07A-web-server-ip.png`.
- [ ] WEB-SRV > Services > HTTP: HTTP On và trang đã tùy biến có tên nhóm/MSSV;
  chụp `C07B-web-http.png`.
- [ ] Từ một client, mở `http://www.mmt-90.com`; chụp
  `C07C-web-content.png`. Không dùng IP thay cho domain ở ảnh nghiệm thu.

### 4.4. Wi-Fi

- [ ] AP tầng 1: SSID `MMT-90-STAFF`, WPA2-PSK và AES; chụp
  `C08-ap-staff.png`.
- [ ] AP tầng 3: SSID `MMT-90-MEETING`, WPA2-PSK và AES; chụp
  `C09-ap-meeting.png`.
- [ ] Laptop/smartphone STAFF và MEETING phải association đúng SSID rồi mới
  chọn DHCP. Ảnh không cần để lộ passphrase.

## 5. Chứng minh DHCP trên đủ năm subnet

Trên client đại diện: Desktop > IP Configuration > DHCP, đợi hiện thông báo
thành công, sau đó Command Prompt chạy `ipconfig /all`.

| Evidence | Điều kiện PASS |
|---|---|
| `T06A-admin-dhcp.png` | IP thuộc `172.90.10.0/28`, gateway `.10.1`, DNS `.40.3` |
| `T06B-staff-dhcp.png` | IP thuộc `172.90.11.0/27`, gateway `.11.1`, DNS `.40.3` |
| `T06C-tech-dhcp.png` | IP thuộc `172.90.20.0/28`, gateway `.20.1`, DNS `.40.3` |
| `T06D-mgmt-dhcp.png` | IP thuộc `172.90.21.0/28`, gateway `.21.1`, DNS `.40.3` |
| `T06E-meeting-dhcp.png` | IP thuộc `172.90.30.0/27`, gateway `.30.1`, DNS `.40.3` |

Ảnh T06E đồng thời dùng làm T12. `169.254.x.x` là DHCP fail. IP `172.90.x.x`
nhưng sai third octet/gateway thường là nối sai segment, association sai SSID
hoặc client đang dùng pool cũ.

## 6. Chạy đủ test chức năng

Sau khi đã biết IP thật của các client đại diện, chạy theo thứ tự này:

1. **T01:** ADMIN ping `172.90.10.1` -> `T01-admin-to-gateway.png`.
2. **T02:** ADMIN ping IP thật của TECH -> `T02-admin-to-tech.png`.
3. **T03:** MGMT ping WEB-SRV `172.90.40.4` -> `T03-mgmt-to-web.png`.
4. **T04:** MEETING wireless ping IP thật của ADMIN ->
   `T04-meeting-to-admin.png`.
5. **T05:** STAFF wireless ping IP thật của ADMIN ->
   `T05-staff-to-admin.png`.
6. **T07:** TECH chạy `nslookup mmt-90.com`; kết quả phải là
   `172.90.40.4` -> `T07-dns-a.png`.
7. **T08:** TECH chạy `nslookup www.mmt-90.com`; kết quả cuối phải là
   `172.90.40.4` -> `T08-dns-cname.png`.
8. **T09A-E:** từ một client ADMIN, STAFF, TECH, MGMT và MEETING lần lượt mở
   `http://www.mmt-90.com`; chụp năm ảnh `T09A-web-admin.png` đến
   `T09E-web-meeting.png`.

Ping lần đầu có thể timeout vì ARP; chạy lại rồi chỉ chụp kết quả cuối. Mỗi ảnh
phải thấy rõ thiết bị nguồn, lệnh/URL, địa chỉ đích và kết quả.

Sau mỗi test, cập nhật cột Actual/Status trong `docs/TEST_MATRIX.md`. Chỉ đánh
PASS khi đã quan sát kết quả thật.

## 7. Simulation Mode bắt buộc

### 7.1. DHCP relay

1. Chọn Simulation và `Edit Filters`, chỉ giữ ARP/DHCP.
2. Trên một ADMIN client chọn Static rồi chọn lại DHCP để tạo giao dịch mới.
3. Dùng Capture/Forward đến khi thấy Discover, Offer, Request, ACK đi qua R1 và
   tới DHCP-SRV `172.90.40.2`.
4. Chụp event list/PDU rõ chuỗi trên thành `SIM-DHCP.png`.

### 7.2. DNS rồi HTTP

1. Xóa event cũ, lọc ARP, DNS, TCP và HTTP.
2. Trên một client mở Browser và nhập `http://www.mmt-90.com`.
3. Capture/Forward đến khi thấy DNS query/response tới `172.90.40.3`, sau đó
   TCP/HTTP tới `172.90.40.4`.
4. Chụp `SIM-DNS-HTTP.png`.

## 8. Lưu file và nghiệm thu cuối

- [ ] Chuyển lại Realtime; kiểm tra link đang dùng đều xanh.
- [ ] Nếu đã sửa router/switch: chạy `copy running-config startup-config` trên
  thiết bị bị sửa và export lại đúng file config tương ứng.
- [ ] Save `topology.pkt`, đóng Packet Tracer rồi mở lại chính file này.
- [ ] Smoke test sau khi mở lại: T01, T07 và mở website bằng domain.
- [ ] Kiểm tra `report/images/` theo `report/SCREENSHOT_INDEX.md`; không để ảnh
  mờ, crop mất lệnh hoặc nhầm IP.
- [ ] Chuẩn bị demo 6-10 phút theo `demo/SCRIPT.md`. Đề yêu cầu **mỗi thành viên
  nói ít nhất một phần**, nên Duy vẫn phải có audio dù Khôi thao tác toàn bộ.
- [ ] Sau khi quay, thử link video trong cửa sổ ẩn danh và điền link thật vào
  `demo/demo-link.txt`.
- [ ] Xác nhận tên file nén và deadline trên LMS/giảng viên.

## 9. Phần cần con người thao tác

Codex không thể tự click và lưu trạng thái trong Packet Tracer, chạy DHCP/DNS/
Browser/Simulation thay người dùng, chụp bằng chứng chưa tồn tại, ghi âm hai
thành viên hoặc upload video. Codex có thể tiếp tục kiểm tra các ảnh Khôi gửi,
đối chiếu PASS/FAIL, cập nhật test matrix và rà soát artifact trong repository.
