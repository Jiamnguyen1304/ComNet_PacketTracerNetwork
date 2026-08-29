# Hướng dẫn hoàn thành thô đồ án Packet Tracer

## 0. Phạm vi và tiêu chí kết thúc

Tài liệu này bắt đầu từ `topology.pkt` hiện tại và kết thúc khi mô hình mạng đã
hoạt động đầy đủ. Phạm vi **không bao gồm** viết report, chèn ảnh vào Prism,
quay/upload video demo hoặc đóng gói bài nộp cuối.

Đồ án đạt mức hoàn thành thô khi:

- Topology đúng bốn tầng, đúng số host và mọi link đang dùng đều hoạt động.
- Bốn router có đúng interface IP, 18 static route và 5 DHCP helper.
- Tất cả client tầng 1-3 nhận DHCP đúng subnet, gateway và DNS.
- Tầng 4 có đúng 7 PC tĩnh + 3 server tĩnh, tổng 10 host.
- DNS A/CNAME, website và hai WLAN hoạt động.
- Kiểm thử liên subnet, DHCP, DNS và HTTP đều PASS.
- File đóng/mở lại vẫn hoạt động; running-config đã export.

Nguồn chuẩn:

1. `topology.pkt` ở thư mục gốc.
2. `docs/ADDRESSING_PLAN.md`.
3. `packet-tracer/configs/R1.txt` đến `R4.txt`.
4. `packet-tracer/web/index.html`.

Không tự thay address plan trong lúc sửa lỗi.

---

## 1. Tạo checkpoint và mở đúng file

1. Đóng toàn bộ cửa sổ Packet Tracer.
2. Mở đúng:

   ```text
   C:\Users\PC\OneDrive\Máy tính\Packet_tracer_project\topology.pkt
   ```

3. Kiểm tra Packet Tracer đang dùng phiên bản 9.0.1.
4. Chọn `File > Save As` và tạo một checkpoint ngoài repository, ví dụ:

   ```text
   topology-before-final-fix.pkt
   ```

5. Quay lại file chính `topology.pkt` để làm tiếp.
6. Chuyển sang Realtime, chờ khoảng 30 giây. Link đang dùng phải xanh.

Không tiếp tục nếu đang mở nhầm bản copy hoặc có link đỏ chưa rõ nguyên nhân.

---

## 2. Sửa topology vật lý trước khi cấu hình dịch vụ

### 2.1. Giảm tầng 4 còn đúng 10 host

Ảnh audit lúc 18:11 cho thấy tầng 4 có 10 PC và 3 server, tổng 13 host. Quyết
định cuối là tính Server-PT như host.

1. Chọn công cụ Delete hoặc chọn thiết bị rồi nhấn Delete.
2. Xóa `PC18`, `PC19`, `PC20` cùng ba cable tương ứng.
3. Giữ `PC11-PC17` và ba server.
4. Kiểm đếm lại: 7 PC + DHCP-SRV + DNS-SRV + WEB-SRV = 10 host.

Không xóa R4, SW-SRV hoặc server dịch vụ.

### 2.2. Đổi display name

Với từng thiết bị: mở thiết bị > `Config` > `Display Name`, rồi đổi theo bảng.

| Tên hiện tại | Tên cuối |
|---|---|
| PC0, PC2, PC3, PC4, PC5 | ADMIN-PC01 đến ADMIN-PC05 |
| PC6, PC7, PC8, PC9, PC10 | ADMIN-PC06 đến ADMIN-PC10 |
| PC21, PC22, PC23, PC24, PC25 | MGMT-PC01 đến MGMT-PC05 |
| PC21(1), PC22(1), PC23(1), PC24(1), PC25(1) | TECH-PC01 đến TECH-PC05 |
| PC11, PC12, PC13, PC14, PC15, PC16, PC17 | SRV-PC01 đến SRV-PC07 |
| Smartphone10(2), Smartphone10, Smartphone10(1) | STAFF-PHONE01 đến STAFF-PHONE03 |
| Smartphone10(3) | MEETING-PHONE01 |
| Laptop0 | MEETING-LAPTOP01 |

