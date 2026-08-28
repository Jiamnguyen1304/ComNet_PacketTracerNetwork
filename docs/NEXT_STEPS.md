# Các bước còn lại để hoàn thành đồ án

Tài liệu này là checklist tích hợp từ bản `topology.pkt` hiện tại đến bản nộp.
Không đánh dấu `PASS` nếu chưa quan sát kết quả thật trong Packet Tracer.

## Trạng thái đã chuẩn bị bằng text

- [x] Chốt `XX = 90` và kế hoạch địa chỉ `172.90.0.0/16`.
- [x] Điền thông tin hai thành viên, domain và SSID.
- [x] Soạn deployment script cho R1-R4 và năm switch trong
  `packet-tracer/configs/`.
- [x] Soạn cấu hình DHCP, DNS, WEB, hai AP và wireless client trong
  `packet-tracer/configs/25127389_NguyenMinhKhoi_Services_Config.txt`.
- [x] Hoàn thiện mã nguồn trang web tại `packet-tracer/web/index.html`.
- [x] Chuyển test matrix về địa chỉ/domain thật và để trạng thái chưa chạy.
- [x] Phân owner cho các ảnh cấu hình C01-C09.

## 1. Duy giữ topology lock và tạo checkpoint

Owner: Duy. Thao tác bắt buộc bằng Packet Tracer.

- [ ] Pull/copy các file text mới nhất.
- [ ] Đóng Packet Tracer trên máy còn lại để tránh hai người sửa `.pkt` đồng thời.
- [ ] Mở `topology.pkt` và chọn `File > Save As` thành một checkpoint có thời gian,
  ví dụ `topology-before-config-20260828.pkt` ở ngoài thư mục Git hoặc với đuôi
  `.bak` đã được ignore.
- [ ] Kiểm tra đúng 4 router 2911, 5 switch 2960-24TT, 2 Access Point-PT và 3
  Server-PT.
- [ ] Đối chiếu port thật với `docs/LOGICAL_TOPOLOGY.md`. Nếu port khác, sửa
  dòng `interface` trong script trước khi paste; không đổi dây tùy tiện.

Điểm dừng: topology mở bình thường và đã có bản backup.

## 2. Nhập cấu hình router và switch

Owner: Duy. Thao tác bắt buộc bằng CLI Packet Tracer.

Nhập từng file vào đúng thiết bị theo thứ tự:

1. `SW-CORE.txt`, `SW-HC.txt`, `SW-KT.txt`, `SW-LD.txt`, `SW-SRV.txt`.
2. `R1.txt`, `R2.txt`, `R3.txt`, `R4.txt`.

Sau mỗi router, kiểm tra:

```text
show ip interface brief
show ip route
show running-config
```

Kết quả bắt buộc:

- [ ] Mọi interface đang dùng là `up/up`.
- [ ] R1 backbone là `172.90.255.249/29`.
- [ ] R2 backbone là `172.90.255.250/29`.
- [ ] R3 backbone là `172.90.255.251/29`.
- [ ] R4 backbone là `172.90.255.252/29`.
- [ ] R1 có 4 static route, R2 có 4, R3 có 5, R4 có 5.
- [ ] R1/R2/R3 có tổng cộng 5 `ip helper-address 172.90.60.10` trên các
  interface LAN client.
- [ ] Không có RIP, OSPF, EIGRP hoặc VLAN dùng để phân đoạn.

Backbone smoke test:

```text
R1# ping 172.90.255.250
R1# ping 172.90.255.251
R1# ping 172.90.255.252
```

Điểm dừng: cả ba ping thành công. Nếu fail, chưa cấu hình service.

## 3. Duy nhập phần cấu hình do Khôi bàn giao

Owner nhập topology: Duy. Owner nội dung/review: Khôi.

Mở file `packet-tracer/configs/25127389_NguyenMinhKhoi_Services_Config.txt`
và nhập lần lượt:

1. Static IP cho DHCP-SRV, DNS-SRV và WEB-SRV.
2. Bật DHCP và tạo đúng 5 pool.
3. Bật DNS, tạo A record và CNAME.
4. Bật HTTP và chép `packet-tracer/web/index.html` vào WEB-SRV.
5. Đặt SSID, WPA2-PSK/AES cho AP-STAFF và AP-MEETING.
6. Cho Laptop-PT/Smartphone-PT kết nối đúng SSID và nhận IP bằng DHCP.

Sau khi nhập, Khôi review trực tiếp hoặc qua ảnh:

- [ ] Ba static IP server đúng `.10`, `.11`, `.12` trong `172.90.60.0/28`.
- [ ] Mỗi DHCP pool có đúng gateway, DNS, start IP, mask và maximum users.
- [ ] `mmt-90.com` A record trả `172.90.60.12`.
- [ ] `www.mmt-90.com` CNAME trỏ `mmt-90.com`.
- [ ] Website có đủ hai họ tên và MSSV.
- [ ] Hai AP là Access Point-PT, không phải WRT300N.

## 4. Cấp DHCP cho toàn bộ client

Owner: Duy thao tác, Khôi kiểm tra kết quả.

Trên từng PC tầng 1-3: `Desktop > IP Configuration > DHCP`.

Trên wireless client: kết nối đúng SSID trước, sau đó chọn DHCP.

Chọn ít nhất một client đại diện mỗi subnet và chạy `ipconfig /all`:

