# Danh sách ảnh Khôi cần bàn giao cho report

Duy viết report trên Prism; Khôi chịu trách nhiệm chạy toàn bộ phần Packet
Tracer và cung cấp ảnh thật. Tất cả ảnh đặt trong `report/images/`. Không tạo
ảnh giả và không đánh PASS nếu chưa quan sát kết quả.

## 1. Ảnh đã có - không cần chụp lại

| ID | Nội dung đã chứng minh | File | Trạng thái |
|---|---|---|---|
| C01A/T10A | Interface `up/up` và route table R1 | `C01-T10A-R1-interface-route.png` | ĐÃ CÓ/PASS |
| C01B/T10B | Interface `up/up` và route table R2 | `C01-T10B-R2-interface-route.png` | ĐÃ CÓ/PASS |
| C01C/T10C | Interface `up/up` và route table R3 | `C01-T10C-R3-interface-route.png` | ĐÃ CÓ/PASS |
| C01D/T10D | Interface `up/up` và route table R4 | `C01-T10D-R4-interface-route.png` | ĐÃ CÓ/PASS |
| C04A | Hai DHCP helper của R1 | `C04A-R1-dhcp-helper.png` | ĐÃ CÓ/PASS |
| C04B | Hai DHCP helper của R2 | `C04B-R2-dhcp-helper.png` | ĐÃ CÓ/PASS |
| C04C | Một DHCP helper của R3 | `C04C-R3-dhcp-helper.png` | ĐÃ CÓ/PASS |
| C05A | IP tĩnh của DHCP-SRV | `C05A-dhcp-server-ip.png` | ĐÃ CÓ/PASS |
| C05B | DHCP On và danh sách năm pool | `C05B-dhcp-pools.png` | ĐÃ CÓ/PASS |
| S01A | R1 ping DHCP-SRV 5/5 | `S01A-R1-to-dhcp.png` | ĐÃ CÓ/PASS |
| S01B | R2 ping DHCP-SRV 5/5 | `S01B-R2-to-dhcp.png` | ĐÃ CÓ/PASS |
| S01C | R3 ping DHCP-SRV 5/5 | `S01C-R3-to-dhcp.png` | ĐÃ CÓ/PASS |
| S01D | R4 ping DHCP-SRV 5/5 | `S01D-R4-to-dhcp.png` | ĐÃ CÓ/PASS |

## 2. Ảnh cấu hình và thiết kế còn thiếu

| Thứ tự | ID/file | Màn hình phải thấy | Điều kiện PASS |
|---:|---|---|---|
| 1 | `D01-topology.png` | Overview bố cục dọc | Từ trên xuống: tầng 4, 3, 2, 1; thấy bốn nhánh về core |
| 2 | `D01-F1.png` | Zoom tầng 1 | Đọc được ADMIN, STAFF, R1, SW-HC, AP và endpoint |
| 3 | `D01-F2.png` | Zoom tầng 2 | Đọc được TECH, MGMT, R2 và hai access switch |
| 4 | `D01-F3.png` | Zoom tầng 3 | Đọc được MEETING, R3, AP và wireless client |
| 5 | `D01-F4.png` | Zoom tầng 4 | Đọc được R4, SW-SRV; tổng host gồm ba server không quá 10 |
| 6 | `C02-backbone.png` | SW-CORE và R1-R4 | Bốn link backbone đều xanh |
| 7 | `C05C-pool-staff.png` | Chi tiết POOL-STAFF | GW `.11.1`, DNS `.40.3`, `/27`, max 20 |
| 8 | `C05D-pool-meeting.png` | Chi tiết POOL-MEETING | GW `.30.1`, DNS `.40.3`, `/27`, max 20 |
| 9 | `C06A-dns-server-ip.png` | Desktop/IP của DNS-SRV | `.40.3/28`, GW `.40.1`, DNS `.40.3` |
| 10 | `C06B-dns-records.png` | Services/DNS | A và CNAME đúng, service On |
| 11 | `C07A-web-server-ip.png` | Desktop/IP của WEB-SRV | `.40.4/28`, GW `.40.1`, DNS `.40.3` |
| 12 | `C07B-web-http.png` | Services/HTTP | HTTP On, HTML đã tùy biến |
| 13 | `C07C-web-content.png` | Browser qua domain | Trang nhóm mở bằng `www.mmt-90.com` |
| 14 | `C08-ap-staff.png` | AP tầng 1 | `MMT-90-STAFF`, WPA2-PSK/AES |
| 15 | `C09-ap-meeting.png` | AP tầng 3 | `MMT-90-MEETING`, WPA2-PSK/AES |

Nếu một cửa sổ không thể hiển thị đồng thời IP và trạng thái dịch vụ thì dùng
hai ảnh như C07A/C07B; không ghép ảnh khiến chữ bị nhỏ hoặc mờ.