Không đổi hostname R1-R4, SW-HC, SW-KT, SW-LD, SW-SRV, SW-CORE, DHCP-SRV,
DNS-SRV, WEB-SRV, AP-STAFF hoặc AP-MEETING.

### 2.3. Sửa tên khu vực và thêm label

1. Đổi nhãn có dây tầng 1 từ `Phòng quản trị` thành `Phòng hành chính`.
2. Dùng Place Note đặt các label sau:

| Khu vực | Label |
|---|---|
| ADMIN | `ADMIN 172.90.10.0/28 - GW 172.90.10.1 - DHCP` |
| STAFF | `STAFF 172.90.11.0/27 - GW 172.90.11.1 - MMT-90-STAFF` |
| TECH | `TECH 172.90.20.0/28 - GW 172.90.20.1 - DHCP` |
| MGMT | `MGMT 172.90.21.0/28 - GW 172.90.21.1 - DHCP` |
| MEETING | `MEETING 172.90.30.0/27 - GW 172.90.30.1 - MMT-90-MEETING` |
| SERVER | `SERVER 172.90.40.0/28 - GW 172.90.40.1 - STATIC` |
| SW-CORE | `BACKBONE 172.90.255.0/29 - R1 .1, R2 .2, R3 .3, R4 .4` |

Đặt label không đè cable hoặc icon. Tổng quan canvas từ trên xuống phải là tầng
4, tầng 3, tầng 2, tầng 1; SW-CORE chỉ nối R1-R4.

### 2.4. Kiểm tra thiết bị và cable

- Tầng 1: R1/G0/0 -> SW-HC; R1/G0/1 -> AP-STAFF.
- Tầng 2: R2/G0/0 -> SW-KT; R2/G0/1 -> SW-LD.
- Tầng 3: R3/G0/0 -> AP-MEETING.
- Tầng 4: R4/G0/0 -> SW-SRV.
- Backbone: R1/R2 G0/2 và R3/R4 G0/1 -> SW-CORE.
- Không có serial link, VLAN hoặc WRT300N.

Save `topology.pkt` sau khi hoàn tất phần 2.

---

## 3. Cấu hình toàn bộ host tĩnh tầng 4

Trên mỗi PC/Server-PT: `Desktop > IP Configuration > Static`. Dòng R4 trong
bảng chỉ để đối chiếu gateway; R4 vẫn được cấu hình bằng CLI.

| Thiết bị | IP | Mask | Gateway | DNS |
|---|---|---|---|---|
| R4/G0/0 | `172.90.40.1` | `255.255.255.240` | Không áp dụng | Không áp dụng |
| DHCP-SRV | `172.90.40.2` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| DNS-SRV | `172.90.40.3` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| WEB-SRV | `172.90.40.4` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| SRV-PC01 | `172.90.40.5` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| SRV-PC02 | `172.90.40.6` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| SRV-PC03 | `172.90.40.7` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| SRV-PC04 | `172.90.40.8` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| SRV-PC05 | `172.90.40.9` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| SRV-PC06 | `172.90.40.10` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |
| SRV-PC07 | `172.90.40.11` | `255.255.255.240` | `172.90.40.1` | `172.90.40.3` |

Để `.40.12-.40.14` trống. Không chọn DHCP cho bất kỳ thiết bị tầng 4 nào.

Kiểm tra nhanh trên SRV-PC01:

```text
ping 172.90.40.1
ping 172.90.40.2
ping 172.90.40.3
ping 172.90.40.4
```

Nếu ping local fail, kiểm tra cable, IP trùng, mask và gateway; chưa kiểm tra
static route ở bước này vì các đích đều cùng subnet.

---

## 4. Kiểm tra router và static routing

Router hiện đã có cấu hình đúng. Chỉ sửa khi output khác bảng dưới.

### 4.1. Expected interface