| Subnet | Khoảng IP hợp lệ | Gateway | DNS |
|---|---|---|---|
| ADMIN | `172.90.10.10-19/27` | `172.90.10.1` | `172.90.60.11` |
| STAFF | `172.90.20.10-29/27` | `172.90.20.1` | `172.90.60.11` |
| TECH | `172.90.30.10-14/28` | `172.90.30.1` | `172.90.60.11` |
| MGMT | `172.90.40.10-14/28` | `172.90.40.1` | `172.90.60.11` |
| MEETING | `172.90.50.10-29/27` | `172.90.50.1` | `172.90.60.11` |

Nếu một phòng có nhiều client hơn `Maximum Users` của pool thì đề xuất chỉ giữ
đủ client cần mô phỏng hoặc tăng pool trong giới hạn usable range sau khi nhóm
đối chiếu lại yêu cầu. Không gán IP tĩnh cho client để né lỗi DHCP.

## 5. Chạy nghiệm thu theo lớp

Owner: Khôi điều phối, cả hai thực hiện.

Chạy đúng thứ tự để dễ xác định lỗi:

1. Client ping default gateway.
2. Client ping `172.90.60.10`, `.11`, `.12`.
3. Client ping thiết bị ở subnet khác.
4. `nslookup mmt-90.com`.
5. `nslookup www.mmt-90.com`.
6. Mở `http://www.mmt-90.com` bằng Web Browser.
7. Chạy đủ T01-T12 trong `docs/TEST_MATRIX.md`.

Sau mỗi test:

- [ ] Ghi output thật vào cột `Actual`.
- [ ] Đổi trạng thái sang `PASS` hoặc `FAIL`.
- [ ] Chụp ảnh với đúng Evidence ID.
- [ ] Không dùng truy cập website bằng IP làm bằng chứng T09.

## 6. Chụp ảnh cấu hình và Simulation Mode

Owner theo `report/SCREENSHOT_INDEX.md`.

- [ ] Chụp D01 và C01-C09, giữ rõ hostname/IP/SSID.
- [ ] Chụp T01-T12; dùng hậu tố A-E cho test nhiều subnet.
- [ ] Lưu ảnh vào `report/images/` đúng tên trong index.
- [ ] Cập nhật trạng thái và caption ngay sau khi chụp.
- [ ] Simulation DHCP: ghi lại Discover, Offer, Request, ACK từ client tầng 1
  qua R1 tới DHCP-SRV.
- [ ] Simulation DNS/HTTP: ghi DNS query/response, TCP handshake và HTTP.

## 7. Export cấu hình thật

Owner: Duy. Bắt buộc sau khi topology đã pass.

Trên từng router/switch:

```text
show running-config
```

Copy output thực tế và thay nội dung deployment script trong đúng 9 file:

- `R1.txt`, `R2.txt`, `R3.txt`, `R4.txt`.
- `SW-CORE.txt`, `SW-HC.txt`, `SW-KT.txt`, `SW-LD.txt`, `SW-SRV.txt`.

Không export giả từ script vì running-config là bằng chứng cấu hình thật đã
được Packet Tracer chấp nhận.

## 8. Ghi ba lỗi thật

Owner: cả hai.

Trong lúc tích hợp, ghi ngay vào `docs/ISSUES.md`:

- triệu chứng;
- lệnh/ảnh chẩn đoán;
- nguyên nhân gốc;
- cách sửa;
- kết quả retest.

Không tự dùng lỗi gợi ý nếu nhóm không thật sự gặp lỗi đó.

## 9. Báo cáo, demo và đóng gói

Owner: cả hai. Cần con người thực hiện.

- [ ] Xác nhận phiên bản trong `Help > About` và điền vào
  `docs/PROJECT_INFO.md` cùng báo cáo Prism.
- [ ] Xác nhận deadline và quy tắc tên file/zip với LMS hoặc giảng viên.
- [ ] Đưa ảnh, bảng test và ba issue thật vào report trên Prism.
- [ ] Xuất `Report.pdf`, mở lại kiểm tra font, ảnh và link.
- [ ] Quay video 6-10 phút theo `demo/SCRIPT.md`; cả hai phải có audio.
- [ ] Upload video, kiểm tra link trong cửa sổ ẩn danh rồi điền
  `demo/demo-link.txt`.
- [ ] Save `topology.pkt`, đóng Packet Tracer, mở lại file và chạy smoke test.
- [ ] Kiểm tra bản nộp không còn `HUMAN_REQUIRED`, `Chưa chạy` hoặc `Chưa chụp`.
- [ ] Commit/push phiên bản cuối và tạo file zip nếu đề yêu cầu.

## Những việc Codex không thể tự thực hiện trong môi trường hiện tại

- Mở, chỉnh và save file nhị phân `.pkt` trong giao diện Packet Tracer.
- Xác nhận port/interface thật nếu chúng khác label trên sơ đồ.
- Quan sát kết quả ping, DHCP, DNS, HTTP và Simulation Mode.
- Chụp ảnh bằng chứng hợp lệ từ Packet Tracer.
- Bịa kết quả PASS hoặc ba lỗi “đã gặp”.
- Xác nhận Packet Tracer version đang dùng nếu chưa mở `Help > About`.
- Truy cập report Prism của nhóm, thu âm hai thành viên hoặc upload video.
- Xác nhận deadline/quy tắc tên file không có trong repo.

Các mục trên được đánh dấu `HUMAN_REQUIRED` để nhóm xử lý bằng kết quả thật.