## 3. Ảnh nghiệm thu T01-T12 còn thiếu

| Thứ tự | ID/file | Kết quả phải thấy |
|---:|---|---|
| 16 | `T06A-admin-dhcp.png` | ADMIN: `172.90.10.x/28`, GW `.10.1`, DNS `.40.3` |
| 17 | `T06B-staff-dhcp.png` | STAFF: `172.90.11.x/27`, GW `.11.1`, DNS `.40.3` |
| 18 | `T06C-tech-dhcp.png` | TECH: `172.90.20.x/28`, GW `.20.1`, DNS `.40.3` |
| 19 | `T06D-mgmt-dhcp.png` | MGMT: `172.90.21.x/28`, GW `.21.1`, DNS `.40.3` |
| 20 | `T06E-meeting-dhcp.png` | MEETING: `172.90.30.x/27`, GW `.30.1`, DNS `.40.3`; dùng lại cho T12 |
| 21 | `T01-admin-to-gateway.png` | ADMIN ping `172.90.10.1` thành công |
| 22 | `T02-admin-to-tech.png` | ADMIN ping IP thật của TECH thành công |
| 23 | `T03-mgmt-to-web.png` | MGMT ping `172.90.40.4` thành công |
| 24 | `T04-meeting-to-admin.png` | MEETING wireless ping ADMIN thành công |
| 25 | `T05-staff-to-admin.png` | STAFF wireless ping ADMIN thành công |
| 26 | `T07-dns-a.png` | `nslookup mmt-90.com` trả `.40.4` |
| 27 | `T08-dns-cname.png` | `nslookup www.mmt-90.com` trả `.40.4` |
| 28 | `T09A-web-admin.png` | Website bằng domain từ ADMIN |
| 29 | `T09B-web-staff.png` | Website bằng domain từ STAFF |
| 30 | `T09C-web-tech.png` | Website bằng domain từ TECH |
| 31 | `T09D-web-mgmt.png` | Website bằng domain từ MGMT |
| 32 | `T09E-web-meeting.png` | Website bằng domain từ MEETING |
| 33 | `T11-r1-to-r4-backbone.png` | R1 ping `172.90.255.4` thành công 5/5 |

T10A-D đã có. T12 dùng lại T06E, vì cùng chứng minh laptop MEETING nhận địa chỉ
thuộc `172.90.30.0/27`.

## 4. Ảnh Simulation Mode

| Thứ tự | ID/file | Event bắt buộc |
|---:|---|---|
| 34 | `SIM-DHCP.png` | Discover, Offer, Request, ACK qua R1 tới `172.90.40.2` |
| 35 | `SIM-DNS-HTTP.png` | DNS query/response tới `.40.3`, sau đó TCP/HTTP tới `.40.4` |

## 5. Quy tắc chụp để Duy dùng thẳng trên Prism

- Để tiêu đề cửa sổ hoặc tên thiết bị nguồn xuất hiện trong ảnh.
- Không crop mất lệnh, URL, IP đích, gateway, DNS hoặc dòng success.
- Chụp ở độ phân giải đủ đọc; không chụp cả desktop nếu nội dung chỉ chiếm một
  góc rất nhỏ.
- Không để lộ WPA2 passphrase, tài khoản cá nhân hoặc thông báo riêng tư.
- Khi ping lần đầu timeout vì ARP, chạy lại rồi chụp lần thành công.
- Nếu test fail, lưu ảnh chẩn đoán với hậu tố `-debug`; không dùng ảnh debug làm
  bằng chứng PASS.
- D01 chỉ minh họa cấu trúc tổng thể. Với bố cục dọc hiện tại, D01-F1 đến D01-F4
  là ảnh bắt buộc để tên thiết bị và nhãn khu vực đủ lớn, rõ.
- C07C có thể dùng lại làm một trong các ảnh T09 nếu thấy rõ client/subnet; để
  an toàn khi chấm, vẫn chụp đủ T09A-E.

## 6. Caption ngắn cho 13 ảnh đã có

- **C01A-D/T10A-D:** Các interface router đang sử dụng đều `up/up`; R1/R2 có
  bốn static route và R3/R4 có năm static route tới các subnet từ xa.
- **C04A-C:** R1, R2 và R3 có tổng cộng năm
  `ip helper-address 172.90.40.2`, chuyển DHCP broadcast tới server tập trung.
- **C05A-B:** DHCP-SRV dùng `172.90.40.2/28`, gateway `.40.1`, DNS `.40.3`; DHCP
  Service On và có đủ năm pool nghiệp vụ.
- **S01A-D:** Cả bốn router ping DHCP-SRV thành công 5/5, chứng minh đường đi và
  đường về giữa backbone và server subnet hoạt động.