| Router | LAN/interface | Backbone | Số helper | Số static route |
|---|---|---|---:|---:|
| R1 | G0/0 `.10.1/28`; G0/1 `.11.1/27` | G0/2 `.255.1/29` | 2 | 4 |
| R2 | G0/0 `.20.1/28`; G0/1 `.21.1/28` | G0/2 `.255.2/29` | 2 | 4 |
| R3 | G0/0 `.30.1/27` | G0/1 `.255.3/29` | 1 | 5 |
| R4 | G0/0 `.40.1/28` | G0/1 `.255.4/29` | 0 | 5 |

Trên từng router chạy:

```text
enable
show ip interface brief
show ip route
show running-config | include helper-address
```

Điều kiện PASS:

- Interface đang dùng là `up/up`.
- Route table có đúng số dòng `S` trong bảng.
- R1/R2 mỗi router có hai helper; R3 có một helper.
- Tất cả helper là `172.90.40.2`.
- Không có RIP, OSPF hoặc EIGRP.

Nếu cấu hình khác, không viết lại bằng trí nhớ. Mở file tương ứng trong
`packet-tracer/configs/`, copy phần interface/route bị thiếu và paste vào CLI.

Sau khi sửa router:

```text
copy running-config startup-config
```

### 4.2. Kiểm tra backbone

Trên R1:

```text
ping 172.90.255.2
ping 172.90.255.3
ping 172.90.255.4
ping 172.90.40.2
```

Tất cả phải thành công. Ping đầu tiên có thể mất một gói vì ARP; chạy lại trước
khi kết luận lỗi.

### 4.3. Kiểm tra switch

Trên SW-CORE, SW-HC, SW-KT, SW-LD và SW-SRV:

```text
enable
show running-config | include hostname
show vlan brief
```

Yêu cầu:

- Hostname đúng.
- Các port access vẫn ở VLAN 1 mặc định.
- Không tạo thêm VLAN hoặc IP quản trị nếu đề không yêu cầu.
- Port đang cắm cable phải hoạt động.

---

## 5. Hoàn thiện DHCP-SRV

### 5.1. IP của server

`DHCP-SRV > Desktop > IP Configuration > Static`:

```text
IP Address      172.90.40.2
Subnet Mask     255.255.255.240
Default Gateway 172.90.40.1
DNS Server      172.90.40.3
```

### 5.2. Năm DHCP pool

Vào `Services > DHCP`, bật `Service: On`. Chọn từng pool có sẵn và chỉnh đúng
bảng; nhấn Save sau mỗi pool. Không tạo pool trùng tên.

| Pool | Gateway | DNS | Start IP | Mask | Max users | Range kết quả |
|---|---|---|---|---|---:|---|
| POOL-ADMIN | `172.90.10.1` | `172.90.40.3` | `172.90.10.4` | `255.255.255.240` | 10 | `.10.4-.10.13` |
| POOL-STAFF | `172.90.11.1` | `172.90.40.3` | `172.90.11.4` | `255.255.255.224` | 20 | `.11.4-.11.23` |
| POOL-TECH | `172.90.20.1` | `172.90.40.3` | `172.90.20.5` | `255.255.255.240` | 5 | `.20.5-.20.9` |
| POOL-MGMT | `172.90.21.1` | `172.90.40.3` | `172.90.21.5` | `255.255.255.240` | 5 | `.21.5-.21.9` |
| POOL-MEETING | `172.90.30.1` | `172.90.40.3` | `172.90.30.4` | `255.255.255.224` | 20 | `.30.4-.30.23` |

`serverPool` là pool mặc định không xóa được. Giữ nguyên nhưng không dùng cho
client. SERVER subnet không có DHCP client.

---

## 6. Hoàn thiện DNS-SRV

### 6.1. IP tĩnh

`DNS-SRV > Desktop > IP Configuration > Static`:

```text
IP Address      172.90.40.3
Subnet Mask     255.255.255.240
Default Gateway 172.90.40.1
DNS Server      172.90.40.3
```

### 6.2. DNS record

Vào `Services > DNS`, bật Service `On`.

Tạo hoặc sửa:

```text
Name: mmt-90.com
Type: A Record
Address: 172.90.40.4
```

```text
Name: www.mmt-90.com
Type: CNAME
Host Name: mmt-90.com
```

Không tạo A record `www` trỏ sang IP khác. Nếu record cũ sai, xóa record sai và
thêm lại hai record trên.

---

## 7. Hoàn thiện WEB-SRV

### 7.1. IP tĩnh

`WEB-SRV > Desktop > IP Configuration > Static`:

```text
IP Address      172.90.40.4
Subnet Mask     255.255.255.240
Default Gateway 172.90.40.1
DNS Server      172.90.40.3
```

### 7.2. Website

1. Vào `Services > HTTP`.
2. Bật HTTP `On`.
3. Chọn `index.html`.
4. Copy nội dung từ `packet-tracer/web/index.html` vào editor.
5. Nhấn Save trong giao diện HTTP.
6. Kiểm tra trang có:
   - `DUYKHOI-NETCOM`.
   - `XX = 90`.
   - Nguyễn Vũ Đức Duy - 25127190.
   - Nguyễn Minh Khôi - 25127389.

Không cần bật HTTPS nếu đề không yêu cầu.

---

## 8. Hoàn thiện hai mạng Wi-Fi

Passphrase lab thống nhất:

```text
MMT90@2026
```

Chỉ dùng passphrase này trong mô phỏng.

### 8.1. AP-STAFF

Mở AP-STAFF > Config > wireless interface:

```text
SSID           MMT-90-STAFF
Authentication WPA2-PSK
Encryption     AES
Pass Phrase    MMT90@2026
```

### 8.2. AP-MEETING

Mở AP-MEETING > Config > wireless interface:

```text
SSID           MMT-90-MEETING
Authentication WPA2-PSK
Encryption     AES
Pass Phrase    MMT90@2026
```

Hai AP chỉ bridge Layer 2. Không bật NAT, routing hoặc DHCP trên AP.

### 8.3. Kết nối wireless client

Với STAFF-PHONE01..03:

1. Chọn Wireless0 hoặc PC Wireless.
2. Kết nối `MMT-90-STAFF`.
3. Chọn WPA2-PSK/AES và nhập passphrase.
4. Chọn IP Configuration `DHCP`.

Với MEETING-PHONE01 và MEETING-LAPTOP01, làm tương tự nhưng chọn
`MMT-90-MEETING`.

Đường association dạng chấm phải xuất hiện từ client đến đúng AP.

---

## 9. Cho toàn bộ client tầng 1-3 nhận DHCP

### 9.1. Client cần đặt DHCP

- ADMIN-PC01..10.
- TECH-PC01..05.
- MGMT-PC01..05.
- STAFF-PHONE01..03.
- MEETING-PHONE01 và MEETING-LAPTOP01.

Trên từng client, chọn `Desktop > IP Configuration > DHCP`. Với smartphone,
dùng mục IP Configuration của Wireless0 nếu giao diện không có Desktop.

### 9.2. Kết quả đúng

| Nhóm | IP phải thuộc | Mask | Gateway | DNS |
|---|---|---|---|---|
| ADMIN | `172.90.10.4-.13` | `255.255.255.240` | `172.90.10.1` | `172.90.40.3` |
| STAFF | `172.90.11.4-.23` | `255.255.255.224` | `172.90.11.1` | `172.90.40.3` |
| TECH | `172.90.20.5-.9` | `255.255.255.240` | `172.90.20.1` | `172.90.40.3` |
| MGMT | `172.90.21.5-.9` | `255.255.255.240` | `172.90.21.1` | `172.90.40.3` |
| MEETING | `172.90.30.4-.23` | `255.255.255.224` | `172.90.30.1` | `172.90.40.3` |

Trên ít nhất một client mỗi nhóm chạy:

```text
ipconfig /all
```

Nếu nhận `169.254.x.x`, DHCP đã fail. Không gán IP tĩnh để che lỗi.

---

## 10. Kiểm thử chức năng từ gần đến xa

Chỉ chuyển sang bước sau khi bước trước PASS.

### 10.1. Local gateway

```text
ADMIN-PC01  -> ping 172.90.10.1
STAFF-PHONE01 -> ping 172.90.11.1
TECH-PC01   -> ping 172.90.20.1
MGMT-PC01   -> ping 172.90.21.1
MEETING-LAPTOP01 -> ping 172.90.30.1
SRV-PC01    -> ping 172.90.40.1
```

Nếu fail ở đây, lỗi nằm ở local IP/mask, cable, Wi-Fi association hoặc cổng
router; chưa cần kiểm tra static route.

### 10.2. Liên subnet

Đọc IP thật bằng `ipconfig` rồi chạy:

```text
ADMIN-PC01 -> ping <IP-TECH-PC01>
MGMT-PC01 -> ping 172.90.40.4
STAFF-PHONE01 -> ping <IP-ADMIN-PC01>
MEETING-LAPTOP01 -> ping <IP-ADMIN-PC01>
SRV-PC01 -> ping <IP-MGMT-PC01>
```

Tất cả phải thành công. Nếu local gateway PASS nhưng remote fail, kiểm tra
static route và đường trả về trên router đích.

### 10.3. DNS

Trên TECH-PC01:

```text
nslookup mmt-90.com
nslookup www.mmt-90.com
```

Cả hai phải phân giải cuối cùng thành `172.90.40.4`. Nếu phiên bản Packet
Tracer không hỗ trợ `nslookup`, dùng:

```text
ping mmt-90.com
ping www.mmt-90.com
```

### 10.4. Website bằng domain

Mở Web Browser trên một client đại diện của từng mạng động và nhập:

```text
http://www.mmt-90.com
```

Chạy từ ADMIN, STAFF, TECH, MGMT và MEETING. Không nghiệm thu bằng
`http://172.90.40.4`.

### 10.5. Bộ test cuối

| ID | Test | Điều kiện PASS |
|---|---|---|
| T01 | ADMIN-PC01 ping `.10.1` | Success |
| T02 | ADMIN-PC01 ping TECH-PC01 | Success |
| T03 | MGMT-PC01 ping `.40.4` | Success |
| T04 | MEETING-LAPTOP01 ping ADMIN-PC01 | Success |
| T05 | STAFF-PHONE01 ping ADMIN-PC01 | Success |
| T06 | Một client mỗi subnet chạy `ipconfig /all` | Đúng pool/GW/DNS |
| T07 | Lookup `mmt-90.com` | `.40.4` |
| T08 | Lookup `www.mmt-90.com` | `.40.4` |
| T09 | Website bằng domain từ năm subnet | Trang nhóm hiển thị |
| T10 | `show ip route` R1-R4 | 4, 4, 5, 5 static route |
| T11 | R1 ping `.255.4` | Success |
| T12 | MEETING-LAPTOP01 nhận DHCP | IP `172.90.30.4-.23` |

Ghi kết quả thật vào `docs/TEST_MATRIX.md`. Việc viết phần thuyết minh report
không thuộc phạm vi tài liệu này.

---

## 11. Cây xử lý lỗi nhanh

### Client nhận 169.254.x.x

Kiểm tra theo thứ tự:

1. Client đang chọn DHCP.
2. Wired link xanh hoặc wireless association đúng SSID.
3. AP không chạy DHCP riêng.
4. Router LAN interface đúng IP và `up/up`.
5. Interface có `ip helper-address 172.90.40.2`.
6. Router ping được `172.90.40.2`.
7. Pool đúng network, mask, gateway, start và max users.
8. DHCP Service đang On.

### Ping gateway được nhưng ping subnet khác không được

1. Chạy `show ip route` trên router nguồn.
2. Kiểm tra route tới subnet đích.
3. Kiểm tra router đích có route quay về subnet nguồn.
4. Kiểm tra default gateway của thiết bị đích.

### Ping WEB-SRV bằng IP được nhưng domain không hoạt động

1. Client phải có DNS `172.90.40.3`.
2. Ping `172.90.40.3`.
3. DNS Service phải On.
4. Kiểm tra A record và CNAME.

### Domain phân giải nhưng trang không mở

1. Ping `172.90.40.4`.
2. WEB-SRV HTTP phải On.
3. Kiểm tra `index.html` đã Save.
4. Nhập đúng `http://www.mmt-90.com`.

### Wireless không nhận DHCP

1. Kiểm tra đúng SSID và passphrase.
2. Xác nhận đường association tới đúng AP.
3. Kiểm tra AP cắm đúng cổng LAN router.
4. Kiểm tra helper trên R1/G0/1 hoặc R3/G0/0.

---

## 12. Save, mở lại và export cấu hình

### 12.1. Lưu thiết bị mạng

Trên R1-R4 và năm switch:

```text
copy running-config startup-config
```

### 12.2. Lưu topology

1. Chọn Realtime.
2. Chờ mọi link hoạt động.
3. `File > Save` vào đúng `topology.pkt` ở thư mục gốc.
4. Đóng Packet Tracer hoàn toàn.
5. Mở lại chính file `topology.pkt`.

### 12.3. Smoke test sau khi mở lại

```text
ADMIN-PC01 -> ping 172.90.10.1
R1 -> ping 172.90.40.2
TECH-PC01 -> nslookup www.mmt-90.com
TECH-PC01 -> mở http://www.mmt-90.com
```

Nếu bốn test PASS thì trạng thái đã được lưu đúng.

### 12.4. Export running-config

Trên từng router/switch:

1. Chạy `show running-config`.
2. Dùng nút Copy của cửa sổ CLI.
3. Cập nhật đúng file trong `packet-tracer/configs/`.
4. Không dán config router này vào file router khác.

Danh sách phải có:

```text
R1.txt
R2.txt
R3.txt
R4.txt
SW-CORE.txt
SW-HC.txt
SW-KT.txt
SW-LD.txt
SW-SRV.txt
```

Nếu chỉ đổi display name client, label hoặc IP end-device thì không cần export
lại switch/router config. Chỉ export lại thiết bị mạng đã thực sự đổi CLI.

---

## 13. Checklist hoàn thành thô

### Topology

- [ ] Chỉ còn 7 PC + 3 server tại tầng 4.
- [ ] Endpoint có display name theo khu vực.
- [ ] Nhãn tầng 1 là Phòng hành chính.
- [ ] Có label cho sáu LAN và backbone.
- [ ] Tất cả link đang dùng hoạt động.

### Addressing và routing

- [ ] Bảy subnet không chồng lấn.
- [ ] R1-R4 có 18 static route tổng cộng.
- [ ] Có đúng 5 DHCP helper về `.40.2`.
- [ ] Không có VLAN, RIP, OSPF, EIGRP hoặc serial link.

### Services

- [ ] Năm DHCP pool đúng bảng và cấp IP thành công.
- [ ] DNS A/CNAME trả về `.40.4`.
- [ ] HTTP On và website đúng nội dung.
- [ ] Hai SSID dùng WPA2-PSK/AES.

### Client

- [ ] 25 client tầng 1-3 nhận DHCP đúng subnet.
- [ ] 7 PC tầng 4 và 3 server dùng static IP duy nhất.
- [ ] T01-T12 đều PASS.

### Artifact thô

- [ ] `topology.pkt` save và mở lại được.
- [ ] Chín running-config phản ánh thiết bị mạng cuối.
- [ ] `packet-tracer/web/index.html` khớp nội dung trên WEB-SRV.
- [ ] Không có file `.pkt` nháp bị nhầm là file chính.

Khi tất cả ô trên hoàn thành, phần mô phỏng kỹ thuật của đồ án đã hoàn thành ở
mức thô. Report, ảnh trình bày, video demo, link chia sẻ và đóng gói nộp bài là
giai đoạn riêng, không thực hiện trong tài liệu này.
