# Hướng dẫn thực hiện đồ án Packet Tracer Project 3

> Phạm vi: mạng văn phòng 4 tầng, nhóm 2 thành viên, Cisco Packet Tracer 8.2+.
>
> Trạng thái: tài liệu triển khai nền tảng; mọi `XX` và `TODO` phải được chốt trước khi nộp.

---

## 0. Mục đích và cách dùng tài liệu

Tài liệu này chuyển toàn bộ yêu cầu trong Project3.pdf thành quy trình có thể thực hiện.

Mục tiêu thứ nhất là giúp hai thành viên làm song song mà không mất dấu phụ thuộc.

Mục tiêu thứ hai là biến từng yêu cầu chấm điểm thành artifact và bằng chứng cụ thể.

Mục tiêu thứ ba là giảm lỗi cấu hình nhờ dùng một address plan và naming plan duy nhất.

Mục tiêu thứ tư là tạo đường kiểm thử lặp lại được trước khi quay demo và nộp bài.

Đọc lần đầu theo thứ tự từ đầu đến cuối.

Khi triển khai, dùng mục lục để đến đúng phần đang làm.

Khi debug, bắt đầu từ lớp vật lý rồi đi dần tới IP, route, service và application.

Khi viết báo cáo, dùng mã bằng chứng thay vì tìm ảnh thủ công.

Khi chuẩn bị nộp, chạy toàn bộ checklist cuối và script validation.

### 0.1. Ký hiệu

- `XX`: hai chữ số cuối MSSV được nhóm chọn.
- `TODO`: thông tin nhóm phải điền.
- `PASS`: kết quả đã được quan sát và có bằng chứng.
- `FAIL`: kết quả chưa đúng hoặc chưa đủ bằng chứng.
- `BLOCKED`: không thể tiếp tục do phụ thuộc chưa hoàn tất.
- `Owner`: người trực tiếp làm.
- `Reviewer`: người còn lại kiểm tra chéo.
- `Evidence ID`: mã ảnh hoặc đoạn video liên kết tới yêu cầu.
- `first usable`: địa chỉ host đầu tiên của subnet.
- `next hop`: địa chỉ router kế tiếp trên backbone.

### 0.2. Những điều không được tự suy diễn

- Không tự chọn `XX` nếu chưa thống nhất với cả nhóm.
- Không đổi prefix sau khi đã cấu hình DHCP mà không cập nhật toàn bộ artifact.
- Không thêm VLAN vì thấy tiện hơn.
- Không bật OSPF hoặc RIP để giảm số route.
- Không gán IP tĩnh cho client tầng 1 đến tầng 3 để chữa lỗi DHCP.
- Không dùng WRT300N thay Access Point-PT.
- Không dùng IP để thay cho domain trong acceptance test.
- Không coi ping thành công là đủ chứng minh DNS hoặc HTTP.
- Không coi file `.pkt` là nguồn duy nhất; luôn export config text.
- Không để ảnh không caption trong report.

### 0.3. Definition of Done cấp dự án

- [ ] `XX` được ghi trên cover và dùng nhất quán.
- [ ] Packet Tracer version thực tế được ghi trong report.
- [ ] Có đúng bốn router Cisco 2911.
- [ ] Có đúng một backbone switch không nối end-station.
- [ ] Có bốn department switch theo đề.
- [ ] Có hai Access Point-PT, không có WRT300N.
- [ ] Có ba Server-PT với vai trò tách biệt.
- [ ] Có bảy subnet không chồng lấn.
- [ ] Không có VLAN dùng để phân đoạn.
- [ ] Không có dynamic routing.
- [ ] Năm subnet client nhận DHCP đúng pool.
- [ ] Ba server dùng static IP đúng bảng.
- [ ] Tất cả subnet liên lạc được.
- [ ] DNS A và CNAME hoạt động.
- [ ] Website mở được bằng `www.mmt-XX.com`.
- [ ] Website hiển thị đủ MSSV và họ tên hai thành viên.
- [ ] Hai SSID đúng format và dùng WPA2-PSK.
- [ ] Đủ mười hai dòng test matrix và ảnh.
- [ ] Có ít nhất ba issues thật và resolutions.
- [ ] Demo dài 6-10 phút, có audio của cả hai người.
- [ ] `configs/` chứa running-config của chín thiết bị mạng.
- [ ] Tên folder và zip đúng hướng dẫn đã xác nhận.

---

## 1. Phân tích đề bài

### 1.1. Bối cảnh nghiệp vụ

Doanh nghiệp chuyển vào văn phòng mới có bốn tầng.

Mạng phải hỗ trợ nhân viên có dây, Wi-Fi, phòng họp và server nội bộ.

Nhóm đóng vai trò network engineering team.

Nhóm chịu trách nhiệm từ phân tích đến nghiệm thu.

Không có hạ tầng kế thừa cần migrate trong mô phỏng.

Mỗi khu vực là một miền IP riêng.

Kiến trúc cố ý đơn giản ở Layer 2 và rõ ràng ở Layer 3.

### 1.2. Nhu cầu theo tầng

| Tầng | Khu vực | Quy mô | Medium | Cấp địa chỉ |
|---:|---|---:|---|---|
| 1 | Hành chính | 10 user | Wired | DHCP |
| 1 | Staff/guest | tối đa 20 user | Wireless | DHCP |
| 2 | Kỹ thuật | 5 user | Wired | DHCP |
| 2 | Lãnh đạo | tối đa 5 user | Wired | DHCP |
| 3 | Phòng họp | tối đa 20 user | Wireless | DHCP |
| 4 | Server room | tối đa 10 host | Wired | Static |

### 1.3. Dịch vụ

DHCP phải tập trung trên một thiết bị ở tầng 4.

DHCP phải phục vụ năm subnet động.

R1, R2 và R3 phải relay DHCP broadcast.

DNS phải resolve `mmt-XX.com`.

DNS phải resolve `www.mmt-XX.com` qua CNAME.

WEB phải trả nội dung nhóm tự viết.

Mọi subnet phải liên thông qua static routing.

### 1.4. Ràng buộc kiến trúc

Mỗi floor có một router.

Bốn router cùng nối một backbone Ethernet switch.

Backbone là một transit subnet chung.

Mỗi LAN subnet gắn vào một cổng router riêng.

Mọi cable trong hệ thống là Copper Straight-Through.

Không dùng serial port.

Không dùng VLAN để tạo subnet.

### 1.5. Rủi ro trừ điểm

Không ghi Packet Tracer version có thể bị trừ 2 điểm.

Dùng dynamic routing có thể bị trừ 2 điểm phần routing.

Dùng VLAN có thể bị trừ 1 điểm.

Gán static IP cho client có thể bị trừ 1 điểm mỗi subnet vi phạm.

Test web bằng IP có thể bị trừ 0.5 điểm.

Dùng Home Wireless Router có thể bị trừ 1 điểm.

Nộp trễ bị trừ theo số ngày và có giới hạn chấp nhận.

### 1.6. Chiến lược tối ưu điểm

Ưu tiên DHCP vì hạng mục này có trọng số cao nhất.

Ưu tiên address plan và route table vì chúng là nền của mọi dịch vụ.

Chụp bằng chứng ngay khi một hạng mục pass.

Không đợi tới cuối mới export running-config.

Không quay demo trước khi test matrix pass hai lần liên tiếp.

---

## 2. Khởi tạo dự án và cộng tác Git

### 2.1. Điền biến dự án

Mở `docs/PROJECT_INFO.md`.

Điền tên nhóm.

Điền MSSV và họ tên thành viên 1.

Điền MSSV và họ tên thành viên 2.

Chọn MSSV cung cấp `XX`.

Giữ đúng hai chữ số, kể cả khi bắt đầu bằng 0.

Ghi domain sau khi thay `XX`.

Ghi SSID sau khi thay `XX`.

Chọn WPA2 passphrase dùng riêng cho lab.

Không commit passphrase nếu repository công khai.

Ghi deadline và quy ước tên zip.

Ghi Packet Tracer version sau khi kiểm tra About.

### 2.2. Phân vai

Thành viên A là Network lead.

Thành viên B là Services and evidence lead.

Network lead sở hữu address plan, cabling, router và static route.

Services lead sở hữu DHCP, DNS, WEB, Wi-Fi và evidence index.

Mỗi người review phần của người kia.

Cả hai cùng chịu trách nhiệm bài nộp cuối.

### 2.3. Quy tắc với file `.pkt`

Packet Tracer topology là file nhị phân.

Git không merge nội dung `.pkt` theo dòng.

Chỉ một người sửa `.pkt` tại một thời điểm.

Người sửa phải giữ topology lock.

Mọi thay đổi CLI phải được export ra text.

Ảnh bằng chứng phải đi cùng mốc cấu hình tương ứng.

Người nhận bàn giao phải chạy smoke test.

### 2.4. Nhánh và commit

Nhánh thiết kế dùng prefix `design/`.

Nhánh cấu hình dùng prefix `config/`.

Nhánh dịch vụ dùng prefix `service/`.

Nhánh tài liệu dùng prefix `docs/`.

Nhánh sửa lỗi dùng prefix `fix/`.

Commit cần nêu phạm vi và kết quả.

Không gom toàn bộ đồ án vào một commit duy nhất.

Không commit video thô vào Git.

### 2.5. Mốc đề xuất

| Mốc | Kết quả bắt buộc |
|---|---|
| M0 | Biến dự án và XX đã chốt |
| M1 | Address plan và sơ đồ đã review |
| M2 | Topology vật lý và basic config hoàn tất |
| M3 | Static routing pass toàn hệ thống |
| M4 | DHCP và relay pass năm subnet |
| M5 | DNS, WEB và Wi-Fi pass |
| M6 | Test matrix pass đủ |
| M7 | Report, configs và evidence hoàn tất |
| M8 | Demo và submission audit hoàn tất |

---

## 3. Kiến trúc logic và vật lý

### 3.1. Vai trò router

R1 là default gateway cho ADMIN và STAFF.

R1 là DHCP relay cho hai subnet tầng 1.

R2 là default gateway cho TECH và MGMT.

R2 là DHCP relay cho hai subnet tầng 2.

R3 là default gateway cho MEETING.

R3 là DHCP relay cho subnet tầng 3.

R4 là default gateway cho SERVER.

R4 không cần relay vì DHCP-SRV nằm cùng server subnet.

### 3.2. Vai trò switch

SW-CORE chỉ nối R1, R2, R3 và R4.

SW-HC nối R1 với các PC hành chính.

SW-KT nối R2 với các PC kỹ thuật.

SW-LD nối R2 với các PC lãnh đạo.

SW-SRV nối R4 với ba server.

Switch không phân đoạn bằng VLAN trong đồ án này.

### 3.3. Vai trò AP

AP-STAFF bridge WLAN tầng 1 vào cổng LAN của R1.

AP-MEETING bridge WLAN tầng 3 vào cổng LAN của R3.

AP không cấp DHCP.

AP không NAT.

Mỗi AP chỉ phát một SSID.

### 3.4. Port allocation

| Router | LAN 1 | LAN 2 | Backbone | Port chưa dùng |
|---|---|---|---|---|
| R1 | G0/0 ADMIN | G0/1 STAFF | G0/2 | Không có |
| R2 | G0/0 TECH | G0/1 MGMT | G0/2 | Không có |
| R3 | G0/0 MEETING | Không có | G0/1 | G0/2 |
| R4 | G0/0 SERVER | Không có | G0/1 | G0/2 |

### 3.5. Cable map

| A | Port A | B | Port B | Cable |
|---|---|---|---|---|
| R1 | G0/0 | SW-HC | G0/1 | Copper Straight-Through |
| R1 | G0/1 | AP-STAFF | Port0 | Copper Straight-Through |
| R1 | G0/2 | SW-CORE | Fa0/1 | Copper Straight-Through |
| R2 | G0/0 | SW-KT | G0/1 | Copper Straight-Through |
| R2 | G0/1 | SW-LD | G0/1 | Copper Straight-Through |
| R2 | G0/2 | SW-CORE | Fa0/2 | Copper Straight-Through |
| R3 | G0/0 | AP-MEETING | Port0 | Copper Straight-Through |
| R3 | G0/1 | SW-CORE | Fa0/3 | Copper Straight-Through |
| R4 | G0/0 | SW-SRV | G0/1 | Copper Straight-Through |
| R4 | G0/1 | SW-CORE | Fa0/4 | Copper Straight-Through |
| SW-SRV | Fa0/1 | DHCP-SRV | Fa0 | Copper Straight-Through |
| SW-SRV | Fa0/2 | DNS-SRV | Fa0 | Copper Straight-Through |
| SW-SRV | Fa0/3 | WEB-SRV | Fa0 | Copper Straight-Through |

### 3.6. Kiểm tra sơ đồ trước khi dựng

- [ ] Mỗi subnet có đúng một cổng router làm gateway.
- [ ] Không có end-station trên SW-CORE.
- [ ] R1 và R2 dùng đủ ba GigabitEthernet port.
- [ ] R3 và R4 dùng hai GigabitEthernet port.
- [ ] Không có cable serial.
- [ ] Không có VLAN label.
- [ ] Tất cả hostname đúng đề.
- [ ] Port label khớp cable map.
- [ ] Network label khớp address plan.
- [ ] Sơ đồ có thể đọc khi đưa vào report.

---

## 4. Kế hoạch địa chỉ

### 4.1. Nguyên tắc

Khối gốc là `172.XX.0.0/16`.

Mỗi area dùng một subnet riêng.

Backbone dùng một subnet riêng.

Gateway luôn là first usable.

Client DHCP bắt đầu từ `.10`.

Infrastructure được dành `.2-.9`.

Server dùng static IP.

Subnet không được chồng lấn.

### 4.2. Bảng địa chỉ chính

| Mã | Network | Mask | Gateway | Broadcast | Cấp phát |
|---|---|---|---|---|---|
| ADMIN | 172.XX.10.0/27 | 255.255.255.224 | 172.XX.10.1 | 172.XX.10.31 | DHCP |
| STAFF | 172.XX.20.0/27 | 255.255.255.224 | 172.XX.20.1 | 172.XX.20.31 | DHCP |
| TECH | 172.XX.30.0/28 | 255.255.255.240 | 172.XX.30.1 | 172.XX.30.15 | DHCP |
| MGMT | 172.XX.40.0/28 | 255.255.255.240 | 172.XX.40.1 | 172.XX.40.15 | DHCP |
| MEETING | 172.XX.50.0/27 | 255.255.255.224 | 172.XX.50.1 | 172.XX.50.31 | DHCP |
| SERVER | 172.XX.60.0/28 | 255.255.255.240 | 172.XX.60.1 | 172.XX.60.15 | Static |
| BACKBONE | 172.XX.255.248/29 | 255.255.255.248 | N/A | 172.XX.255.255 | Static |

### 4.3. Địa chỉ router backbone

R1 dùng `172.XX.255.249/29`.

R2 dùng `172.XX.255.250/29`.

R3 dùng `172.XX.255.251/29`.

R4 dùng `172.XX.255.252/29`.

`.253` và `.254` được để dự phòng.

### 4.4. Địa chỉ server

DHCP-SRV dùng `172.XX.60.10/28`.

DNS-SRV dùng `172.XX.60.11/28`.

WEB-SRV dùng `172.XX.60.12/28`.

Ba server dùng gateway `172.XX.60.1`.

DNS client và server trỏ về `172.XX.60.11`.

### 4.5. Lập luận subnetting

`/27` cung cấp 30 usable address.

`/27` đáp ứng tối đa 20 client Wi-Fi và hạ tầng.

`/27` cho ADMIN giữ headroom tăng trưởng.

`/28` cung cấp 14 usable address.

`/28` đáp ứng phòng 5 user và server room 10 host.

`/29` cung cấp 6 usable address.

`/29` đáp ứng bốn router backbone và hai địa chỉ dự phòng.

Third octet theo bội số 10 giúp ghi nhớ.

Giải pháp hy sinh một phần hiệu quả không gian `/16` để giảm lỗi vận hành.

### 4.6. Kiểm tra toán học

- [ ] `/27` có block size 32.
- [ ] `/27` mask là `255.255.255.224`.
- [ ] `/27` có 30 usable host.
- [ ] `/28` có block size 16.
- [ ] `/28` mask là `255.255.255.240`.
- [ ] `/28` có 14 usable host.
- [ ] `/29` có block size 8.
- [ ] `/29` mask là `255.255.255.248`.
- [ ] `/29` có 6 usable host.
- [ ] Không dùng network address cho interface.
- [ ] Không dùng broadcast address cho interface.
- [ ] Gateway thuộc đúng subnet.
- [ ] DHCP start và end thuộc đúng usable range.

---

## 5. Dựng topology trong Packet Tracer

### 5.1. Tạo file

Mở Cisco Packet Tracer 8.2 hoặc mới hơn.

Kiểm tra version trong Help hoặc About.

Ghi version vào `docs/PROJECT_INFO.md`.

Save As vào `packet-tracer/topology.pkt`.

Không save bản chính trong Downloads.

### 5.2. Đặt thiết bị

Kéo bốn Cisco 2911 vào workspace.

Đổi display name và hostname logic thành R1 đến R4.

Kéo năm Cisco 2960-24TT.

Đặt tên SW-CORE, SW-HC, SW-KT, SW-LD, SW-SRV.

Kéo hai Access Point-PT.

Đặt tên AP-STAFF và AP-MEETING.

Kéo ba Server-PT.

Đặt tên DHCP-SRV, DNS-SRV và WEB-SRV.

Kéo số client tối thiểu theo device inventory.

Đặt tên client dễ đọc theo khu vực.

### 5.3. Bố cục

Chia canvas thành bốn vùng theo tầng.

Đặt SW-CORE ở vị trí trung tâm.

Đặt router gần ranh giới từng tầng.

Đặt LAN switch hoặc AP cạnh router tương ứng.

Đặt server cùng SW-SRV ở tầng 4.

Chừa khoảng trống cho port và network label.

Không để cable giao nhau quá nhiều.

### 5.4. Nối cable

Chọn Copper Straight-Through.

Nối đúng từng dòng cable map.

Không dùng nút tự chọn cable khi chụp bằng chứng nếu cần chứng minh loại cable.

Chờ link chuyển xanh sau khi interface được no shutdown.

Nếu link đỏ, kiểm tra cả hai đầu và interface status.

### 5.5. Label

Gắn network/prefix cho bảy segment.

Gắn IP backbone cạnh mỗi router.

Gắn domain cạnh DNS-SRV và WEB-SRV.

Gắn SSID cạnh hai AP.

Gắn port name ở hai đầu các link router.

### 5.6. Checkpoint

- [ ] Save file.
- [ ] Commit file `.pkt` lần đầu.
- [ ] Chụp D01 topology tổng thể.
- [ ] Reviewer so cable map.
- [ ] Reviewer so hostname.
- [ ] Reviewer xác nhận không có VLAN/WRT/serial.

---

## 6. Basic configuration

### 6.1. Template router chung

```text
enable
configure terminal
hostname <R1|R2|R3|R4>
no ip domain-lookup
enable secret <ENABLE_SECRET>
service password-encryption
banner motd #Authorized lab access only#
line console 0
 password <CONSOLE_SECRET>
 login
 logging synchronous
 exit
line vty 0 4
 password <VTY_SECRET>
 login
 exit
end
copy running-config startup-config
```

Không dùng password thật của cá nhân.

Ghi chú password policy trong report nhưng không cần công khai secret.

### 6.2. R1 interfaces

```text
enable
configure terminal
interface GigabitEthernet0/0
 description ADMIN_LAN
 ip address 172.XX.10.1 255.255.255.224
 ip helper-address 172.XX.60.10
 no shutdown
 exit
interface GigabitEthernet0/1
 description STAFF_WIFI_LAN
 ip address 172.XX.20.1 255.255.255.224
 ip helper-address 172.XX.60.10
 no shutdown
 exit
interface GigabitEthernet0/2
 description BACKBONE_TO_SW_CORE
 ip address 172.XX.255.249 255.255.255.248
 no shutdown
 exit
end
copy running-config startup-config
```

### 6.3. R2 interfaces

```text
enable
configure terminal
interface GigabitEthernet0/0
 description TECH_LAN
 ip address 172.XX.30.1 255.255.255.240
 ip helper-address 172.XX.60.10
 no shutdown
 exit
interface GigabitEthernet0/1
 description MANAGEMENT_LAN
 ip address 172.XX.40.1 255.255.255.240
 ip helper-address 172.XX.60.10
 no shutdown
 exit
interface GigabitEthernet0/2
 description BACKBONE_TO_SW_CORE
 ip address 172.XX.255.250 255.255.255.248
 no shutdown
 exit
end
copy running-config startup-config
```

### 6.4. R3 interfaces

```text
enable
configure terminal
interface GigabitEthernet0/0
 description MEETING_WIFI_LAN
 ip address 172.XX.50.1 255.255.255.224
 ip helper-address 172.XX.60.10
 no shutdown
 exit
interface GigabitEthernet0/1
 description BACKBONE_TO_SW_CORE
 ip address 172.XX.255.251 255.255.255.248
 no shutdown
 exit
interface GigabitEthernet0/2
 description UNUSED
 shutdown
 exit
end
copy running-config startup-config
```

### 6.5. R4 interfaces

```text
enable
configure terminal
interface GigabitEthernet0/0
 description SERVER_LAN
 ip address 172.XX.60.1 255.255.255.240
 no shutdown
 exit
interface GigabitEthernet0/1
 description BACKBONE_TO_SW_CORE
 ip address 172.XX.255.252 255.255.255.248
 no shutdown
 exit
interface GigabitEthernet0/2
 description UNUSED
 shutdown
 exit
end
copy running-config startup-config
```

### 6.6. Kiểm tra interface

```text
show ip interface brief
show running-config | section interface
show interfaces description
```

Mọi interface đang dùng phải là `up/up`.

Interface unused nên là administratively down.

IP và mask phải trùng address plan.

Helper chỉ đặt trên năm cổng client LAN.

Không đặt helper trên backbone hoặc server interface.

### 6.7. Template switch chung

```text
enable
configure terminal
hostname <SW-NAME>
no ip domain-lookup
enable secret <ENABLE_SECRET>
service password-encryption
banner motd #Authorized lab access only#
line console 0
 password <CONSOLE_SECRET>
 login
 logging synchronous
 exit
line vty 0 4
 password <VTY_SECRET>
 login
 exit
end
copy running-config startup-config
```

Không tạo VLAN để phân đoạn.

Không cần IP management nếu nhóm không dùng quản trị từ xa.

Đặt description cho các port quan trọng nếu thời gian cho phép.

### 6.8. Kiểm tra basic config

- [ ] Hostname đúng trên 9 thiết bị mạng.
- [ ] Password được mã hóa ở mức lab bằng service password-encryption.
- [ ] Enable secret đã cấu hình.
- [ ] Console login hoạt động.
- [ ] VTY login hoạt động nếu test.
- [ ] Banner hiển thị.
- [ ] Running-config đã copy vào startup-config.
- [ ] Router interface có description.
- [ ] Switch port map khớp tài liệu.
- [ ] Không có lệnh router ospf.
- [ ] Không có lệnh router rip.
- [ ] Không có VLAN tùy chỉnh.

---

## 7. Static routing

### 7.1. Nguyên lý

Router tự biết các mạng directly connected.

IOS tạo route `C` cho connected network.

IOS tạo route `L` `/32` cho chính địa chỉ interface.

Nhóm chỉ thêm route `S` cho remote network.

Next hop phải thuộc backbone mà router đang directly connected.

Không trỏ next hop tới địa chỉ nằm sau một router khác.

### 7.2. Route của R1

```text
enable
configure terminal
ip route 172.XX.30.0 255.255.255.240 172.XX.255.250
ip route 172.XX.40.0 255.255.255.240 172.XX.255.250
ip route 172.XX.50.0 255.255.255.224 172.XX.255.251
ip route 172.XX.60.0 255.255.255.240 172.XX.255.252
end
copy running-config startup-config
```

### 7.3. Route của R2

```text
enable
configure terminal
ip route 172.XX.10.0 255.255.255.224 172.XX.255.249
ip route 172.XX.20.0 255.255.255.224 172.XX.255.249
ip route 172.XX.50.0 255.255.255.224 172.XX.255.251
ip route 172.XX.60.0 255.255.255.240 172.XX.255.252
end
copy running-config startup-config
```

### 7.4. Route của R3

```text
enable
configure terminal
ip route 172.XX.10.0 255.255.255.224 172.XX.255.249
ip route 172.XX.20.0 255.255.255.224 172.XX.255.249
ip route 172.XX.30.0 255.255.255.240 172.XX.255.250
ip route 172.XX.40.0 255.255.255.240 172.XX.255.250
ip route 172.XX.60.0 255.255.255.240 172.XX.255.252
end
copy running-config startup-config
```

### 7.5. Route của R4

```text
enable
configure terminal
ip route 172.XX.10.0 255.255.255.224 172.XX.255.249
ip route 172.XX.20.0 255.255.255.224 172.XX.255.249
ip route 172.XX.30.0 255.255.255.240 172.XX.255.250
ip route 172.XX.40.0 255.255.255.240 172.XX.255.250
ip route 172.XX.50.0 255.255.255.224 172.XX.255.251
end
copy running-config startup-config
```

### 7.6. Thứ tự kiểm tra

Ping các địa chỉ backbone trước.

Ping gateway remote tiếp theo.

Ping server sau khi server có gateway.

Ping client remote sau khi client có DHCP.

Chạy `show ip route` trên từng router.

Đếm đủ connected, local và static route.

### 7.7. Câu hỏi default route trên R1

Về mặt reachability, route remote của R1 có thể thay bằng một default route.

Default route phải trỏ tới một router láng giềng trên backbone.

Nếu trỏ R4, packet từ R1 tới TECH sẽ đi R1 -> R4 -> R2.

Đường này dài hơn đường trực tiếp R1 -> R2.

Nếu trỏ R2, lưu lượng tới SERVER hoặc MEETING có thể đi vòng qua R2.

Một default route cũng tạo phụ thuộc vào router trung gian được chọn.

Vì mọi router cùng trên một backbone, explicit route tới router sở hữu subnet là tối ưu và dễ giải thích hơn.

### 7.8. Giải thích C, L, S

`C` là connected route cho network của interface up/up.

`L` là local host route `/32` cho chính IP interface.

`S` là static route do quản trị viên cấu hình.

`S*` thường đánh dấu candidate default route nếu có route `0.0.0.0/0`.

Route connected biến mất nếu interface down.

Static route có thể không cài vào bảng nếu next hop không resolve được.

### 7.9. Checklist static routing

- [ ] R1 có 4 static routes.
- [ ] R2 có 4 static routes.
- [ ] R3 có 5 static routes.
- [ ] R4 có 5 static routes.
- [ ] Không cấu hình route tới connected network.
- [ ] Mỗi next hop là một IP backbone.
- [ ] Không có default route trong topology cuối nếu nhóm dùng thiết kế explicit.
- [ ] Không có RIP.
- [ ] Không có OSPF.
- [ ] Ping backbone pass.
- [ ] Ping remote gateway pass.
- [ ] Ping server từ tầng 1-3 pass.
- [ ] Ảnh T10A-D đọc được toàn bộ route table.

---

## 8. DHCP tập trung và relay

### 8.1. Cấu hình IP cho DHCP-SRV

Mở DHCP-SRV.

Chọn Desktop.

Chọn IP Configuration.

Chọn Static.

Nhập IP `172.XX.60.10`.

Nhập mask `255.255.255.240`.

Nhập gateway `172.XX.60.1`.

Nhập DNS `172.XX.60.11`.

Đóng cửa sổ và ping gateway.

### 8.2. Tạo pool

Mở Services.

Chọn DHCP.

Bật service On.

Tạo từng pool theo bảng chính.

Không dùng pool mặc định nếu thông số không đúng.

Sau khi nhập, bấm Add hoặc Save theo giao diện phiên bản.

Kiểm tra pool xuất hiện trong danh sách.

### 8.3. Thông số năm pool

| Pool | Gateway | DNS | Start | Mask | Max users |
|---|---|---|---|---|---:|
| POOL-ADMIN | 172.XX.10.1 | 172.XX.60.11 | 172.XX.10.10 | 255.255.255.224 | 10 |
| POOL-STAFF | 172.XX.20.1 | 172.XX.60.11 | 172.XX.20.10 | 255.255.255.224 | 20 |
| POOL-TECH | 172.XX.30.1 | 172.XX.60.11 | 172.XX.30.10 | 255.255.255.240 | 5 |
| POOL-MGMT | 172.XX.40.1 | 172.XX.60.11 | 172.XX.40.10 | 255.255.255.240 | 5 |
| POOL-MEETING | 172.XX.50.1 | 172.XX.60.11 | 172.XX.50.10 | 255.255.255.224 | 20 |

### 8.4. Relay agent

DHCP Discover ban đầu là broadcast.

Router không forward broadcast theo mặc định.

`ip helper-address` nhận broadcast tại LAN interface.

Router đóng gói và chuyển request unicast tới DHCP-SRV.

DHCP-SRV dùng gateway/giaddr để chọn đúng pool.

Do đó helper phải đặt trên interface gần client.

Helper không đặt trên interface gần server.

### 8.5. Client DHCP

Mở client.

Chọn Desktop.

Chọn IP Configuration.

Chọn DHCP.

Chờ trạng thái DHCP successful.

Nếu nhận `169.254.*`, quy trình DHCP đã fail.

Mở Command Prompt.

Chạy `ipconfig /all`.

So IP, mask, gateway, DNS và DHCP server với bảng.

### 8.6. Thứ tự debug DHCP

Kiểm tra client đang chọn DHCP.

Kiểm tra link từ client/AP/switch tới router.

Kiểm tra gateway interface up/up.

Kiểm tra helper đúng IP `172.XX.60.10`.

Kiểm tra route từ client router tới SERVER.

Kiểm tra route hồi đáp từ R4 về client subnet.

Kiểm tra DHCP-SRV ping được gateway.

Kiểm tra DHCP service On.

Kiểm tra pool network và mask đúng.

Kiểm tra start IP không phải network/broadcast.

Kiểm tra Max users chưa cạn.

Renew DHCP sau khi sửa.

### 8.7. Checklist DHCP

- [ ] Chỉ một thiết bị cấp DHCP.
- [ ] Thiết bị DHCP nằm ở tầng 4.
- [ ] Có đúng năm pool.
- [ ] Mỗi pool có gateway đúng subnet.
- [ ] Mỗi pool trả DNS-SRV.
- [ ] Mỗi pool có start IP đúng.
- [ ] Mỗi pool có mask đúng.
- [ ] Mỗi pool có Max users đúng nhu cầu.
- [ ] R1 có hai helper.
- [ ] R2 có hai helper.
- [ ] R3 có một helper.
- [ ] R4 không có helper không cần thiết.
- [ ] Có `ipconfig /all` cho năm subnet.
- [ ] Không client tầng 1-3 nào dùng static IP.

---

## 9. DNS và WEB

### 9.1. DNS-SRV static IP

IP là `172.XX.60.11`.

Mask là `255.255.255.240`.

Gateway là `172.XX.60.1`.

DNS có thể trỏ về chính `172.XX.60.11`.

Ping gateway trước khi cấu hình record.

### 9.2. A record

Mở Services trên DNS-SRV.

Chọn DNS.

Bật DNS service On.

Name là `mmt-XX.com`.

Type là `A Record`.

Address là `172.XX.60.12`.

Add record.

### 9.3. CNAME record

Name là `www.mmt-XX.com`.

Type là `CNAME`.

Target hoặc Host Name là `mmt-XX.com`.

Add record.

Tên label có thể khác theo Packet Tracer version.

Kết quả logic phải là alias trỏ tới A record.

### 9.4. WEB-SRV static IP

IP là `172.XX.60.12`.

Mask là `255.255.255.240`.

Gateway là `172.XX.60.1`.

DNS là `172.XX.60.11`.

Ping gateway và DNS-SRV.

### 9.5. Nội dung web

Mở Services trên WEB-SRV.

Chọn HTTP.

Bật HTTP On.

Mở `index.html`.

Thay trang Packet Tracer mặc định.

Dùng nội dung từ `packet-tracer/web/index.html`.

Điền tên công ty.

Điền tên nhóm.

Điền `XX`.

Điền MSSV và họ tên hai thành viên.

Save nội dung.

### 9.6. Kiểm thử phân lớp

Ping `172.XX.60.12` để kiểm tra network reachability.

Chạy `nslookup mmt-XX.com` để kiểm tra A record.

Chạy `nslookup www.mmt-XX.com` để kiểm tra CNAME.

Mở `http://www.mmt-XX.com` để kiểm tra HTTP.

Không dùng kết quả ping để tuyên bố HTTP pass.

Không dùng IP URL trong ảnh nghiệm thu.

### 9.7. Checklist DNS/WEB

- [ ] DNS service On.
- [ ] A record đúng name.
- [ ] A record đúng WEB-SRV IP.
- [ ] CNAME đúng alias.
- [ ] CNAME đúng target.
- [ ] DHCP pool trả DNS-SRV IP.
- [ ] WEB service On.
- [ ] Trang mặc định đã được thay.
- [ ] Website có tên công ty.
- [ ] Website có tên nhóm.
- [ ] Website có XX.
- [ ] Website có bảng hai thành viên.
- [ ] Client mỗi subnet mở domain thành công.

---

## 10. Wi-Fi

### 10.1. AP-STAFF

Nối AP-STAFF trực tiếp vào R1/G0/1.

Đặt SSID `MMT-XX-STAFF`.

Chọn WPA2-PSK.

Chọn AES nếu giao diện yêu cầu encryption type.

Nhập passphrase nhóm đã chốt.

Không bật DHCP hoặc NAT.

Đặt IP management `172.XX.20.2/27` nếu model cho phép và nhóm cần.

### 10.2. AP-MEETING

Nối AP-MEETING trực tiếp vào R3/G0/0.

Đặt SSID `MMT-XX-MEETING`.

Chọn WPA2-PSK.

Chọn AES nếu giao diện yêu cầu.

Nhập passphrase nhóm đã chốt.

Không bật DHCP hoặc NAT.

Đặt IP management `172.XX.50.2/27` nếu model cho phép và nhóm cần.

### 10.3. Laptop-PT

Mở tab Physical.

Tắt nguồn laptop.

Tháo module Ethernet nếu khe yêu cầu.

Gắn module WPC300N.

Bật nguồn laptop.

Mở Desktop.

Mở PC Wireless.

Chọn đúng SSID.

Nhập đúng WPA2 passphrase.

Chọn DHCP trong IP Configuration.

### 10.4. Smartphone-PT

Mở wireless configuration.

Chọn SSID tương ứng.

Chọn đúng security mode.

Nhập passphrase.

Đảm bảo IP mode là DHCP.

### 10.5. Debug Wi-Fi

Nếu không thấy SSID, kiểm tra AP power và SSID broadcast.

Nếu authentication fail, kiểm tra WPA2 mode và passphrase.

Nếu kết nối nhưng không có IP, debug DHCP relay.

Nếu có IP sai subnet, kiểm tra AP đang nối nhầm router port.

Nếu ping gateway fail, kiểm tra link AP-router và mask.

Nếu domain fail nhưng ping IP pass, debug DNS.

### 10.6. Checklist Wi-Fi

- [ ] Có đúng hai AP.
- [ ] Không có WRT300N.
- [ ] SSID theo đúng format.
- [ ] Mỗi AP phát một SSID.
- [ ] Cả hai dùng WPA2-PSK.
- [ ] Laptop có WPC300N.
- [ ] Staff client nhận subnet STAFF.
- [ ] Meeting client nhận subnet MEETING.
- [ ] Wireless client ping gateway được.
- [ ] Wireless client mở website bằng domain được.

---

## 11. Quy trình kiểm thử tổng thể

### 11.1. Freeze trước test

Save topology.

Export running-config.

Ghi commit hoặc checkpoint name.

Không thay address plan giữa test.

Đồng bộ đồng hồ và tên ảnh.

Xóa command prompt cũ nếu gây nhầm.

### 11.2. Thứ tự test khuyến nghị

Test interface local.

Test backbone.

Test static route tới remote gateway.

Test DHCP cho từng subnet.

Test ping giữa client.

Test DNS A record.

Test DNS CNAME.

Test website bằng domain.

Test Wi-Fi end-to-end.

Test route table.

Chạy Simulation Mode.

### 11.3. Quy tắc ghi actual result

Không chỉ ghi `Success`.

Ghi source IP quan sát được.

Ghi destination hoặc domain.

Ghi packet loss hoặc resolved IP.

Ghi timestamp hoặc test run ID nếu cần.

Gắn đúng Evidence ID.

Reviewer phải mở ảnh và xác nhận.

### 11.4. Quy tắc retest

Mỗi fix phải chạy lại test fail.

Fix route cần chạy thêm test đường về.

Fix DHCP cần renew client.

Fix DNS cần chạy lại nslookup và browser.

Fix AP cần test cả DHCP và HTTP.

Không dùng ảnh trước fix làm bằng chứng cuối.

---

## 12. Simulation Mode

### 12.1. Luồng DHCP

Chuyển Packet Tracer sang Simulation Mode.

Lọc DHCP và ARP nếu cần.

Cho một client tầng 1 renew địa chỉ.

Quan sát DHCP Discover từ client.

Quan sát R1 nhận broadcast trên LAN.

Quan sát R1 relay unicast tới DHCP-SRV.

Quan sát Offer quay về qua R4 và R1.

Quan sát Request và ACK.

Ghi vai trò của `ip helper-address`.

Chụp hoặc quay đoạn luồng rõ ràng.

### 12.2. Luồng DNS rồi HTTP

Xóa cache hoặc dùng client chưa truy cập domain nếu cần.

Mở browser tới `http://www.mmt-XX.com`.

Quan sát DNS query đi tới DNS-SRV.

Quan sát DNS response trả WEB-SRV IP.

Quan sát TCP handshake tới WEB-SRV.

Quan sát HTTP request và response.

Giải thích DNS xảy ra trước HTTP vì client cần resolve domain.

### 12.3. Nội dung đưa vào report

Nêu source và destination.

Nêu protocol theo từng bước.

Nêu router path.

Nêu điểm thay đổi từ broadcast sang unicast trong DHCP relay.

Nêu kết quả cuối.

Không cần chụp quá nhiều frame trùng nhau.

---

## 13. Troubleshooting theo lớp

### 13.1. Layer 1

Kiểm tra thiết bị có nguồn.

Kiểm tra cable đúng loại.

Kiểm tra đúng port.

Kiểm tra interface đã `no shutdown`.

Kiểm tra link light.

Kiểm tra module Wi-Fi đã gắn khi laptop tắt nguồn.

### 13.2. Layer 2

Kiểm tra switch port đang up.

Kiểm tra AP bridge vào đúng subnet.

Kiểm tra SSID và WPA2.

Kiểm tra không có WRT/NAT ngoài ý muốn.

Kiểm tra SW-CORE không nối end-station.

### 13.3. Layer 3

Kiểm tra IP.

Kiểm tra mask.

Kiểm tra gateway.

Kiểm tra subnet membership.

Kiểm tra `show ip interface brief`.

Kiểm tra connected route.

Kiểm tra static route và next hop.

Kiểm tra return path.

### 13.4. DHCP

Kiểm tra helper ở interface client-facing.

Kiểm tra route hai chiều tới DHCP-SRV.

Kiểm tra service On.

Kiểm tra đúng pool.

Kiểm tra Max users.

Kiểm tra client renew.

### 13.5. DNS

Kiểm tra client nhận DNS qua DHCP.

Kiểm tra ping DNS-SRV.

Kiểm tra DNS service On.

Kiểm tra spelling domain.

Kiểm tra A record.

Kiểm tra CNAME target.

### 13.6. HTTP

Kiểm tra ping WEB-SRV.

Kiểm tra HTTP service On.

Kiểm tra index.html đã Save.

Kiểm tra URL có `http://` và đúng domain.

Kiểm tra DNS trước khi debug HTML.

### 13.7. Quy tắc chẩn đoán

Chỉ thay một biến tại một thời điểm.

Ghi lại triệu chứng trước khi sửa.

Chụp bằng chứng lệnh chẩn đoán quan trọng.

Xác định nguyên nhân gốc, không chỉ triệu chứng.

Retest test case gốc.

Chạy thêm regression test liên quan.

Ghi issue vào `docs/ISSUES.md`.

---

## 14. Báo cáo kỹ thuật

### 14.1. Cover

Tên môn.

Tên project.

Tên nhóm.

Danh sách hai thành viên.

MSSV và họ tên đầy đủ.

Giá trị XX.

Domain.

Packet Tracer version.

Ngày nộp.

### 14.2. Phân công

Dùng bảng task, owner, reviewer, artifact và trạng thái.

Thể hiện cả hai người có phần kỹ thuật và thuyết minh.

Không chỉ ghi tỉ lệ phần trăm chung chung.

### 14.3. Phân tích nhu cầu

Nêu số user từng khu vực.

Nêu wired/wireless.

Nêu dynamic/static addressing.

Nêu dịch vụ.

Nêu ràng buộc không VLAN và static routing.

### 14.4. Sơ đồ logic

Chèn sơ đồ nhóm tự vẽ.

Ghi hostname.

Ghi interface/port.

Ghi IP và mask.

Ghi network/prefix.

Ghi SSID và server role.

### 14.5. Address planning

Chèn bảng đủ network, prefix, mask, gateway, usable range và broadcast.

Giải thích tiêu chí memorability.

Giải thích scalability.

Giải thích address efficiency.

Nêu ưu và nhược điểm.

Nêu convention first usable.

### 14.6. Equipment table

Liệt kê mọi router.

Liệt kê mọi switch.

Liệt kê AP.

Liệt kê server.

Liệt kê client kiểm thử.

Ghi location, type, name, model, function, port và IP.

### 14.7. Deployment

Mỗi nhóm cấu hình có ảnh.

Mỗi ảnh có caption.

Caption nêu thao tác và kết quả.

Không chèn ảnh quá nhỏ.

Không để lộ phần desktop không liên quan.

### 14.8. Verification

Chèn đủ test matrix.

Ghi command thật.

Ghi actual result.

Gắn Evidence ID.

Giải thích C, L, S.

Trả lời câu hỏi default route R1.

Mô tả hai luồng Simulation Mode.

### 14.9. Issues and Resolutions

Có ít nhất ba issue thật.

Mỗi issue có triệu chứng.

Mỗi issue có nguyên nhân gốc.

Mỗi issue có cách sửa.

Mỗi issue có retest.

### 14.10. Kết luận

Đối chiếu kết quả với yêu cầu.

Nêu hạn chế của mô phỏng.

Đề xuất future extension.

Phân biệt extension với topology hiện tại.

---

## 15. Demo video

### 15.1. Chuẩn bị

Đóng ứng dụng có notification cá nhân.

Kiểm tra microphone.

Kiểm tra độ phân giải.

Mở sẵn topology và các cửa sổ cần dùng.

Chuẩn bị danh sách test theo thứ tự.

Đảm bảo topology vừa save.

### 15.2. Nội dung

Giới thiệu thành viên và XX.

Giới thiệu topology.

Giải thích subnet.

Hiển thị static route.

Hiển thị DHCP và relay.

Hiển thị DNS/WEB.

Hiển thị hai WLAN.

Chạy test matrix theo nhóm.

Hiển thị Simulation Mode ngắn.

Kết luận.

### 15.3. Chất lượng

Video dài 6-10 phút.

Audio rõ.

Cả hai thành viên nói.

IP và lệnh đọc được.

Không tua nhanh qua kết quả quan trọng.

Không cắt mất domain trên browser.

### 15.4. Link video

Nếu nộp link, quyền xem phải đúng.

Test link trong cửa sổ ẩn danh.

Không yêu cầu người chấm request access.

Ghi link trong file text nếu cấu trúc nộp yêu cầu.

---

## 16. Đóng gói bài nộp

### 16.1. Artifact bắt buộc

`topology.pkt`.

`Report.pdf`.

`configs/R1.txt`.

`configs/R2.txt`.

`configs/R3.txt`.

`configs/R4.txt`.

`configs/SW-CORE.txt`.

`configs/SW-HC.txt`.

`configs/SW-KT.txt`.

`configs/SW-LD.txt`.

`configs/SW-SRV.txt`.

`demo.mp4` hoặc link.

### 16.2. Audit trước nén

Mở topology bằng đúng Packet Tracer version.

Chạy ba smoke test.

Mở Report.pdf từ đầu đến cuối.

Kiểm tra ảnh không vỡ.

Kiểm tra font không lỗi.

Kiểm tra page number.

Kiểm tra config file không còn placeholder.

Kiểm tra video hoặc link.

Kiểm tra tên folder.

Kiểm tra tên zip.

### 16.3. Lưu ý nhóm hai người

Đề minh họa tên file bằng ba MSSV.

Nhóm hai người phải xác nhận quy ước với giảng viên.

Không tự thêm chuỗi giả làm MSSV thứ ba.

Lưu bằng chứng xác nhận nếu có.

---

## 17. Phiếu kiểm tra chi tiết theo subnet

Mỗi phiếu được hoàn tất bởi Owner và ký review bởi thành viên còn lại.

### 17.1. Subnet ADMIN

- Khu vực: Hành chính tầng 1.
- Network: `172.XX.10.0/27`.
- Mask: `255.255.255.224`.
- Gateway: `172.XX.10.1`.
- Router/interface: `R1/G0/0`.
- Thiết bị Layer 2/peer: `SW-HC`.
- Phương thức cấp IP: DHCP.
- Địa chỉ bắt đầu tham chiếu: `172.XX.10.10`.
- Quy mô: 10.

#### Thiết kế

- [ ] Network address đúng ranh giới prefix.
- [ ] Mask dotted-decimal đúng với prefix.
- [ ] Gateway không phải network hoặc broadcast.
- [ ] Gateway theo convention first usable.
- [ ] Không chồng lấn subnet khác.
- [ ] Network label xuất hiện trên topology.
- [ ] Router interface label xuất hiện trên topology.
- [ ] Address plan và equipment table trùng nhau.

#### Triển khai

- [ ] Cable đúng theo cable map.
- [ ] Interface có description.
- [ ] Interface có IP đúng.
- [ ] Interface có mask đúng.
- [ ] Interface đã no shutdown nếu sử dụng.
- [ ] Peer device đúng hostname.
- [ ] End device được đặt tên rõ ràng.
- [ ] Không thêm VLAN để tạo segment.

#### Dịch vụ và kiểm thử

- [ ] Phương thức cấp IP đúng yêu cầu.
- [ ] Default gateway trên host đúng.
- [ ] DNS trên host đúng 172.XX.60.11 khi áp dụng.
- [ ] Ping gateway pass.
- [ ] Ping một remote subnet pass.
- [ ] Ping WEB-SRV pass.
- [ ] Resolve mmt-XX.com pass.
- [ ] Mở www.mmt-XX.com pass.
- [ ] Evidence ID đã ghi.
- [ ] Reviewer đã xác nhận ảnh đọc được.

### 17.2. Subnet STAFF

- Khu vực: Wi-Fi staff/guest tầng 1.
- Network: `172.XX.20.0/27`.
- Mask: `255.255.255.224`.
- Gateway: `172.XX.20.1`.
- Router/interface: `R1/G0/1`.
- Thiết bị Layer 2/peer: `AP-STAFF`.
- Phương thức cấp IP: DHCP.
- Địa chỉ bắt đầu tham chiếu: `172.XX.20.10`.
- Quy mô: 20.

#### Thiết kế

- [ ] Network address đúng ranh giới prefix.
- [ ] Mask dotted-decimal đúng với prefix.
- [ ] Gateway không phải network hoặc broadcast.
- [ ] Gateway theo convention first usable.
- [ ] Không chồng lấn subnet khác.
- [ ] Network label xuất hiện trên topology.
- [ ] Router interface label xuất hiện trên topology.
- [ ] Address plan và equipment table trùng nhau.

#### Triển khai

- [ ] Cable đúng theo cable map.
- [ ] Interface có description.
- [ ] Interface có IP đúng.
- [ ] Interface có mask đúng.
- [ ] Interface đã no shutdown nếu sử dụng.
- [ ] Peer device đúng hostname.
- [ ] End device được đặt tên rõ ràng.
- [ ] Không thêm VLAN để tạo segment.

#### Dịch vụ và kiểm thử

- [ ] Phương thức cấp IP đúng yêu cầu.
- [ ] Default gateway trên host đúng.
- [ ] DNS trên host đúng 172.XX.60.11 khi áp dụng.
- [ ] Ping gateway pass.
- [ ] Ping một remote subnet pass.
- [ ] Ping WEB-SRV pass.
- [ ] Resolve mmt-XX.com pass.
- [ ] Mở www.mmt-XX.com pass.
- [ ] Evidence ID đã ghi.
- [ ] Reviewer đã xác nhận ảnh đọc được.

### 17.3. Subnet TECH

- Khu vực: Kỹ thuật tầng 2.
- Network: `172.XX.30.0/28`.
- Mask: `255.255.255.240`.
- Gateway: `172.XX.30.1`.
- Router/interface: `R2/G0/0`.
- Thiết bị Layer 2/peer: `SW-KT`.
- Phương thức cấp IP: DHCP.
- Địa chỉ bắt đầu tham chiếu: `172.XX.30.10`.
- Quy mô: 5.

#### Thiết kế

- [ ] Network address đúng ranh giới prefix.
- [ ] Mask dotted-decimal đúng với prefix.
- [ ] Gateway không phải network hoặc broadcast.
- [ ] Gateway theo convention first usable.
- [ ] Không chồng lấn subnet khác.
- [ ] Network label xuất hiện trên topology.
- [ ] Router interface label xuất hiện trên topology.
- [ ] Address plan và equipment table trùng nhau.

#### Triển khai

- [ ] Cable đúng theo cable map.
- [ ] Interface có description.
- [ ] Interface có IP đúng.
- [ ] Interface có mask đúng.
- [ ] Interface đã no shutdown nếu sử dụng.
- [ ] Peer device đúng hostname.
- [ ] End device được đặt tên rõ ràng.
- [ ] Không thêm VLAN để tạo segment.

#### Dịch vụ và kiểm thử

- [ ] Phương thức cấp IP đúng yêu cầu.
- [ ] Default gateway trên host đúng.
- [ ] DNS trên host đúng 172.XX.60.11 khi áp dụng.
- [ ] Ping gateway pass.
- [ ] Ping một remote subnet pass.
- [ ] Ping WEB-SRV pass.
- [ ] Resolve mmt-XX.com pass.
- [ ] Mở www.mmt-XX.com pass.
- [ ] Evidence ID đã ghi.
- [ ] Reviewer đã xác nhận ảnh đọc được.

### 17.4. Subnet MGMT

- Khu vực: Lãnh đạo tầng 2.
- Network: `172.XX.40.0/28`.
- Mask: `255.255.255.240`.
- Gateway: `172.XX.40.1`.
- Router/interface: `R2/G0/1`.
- Thiết bị Layer 2/peer: `SW-LD`.
- Phương thức cấp IP: DHCP.
- Địa chỉ bắt đầu tham chiếu: `172.XX.40.10`.
- Quy mô: 5.

#### Thiết kế

- [ ] Network address đúng ranh giới prefix.
- [ ] Mask dotted-decimal đúng với prefix.
- [ ] Gateway không phải network hoặc broadcast.
- [ ] Gateway theo convention first usable.
- [ ] Không chồng lấn subnet khác.
- [ ] Network label xuất hiện trên topology.
- [ ] Router interface label xuất hiện trên topology.
- [ ] Address plan và equipment table trùng nhau.

#### Triển khai

- [ ] Cable đúng theo cable map.
- [ ] Interface có description.
- [ ] Interface có IP đúng.
- [ ] Interface có mask đúng.
- [ ] Interface đã no shutdown nếu sử dụng.
- [ ] Peer device đúng hostname.
- [ ] End device được đặt tên rõ ràng.
- [ ] Không thêm VLAN để tạo segment.

#### Dịch vụ và kiểm thử

- [ ] Phương thức cấp IP đúng yêu cầu.
- [ ] Default gateway trên host đúng.
- [ ] DNS trên host đúng 172.XX.60.11 khi áp dụng.
- [ ] Ping gateway pass.
- [ ] Ping một remote subnet pass.
- [ ] Ping WEB-SRV pass.
- [ ] Resolve mmt-XX.com pass.
- [ ] Mở www.mmt-XX.com pass.
- [ ] Evidence ID đã ghi.
- [ ] Reviewer đã xác nhận ảnh đọc được.

### 17.5. Subnet MEETING

- Khu vực: Wi-Fi phòng họp tầng 3.
- Network: `172.XX.50.0/27`.
- Mask: `255.255.255.224`.
- Gateway: `172.XX.50.1`.
- Router/interface: `R3/G0/0`.
- Thiết bị Layer 2/peer: `AP-MEETING`.
- Phương thức cấp IP: DHCP.
- Địa chỉ bắt đầu tham chiếu: `172.XX.50.10`.
- Quy mô: 20.

#### Thiết kế

- [ ] Network address đúng ranh giới prefix.
- [ ] Mask dotted-decimal đúng với prefix.
- [ ] Gateway không phải network hoặc broadcast.
- [ ] Gateway theo convention first usable.
- [ ] Không chồng lấn subnet khác.
- [ ] Network label xuất hiện trên topology.
- [ ] Router interface label xuất hiện trên topology.
- [ ] Address plan và equipment table trùng nhau.

#### Triển khai

- [ ] Cable đúng theo cable map.
- [ ] Interface có description.
- [ ] Interface có IP đúng.
- [ ] Interface có mask đúng.
- [ ] Interface đã no shutdown nếu sử dụng.
- [ ] Peer device đúng hostname.
- [ ] End device được đặt tên rõ ràng.
- [ ] Không thêm VLAN để tạo segment.

#### Dịch vụ và kiểm thử

- [ ] Phương thức cấp IP đúng yêu cầu.
- [ ] Default gateway trên host đúng.
- [ ] DNS trên host đúng 172.XX.60.11 khi áp dụng.
- [ ] Ping gateway pass.
- [ ] Ping một remote subnet pass.
- [ ] Ping WEB-SRV pass.
- [ ] Resolve mmt-XX.com pass.
- [ ] Mở www.mmt-XX.com pass.
- [ ] Evidence ID đã ghi.
- [ ] Reviewer đã xác nhận ảnh đọc được.

### 17.6. Subnet SERVER

- Khu vực: Server room tầng 4.
- Network: `172.XX.60.0/28`.
- Mask: `255.255.255.240`.
- Gateway: `172.XX.60.1`.
- Router/interface: `R4/G0/0`.
- Thiết bị Layer 2/peer: `SW-SRV`.
- Phương thức cấp IP: Static.
- Địa chỉ bắt đầu tham chiếu: `172.XX.60.10`.
- Quy mô: 10 hosts.

#### Thiết kế

- [ ] Network address đúng ranh giới prefix.
- [ ] Mask dotted-decimal đúng với prefix.
- [ ] Gateway không phải network hoặc broadcast.
- [ ] Gateway theo convention first usable.
- [ ] Không chồng lấn subnet khác.
- [ ] Network label xuất hiện trên topology.
- [ ] Router interface label xuất hiện trên topology.
- [ ] Address plan và equipment table trùng nhau.

#### Triển khai

- [ ] Cable đúng theo cable map.
- [ ] Interface có description.
- [ ] Interface có IP đúng.
- [ ] Interface có mask đúng.
- [ ] Interface đã no shutdown nếu sử dụng.
- [ ] Peer device đúng hostname.
- [ ] End device được đặt tên rõ ràng.
- [ ] Không thêm VLAN để tạo segment.

#### Dịch vụ và kiểm thử

- [ ] Phương thức cấp IP đúng yêu cầu.
- [ ] Default gateway trên host đúng.
- [ ] DNS trên host đúng 172.XX.60.11 khi áp dụng.
- [ ] Ping gateway pass.
- [ ] Ping một remote subnet pass.
- [ ] Ping WEB-SRV pass.
- [ ] Resolve mmt-XX.com pass.
- [ ] Mở www.mmt-XX.com pass.
- [ ] Evidence ID đã ghi.
- [ ] Reviewer đã xác nhận ảnh đọc được.

### 17.7. Subnet BACKBONE

- Khu vực: Transit bốn router.
- Network: `172.XX.255.248/29`.
- Mask: `255.255.255.248`.
- Gateway: `Không áp dụng`.
- Router/interface: `R1-R4`.
- Thiết bị Layer 2/peer: `SW-CORE`.
- Phương thức cấp IP: Static.
- Địa chỉ bắt đầu tham chiếu: `172.XX.255.249`.
- Quy mô: 4 router ports.

#### Thiết kế

- [ ] Network address đúng ranh giới prefix.
- [ ] Mask dotted-decimal đúng với prefix.
- [ ] Gateway không phải network hoặc broadcast.
- [ ] Gateway theo convention first usable.
- [ ] Không chồng lấn subnet khác.
- [ ] Network label xuất hiện trên topology.
- [ ] Router interface label xuất hiện trên topology.
- [ ] Address plan và equipment table trùng nhau.

#### Triển khai

- [ ] Cable đúng theo cable map.
- [ ] Interface có description.
- [ ] Interface có IP đúng.
- [ ] Interface có mask đúng.
- [ ] Interface đã no shutdown nếu sử dụng.
- [ ] Peer device đúng hostname.
- [ ] End device được đặt tên rõ ràng.
- [ ] Không thêm VLAN để tạo segment.

#### Dịch vụ và kiểm thử

- [ ] Phương thức cấp IP đúng yêu cầu.
- [ ] Default gateway trên host đúng.
- [ ] DNS trên host đúng 172.XX.60.11 khi áp dụng.
- [ ] Ping gateway pass.
- [ ] Ping một remote subnet pass.
- [ ] Ping WEB-SRV pass.
- [ ] Resolve mmt-XX.com pass.
- [ ] Mở www.mmt-XX.com pass.
- [ ] Evidence ID đã ghi.
- [ ] Reviewer đã xác nhận ảnh đọc được.

## 18. Phiếu kiểm tra chi tiết theo thiết bị

Phiếu này dùng để review chéo giữa topology, running-config, ảnh và report.

### 18.1. R1

- Loại: Cisco 2911 router.
- Vị trí: Tầng 1.
- Vai trò: Gateway ADMIN/STAFF, DHCP relay, static routing.
- Port/IP chính: `G0/0 G0/1 G0/2`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.2. R2

- Loại: Cisco 2911 router.
- Vị trí: Tầng 2.
- Vai trò: Gateway TECH/MGMT, DHCP relay, static routing.
- Port/IP chính: `G0/0 G0/1 G0/2`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.3. R3

- Loại: Cisco 2911 router.
- Vị trí: Tầng 3.
- Vai trò: Gateway MEETING, DHCP relay, static routing.
- Port/IP chính: `G0/0 G0/1`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.4. R4

- Loại: Cisco 2911 router.
- Vị trí: Tầng 4.
- Vai trò: Gateway SERVER và static routing.
- Port/IP chính: `G0/0 G0/1`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.5. SW-CORE

- Loại: Cisco 2960-24TT switch.
- Vị trí: Technical closet.
- Vai trò: Backbone transit only.
- Port/IP chính: `Fa0/1-Fa0/4`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.6. SW-HC

- Loại: Cisco 2960-24TT switch.
- Vị trí: Tầng 1.
- Vai trò: Access switch ADMIN.
- Port/IP chính: `G0/1 Fa0/1 Fa0/2`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.7. SW-KT

- Loại: Cisco 2960-24TT switch.
- Vị trí: Tầng 2.
- Vai trò: Access switch TECH.
- Port/IP chính: `G0/1 Fa0/1 Fa0/2`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.8. SW-LD

- Loại: Cisco 2960-24TT switch.
- Vị trí: Tầng 2.
- Vai trò: Access switch MGMT.
- Port/IP chính: `G0/1 Fa0/1`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.9. SW-SRV

- Loại: Cisco 2960-24TT switch.
- Vị trí: Tầng 4.
- Vai trò: Access switch SERVER.
- Port/IP chính: `G0/1 Fa0/1-Fa0/3`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.10. AP-STAFF

- Loại: Access Point-PT.
- Vị trí: Tầng 1.
- Vai trò: Bridge SSID MMT-XX-STAFF.
- Port/IP chính: `Port0`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.11. AP-MEETING

- Loại: Access Point-PT.
- Vị trí: Tầng 3.
- Vai trò: Bridge SSID MMT-XX-MEETING.
- Port/IP chính: `Port0`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.12. DHCP-SRV

- Loại: Server-PT.
- Vị trí: Tầng 4.
- Vai trò: Năm DHCP pool tập trung.
- Port/IP chính: `Fa0 172.XX.60.10`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.13. DNS-SRV

- Loại: Server-PT.
- Vị trí: Tầng 4.
- Vai trò: A record và CNAME.
- Port/IP chính: `Fa0 172.XX.60.11`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.14. WEB-SRV

- Loại: Server-PT.
- Vị trí: Tầng 4.
- Vai trò: HTTP website nội bộ.
- Port/IP chính: `Fa0 172.XX.60.12`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.15. PC-HC-01

- Loại: PC-PT.
- Vị trí: Tầng 1.
- Vai trò: Client kiểm thử ADMIN.
- Port/IP chính: `Fa0 DHCP`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.16. LAP-STAFF-01

- Loại: Laptop-PT.
- Vị trí: Tầng 1.
- Vai trò: Client kiểm thử STAFF.
- Port/IP chính: `WPC300N DHCP`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.17. PC-KT-01

- Loại: PC-PT.
- Vị trí: Tầng 2.
- Vai trò: Client kiểm thử TECH.
- Port/IP chính: `Fa0 DHCP`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.18. PC-LD-01

- Loại: PC-PT.
- Vị trí: Tầng 2.
- Vai trò: Client kiểm thử MGMT.
- Port/IP chính: `Fa0 DHCP`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

### 18.19. LAP-MEETING-01

- Loại: Laptop-PT.
- Vị trí: Tầng 3.
- Vai trò: Client kiểm thử MEETING.
- Port/IP chính: `WPC300N DHCP`.

#### Nhận dạng

- [ ] Tên hiển thị trên topology đúng.
- [ ] Hostname hoặc display name đúng.
- [ ] Vị trí trên sơ đồ đúng tầng.
- [ ] Label không che cable hoặc port.
- [ ] Model đúng yêu cầu đề.

#### Kết nối

- [ ] Đúng peer device.
- [ ] Đúng local port.
- [ ] Đúng remote port.
- [ ] Đúng cable hoặc wireless association.
- [ ] Link ở trạng thái hoạt động.
- [ ] Không có kết nối ngoài thiết kế.

#### Cấu hình

- [ ] Thông số khớp address plan khi có IP.
- [ ] Gateway hoặc route khớp thiết kế khi áp dụng.
- [ ] Service cần thiết đang On khi áp dụng.
- [ ] Không bật service ngoài ý muốn.
- [ ] Cấu hình đã Save.
- [ ] Running-config đã export nếu là router/switch.

#### Nghiệm thu

- [ ] Có ít nhất một test chứng minh vai trò.
- [ ] Ảnh có thể đọc tên thiết bị.
- [ ] Ảnh có thể đọc kết quả chính.
- [ ] Evidence ID đã ghi vào index.
- [ ] Report mô tả đúng vai trò.
- [ ] Reviewer đã ký xác nhận.

## 19. Phiếu xác minh từng static route


### 19.1. R1 tới TECH

- Destination: `172.XX.30.0/28`.
- Next hop: `172.XX.255.250`.
- Router sở hữu subnet: `R2`.
```text
ip route 172.XX.30.0 255.255.255.240 172.XX.255.250
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.2. R1 tới MGMT

- Destination: `172.XX.40.0/28`.
- Next hop: `172.XX.255.250`.
- Router sở hữu subnet: `R2`.
```text
ip route 172.XX.40.0 255.255.255.240 172.XX.255.250
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.3. R1 tới MEETING

- Destination: `172.XX.50.0/27`.
- Next hop: `172.XX.255.251`.
- Router sở hữu subnet: `R3`.
```text
ip route 172.XX.50.0 255.255.255.224 172.XX.255.251
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.4. R1 tới SERVER

- Destination: `172.XX.60.0/28`.
- Next hop: `172.XX.255.252`.
- Router sở hữu subnet: `R4`.
```text
ip route 172.XX.60.0 255.255.255.240 172.XX.255.252
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.5. R2 tới ADMIN

- Destination: `172.XX.10.0/27`.
- Next hop: `172.XX.255.249`.
- Router sở hữu subnet: `R1`.
```text
ip route 172.XX.10.0 255.255.255.224 172.XX.255.249
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.6. R2 tới STAFF

- Destination: `172.XX.20.0/27`.
- Next hop: `172.XX.255.249`.
- Router sở hữu subnet: `R1`.
```text
ip route 172.XX.20.0 255.255.255.224 172.XX.255.249
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.7. R2 tới MEETING

- Destination: `172.XX.50.0/27`.
- Next hop: `172.XX.255.251`.
- Router sở hữu subnet: `R3`.
```text
ip route 172.XX.50.0 255.255.255.224 172.XX.255.251
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.8. R2 tới SERVER

- Destination: `172.XX.60.0/28`.
- Next hop: `172.XX.255.252`.
- Router sở hữu subnet: `R4`.
```text
ip route 172.XX.60.0 255.255.255.240 172.XX.255.252
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.9. R3 tới ADMIN

- Destination: `172.XX.10.0/27`.
- Next hop: `172.XX.255.249`.
- Router sở hữu subnet: `R1`.
```text
ip route 172.XX.10.0 255.255.255.224 172.XX.255.249
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.10. R3 tới STAFF

- Destination: `172.XX.20.0/27`.
- Next hop: `172.XX.255.249`.
- Router sở hữu subnet: `R1`.
```text
ip route 172.XX.20.0 255.255.255.224 172.XX.255.249
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.11. R3 tới TECH

- Destination: `172.XX.30.0/28`.
- Next hop: `172.XX.255.250`.
- Router sở hữu subnet: `R2`.
```text
ip route 172.XX.30.0 255.255.255.240 172.XX.255.250
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.12. R3 tới MGMT

- Destination: `172.XX.40.0/28`.
- Next hop: `172.XX.255.250`.
- Router sở hữu subnet: `R2`.
```text
ip route 172.XX.40.0 255.255.255.240 172.XX.255.250
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.13. R3 tới SERVER

- Destination: `172.XX.60.0/28`.
- Next hop: `172.XX.255.252`.
- Router sở hữu subnet: `R4`.
```text
ip route 172.XX.60.0 255.255.255.240 172.XX.255.252
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.14. R4 tới ADMIN

- Destination: `172.XX.10.0/27`.
- Next hop: `172.XX.255.249`.
- Router sở hữu subnet: `R1`.
```text
ip route 172.XX.10.0 255.255.255.224 172.XX.255.249
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.15. R4 tới STAFF

- Destination: `172.XX.20.0/27`.
- Next hop: `172.XX.255.249`.
- Router sở hữu subnet: `R1`.
```text
ip route 172.XX.20.0 255.255.255.224 172.XX.255.249
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.16. R4 tới TECH

- Destination: `172.XX.30.0/28`.
- Next hop: `172.XX.255.250`.
- Router sở hữu subnet: `R2`.
```text
ip route 172.XX.30.0 255.255.255.240 172.XX.255.250
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.17. R4 tới MGMT

- Destination: `172.XX.40.0/28`.
- Next hop: `172.XX.255.250`.
- Router sở hữu subnet: `R2`.
```text
ip route 172.XX.40.0 255.255.255.240 172.XX.255.250
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

### 19.18. R4 tới MEETING

- Destination: `172.XX.50.0/27`.
- Next hop: `172.XX.255.251`.
- Router sở hữu subnet: `R3`.
```text
ip route 172.XX.50.0 255.255.255.224 172.XX.255.251
```
- [ ] Destination network đúng ranh giới.
- [ ] Subnet mask đúng prefix.
- [ ] Next hop nằm trong 172.XX.255.248/29.
- [ ] Next hop thuộc router sở hữu subnet.
- [ ] Router nguồn directly connected với backbone.
- [ ] Không có route connected trùng destination.
- [ ] Lệnh xuất hiện trong running-config.
- [ ] Route S xuất hiện trong show ip route.
- [ ] Ping remote gateway pass.
- [ ] Ping một host remote pass.
- [ ] Return route tồn tại ở router đích.
- [ ] Ảnh hoặc output đã được lưu.
- [ ] Reviewer giải thích được đường đi.
- [ ] Không dùng dynamic routing để thay route này.

## 20. Phiếu xác minh từng DHCP pool


### 20.1. POOL-ADMIN

- Network: `172.XX.10.0/27`.
- Gateway: `172.XX.10.1`.
- Start IP: `172.XX.10.10`.
- Expected last lease: `172.XX.10.19`.
- Mask: `255.255.255.224`.
- Maximum users: 10.
- DNS: `172.XX.60.11`.
- Relay interface: `R1/G0/0`.

#### Cấu hình

- [ ] Pool name đúng và duy nhất.
- [ ] Default gateway đúng subnet.
- [ ] DNS server đúng.
- [ ] Start IP đúng.
- [ ] Subnet mask đúng.
- [ ] Maximum users đúng.
- [ ] Dải lease không chạm broadcast.
- [ ] Dải lease không chứa gateway.
- [ ] Dải lease dành chỗ cho infrastructure.
- [ ] DHCP service đang On.

#### Relay và route

- [ ] Helper trỏ 172.XX.60.10.
- [ ] Helper nằm trên client-facing interface.
- [ ] Router client có route tới SERVER.
- [ ] R4 có route về client network.
- [ ] DHCP-SRV gateway là 172.XX.60.1.

#### Nghiệm thu

- [ ] Client nhận địa chỉ trong expected range.
- [ ] Client nhận đúng mask.
- [ ] Client nhận đúng gateway.
- [ ] Client nhận đúng DNS.
- [ ] ipconfig /all hiển thị DHCP Server.
- [ ] Ping gateway pass.
- [ ] Ping DHCP-SRV pass.
- [ ] Ảnh có Evidence ID.
- [ ] Reviewer xác nhận không dùng static client IP.

### 20.2. POOL-STAFF

- Network: `172.XX.20.0/27`.
- Gateway: `172.XX.20.1`.
- Start IP: `172.XX.20.10`.
- Expected last lease: `172.XX.20.29`.
- Mask: `255.255.255.224`.
- Maximum users: 20.
- DNS: `172.XX.60.11`.
- Relay interface: `R1/G0/1`.

#### Cấu hình

- [ ] Pool name đúng và duy nhất.
- [ ] Default gateway đúng subnet.
- [ ] DNS server đúng.
- [ ] Start IP đúng.
- [ ] Subnet mask đúng.
- [ ] Maximum users đúng.
- [ ] Dải lease không chạm broadcast.
- [ ] Dải lease không chứa gateway.
- [ ] Dải lease dành chỗ cho infrastructure.
- [ ] DHCP service đang On.

#### Relay và route

- [ ] Helper trỏ 172.XX.60.10.
- [ ] Helper nằm trên client-facing interface.
- [ ] Router client có route tới SERVER.
- [ ] R4 có route về client network.
- [ ] DHCP-SRV gateway là 172.XX.60.1.

#### Nghiệm thu

- [ ] Client nhận địa chỉ trong expected range.
- [ ] Client nhận đúng mask.
- [ ] Client nhận đúng gateway.
- [ ] Client nhận đúng DNS.
- [ ] ipconfig /all hiển thị DHCP Server.
- [ ] Ping gateway pass.
- [ ] Ping DHCP-SRV pass.
- [ ] Ảnh có Evidence ID.
- [ ] Reviewer xác nhận không dùng static client IP.

### 20.3. POOL-TECH

- Network: `172.XX.30.0/28`.
- Gateway: `172.XX.30.1`.
- Start IP: `172.XX.30.10`.
- Expected last lease: `172.XX.30.14`.
- Mask: `255.255.255.240`.
- Maximum users: 5.
- DNS: `172.XX.60.11`.
- Relay interface: `R2/G0/0`.

#### Cấu hình

- [ ] Pool name đúng và duy nhất.
- [ ] Default gateway đúng subnet.
- [ ] DNS server đúng.
- [ ] Start IP đúng.
- [ ] Subnet mask đúng.
- [ ] Maximum users đúng.
- [ ] Dải lease không chạm broadcast.
- [ ] Dải lease không chứa gateway.
- [ ] Dải lease dành chỗ cho infrastructure.
- [ ] DHCP service đang On.

#### Relay và route

- [ ] Helper trỏ 172.XX.60.10.
- [ ] Helper nằm trên client-facing interface.
- [ ] Router client có route tới SERVER.
- [ ] R4 có route về client network.
- [ ] DHCP-SRV gateway là 172.XX.60.1.

#### Nghiệm thu

- [ ] Client nhận địa chỉ trong expected range.
- [ ] Client nhận đúng mask.
- [ ] Client nhận đúng gateway.
- [ ] Client nhận đúng DNS.
- [ ] ipconfig /all hiển thị DHCP Server.
- [ ] Ping gateway pass.
- [ ] Ping DHCP-SRV pass.
- [ ] Ảnh có Evidence ID.
- [ ] Reviewer xác nhận không dùng static client IP.

### 20.4. POOL-MGMT

- Network: `172.XX.40.0/28`.
- Gateway: `172.XX.40.1`.
- Start IP: `172.XX.40.10`.
- Expected last lease: `172.XX.40.14`.
- Mask: `255.255.255.240`.
- Maximum users: 5.
- DNS: `172.XX.60.11`.
- Relay interface: `R2/G0/1`.

#### Cấu hình

- [ ] Pool name đúng và duy nhất.
- [ ] Default gateway đúng subnet.
- [ ] DNS server đúng.
- [ ] Start IP đúng.
- [ ] Subnet mask đúng.
- [ ] Maximum users đúng.
- [ ] Dải lease không chạm broadcast.
- [ ] Dải lease không chứa gateway.
- [ ] Dải lease dành chỗ cho infrastructure.
- [ ] DHCP service đang On.

#### Relay và route

- [ ] Helper trỏ 172.XX.60.10.
- [ ] Helper nằm trên client-facing interface.
- [ ] Router client có route tới SERVER.
- [ ] R4 có route về client network.
- [ ] DHCP-SRV gateway là 172.XX.60.1.

#### Nghiệm thu

- [ ] Client nhận địa chỉ trong expected range.
- [ ] Client nhận đúng mask.
- [ ] Client nhận đúng gateway.
- [ ] Client nhận đúng DNS.
- [ ] ipconfig /all hiển thị DHCP Server.
- [ ] Ping gateway pass.
- [ ] Ping DHCP-SRV pass.
- [ ] Ảnh có Evidence ID.
- [ ] Reviewer xác nhận không dùng static client IP.

### 20.5. POOL-MEETING

- Network: `172.XX.50.0/27`.
- Gateway: `172.XX.50.1`.
- Start IP: `172.XX.50.10`.
- Expected last lease: `172.XX.50.29`.
- Mask: `255.255.255.224`.
- Maximum users: 20.
- DNS: `172.XX.60.11`.
- Relay interface: `R3/G0/0`.

#### Cấu hình

- [ ] Pool name đúng và duy nhất.
- [ ] Default gateway đúng subnet.
- [ ] DNS server đúng.
- [ ] Start IP đúng.
- [ ] Subnet mask đúng.
- [ ] Maximum users đúng.
- [ ] Dải lease không chạm broadcast.
- [ ] Dải lease không chứa gateway.
- [ ] Dải lease dành chỗ cho infrastructure.
- [ ] DHCP service đang On.

#### Relay và route

- [ ] Helper trỏ 172.XX.60.10.
- [ ] Helper nằm trên client-facing interface.
- [ ] Router client có route tới SERVER.
- [ ] R4 có route về client network.
- [ ] DHCP-SRV gateway là 172.XX.60.1.

#### Nghiệm thu

- [ ] Client nhận địa chỉ trong expected range.
- [ ] Client nhận đúng mask.
- [ ] Client nhận đúng gateway.
- [ ] Client nhận đúng DNS.
- [ ] ipconfig /all hiển thị DHCP Server.
- [ ] Ping gateway pass.
- [ ] Ping DHCP-SRV pass.
- [ ] Ảnh có Evidence ID.
- [ ] Reviewer xác nhận không dùng static client IP.

## 21. Phiếu thực thi từng dòng test matrix


### 21.1. T01 - Local Layer 3

- Source: `PC-HC-01`.
- Destination: `172.XX.10.1`.
- Thao tác/lệnh: `ping 172.XX.10.1`.
- Expected: Gateway ADMIN phản hồi.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T01.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.2. T02 - Static routing

- Source: `PC-HC-01`.
- Destination: `PC-KT-01`.
- Thao tác/lệnh: `ping <IP-PC-KT-01>`.
- Expected: Ping liên tầng thành công.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T02.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.3. T03 - Routing tới server

- Source: `PC-LD-01`.
- Destination: `172.XX.60.12`.
- Thao tác/lệnh: `ping 172.XX.60.12`.
- Expected: WEB-SRV phản hồi.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T03.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.4. T04 - Wi-Fi và routing

- Source: `LAP-MEETING-01`.
- Destination: `PC-HC-01`.
- Thao tác/lệnh: `ping <IP-PC-HC-01>`.
- Expected: Wireless tới wired liên tầng thành công.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T04.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.5. T05 - Inter-subnet routing

- Source: `LAP-STAFF-01`.
- Destination: `PC-HC-01`.
- Thao tác/lệnh: `ping <IP-PC-HC-01>`.
- Expected: Hai subnet cùng tầng liên lạc qua R1.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T05.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.6. T06 - DHCP

- Source: `Một client mỗi subnet`.
- Destination: `N/A`.
- Thao tác/lệnh: `ipconfig /all`.
- Expected: Đúng IP, mask, gateway, DNS, DHCP server.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T06.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.7. T07 - DNS A record

- Source: `PC-KT-01`.
- Destination: `mmt-XX.com`.
- Thao tác/lệnh: `nslookup mmt-XX.com`.
- Expected: Trả về 172.XX.60.12.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T07.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.8. T08 - DNS CNAME

- Source: `PC-KT-01`.
- Destination: `www.mmt-XX.com`.
- Thao tác/lệnh: `nslookup www.mmt-XX.com`.
- Expected: Alias resolve về 172.XX.60.12.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T08.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.9. T09 - DNS và HTTP

- Source: `Client trên mỗi subnet`.
- Destination: `http://www.mmt-XX.com`.
- Thao tác/lệnh: `Web Browser`.
- Expected: Trang hiển thị đúng thông tin nhóm.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T09.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.10. T10 - Routing table

- Source: `R1 R2 R3 R4`.
- Destination: `N/A`.
- Thao tác/lệnh: `show ip route`.
- Expected: Thấy mọi subnet bằng C, L hoặc S.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T10.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.11. T11 - Backbone

- Source: `R1`.
- Destination: `172.XX.255.252`.
- Thao tác/lệnh: `ping 172.XX.255.252`.
- Expected: R4 backbone phản hồi.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T11.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

### 21.12. T12 - Meeting DHCP

- Source: `LAP-MEETING-01`.
- Destination: `N/A`.
- Thao tác/lệnh: `ipconfig`.
- Expected: Nhận IP 172.XX.50.10-29.

#### Precondition

- [ ] Topology đã Save.
- [ ] Source device đúng tên.
- [ ] Source link đang hoạt động.
- [ ] Source IP đã được xác nhận.
- [ ] Destination IP/domain đã được xác nhận.
- [ ] Route hai chiều đã được kiểm tra khi áp dụng.
- [ ] Service đang On khi áp dụng.

#### Thực thi

- [ ] Mở đúng công cụ trên source.
- [ ] Nhập đúng lệnh hoặc URL.
- [ ] Chờ kết quả hoàn tất.
- [ ] Không crop trước khi thấy đầy đủ kết quả.
- [ ] Ghi actual result cụ thể.
- [ ] Ghi địa chỉ được quan sát.
- [ ] Ghi packet loss hoặc resolved address khi phù hợp.

#### Bằng chứng

- [ ] Lưu ảnh với prefix T12.
- [ ] Ảnh thấy rõ source device.
- [ ] Ảnh thấy rõ command hoặc URL.
- [ ] Ảnh thấy rõ result.
- [ ] Caption nêu source, destination và kết luận.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật Actual column.
- [ ] Cập nhật Status PASS hoặc FAIL.
- [ ] Reviewer mở ảnh và xác nhận.

#### Nếu FAIL

- [ ] Không đổi nhiều cấu hình cùng lúc.
- [ ] Kiểm tra lớp thấp hơn trước.
- [ ] Lưu ảnh chẩn đoán nếu hữu ích.
- [ ] Ghi issue nếu lỗi đáng đưa vào report.
- [ ] Sửa nguyên nhân gốc.
- [ ] Chạy lại chính test này.
- [ ] Chạy regression test liên quan.

## 22. Phiếu quản lý từng bằng chứng


### 22.1. D01 - Topology tổng thể

- Nội dung bắt buộc: hostname, bốn tầng, cable, network label.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng D01.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.2. C01-R1 - R1 interfaces

- Nội dung bắt buộc: IP, mask, helper, trạng thái.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C01-R1.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.3. C01-R2 - R2 interfaces

- Nội dung bắt buộc: IP, mask, helper, trạng thái.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C01-R2.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.4. C01-R3 - R3 interfaces

- Nội dung bắt buộc: IP, mask, helper, trạng thái.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C01-R3.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.5. C01-R4 - R4 interfaces

- Nội dung bắt buộc: IP, mask, trạng thái.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C01-R4.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.6. C02 - Backbone

- Nội dung bắt buộc: R1-R4 nối SW-CORE và port label.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C02.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.7. C03-R1 - R1 static routes

- Nội dung bắt buộc: bốn route.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C03-R1.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.8. C03-R2 - R2 static routes

- Nội dung bắt buộc: bốn route.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C03-R2.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.9. C03-R3 - R3 static routes

- Nội dung bắt buộc: năm route.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C03-R3.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.10. C03-R4 - R4 static routes

- Nội dung bắt buộc: năm route.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C03-R4.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.11. C04-R1 - R1 DHCP relay

- Nội dung bắt buộc: helper trên G0/0 và G0/1.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C04-R1.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.12. C04-R2 - R2 DHCP relay

- Nội dung bắt buộc: helper trên G0/0 và G0/1.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C04-R2.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.13. C04-R3 - R3 DHCP relay

- Nội dung bắt buộc: helper trên G0/0.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C04-R3.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.14. C05-A - POOL-ADMIN

- Nội dung bắt buộc: gateway DNS start mask max.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C05-A.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.15. C05-B - POOL-STAFF

- Nội dung bắt buộc: gateway DNS start mask max.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C05-B.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.16. C05-C - POOL-TECH

- Nội dung bắt buộc: gateway DNS start mask max.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C05-C.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.17. C05-D - POOL-MGMT

- Nội dung bắt buộc: gateway DNS start mask max.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C05-D.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.18. C05-E - POOL-MEETING

- Nội dung bắt buộc: gateway DNS start mask max.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C05-E.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.19. C06 - DNS records

- Nội dung bắt buộc: A record và CNAME.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C06.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.20. C07 - Website content

- Nội dung bắt buộc: company group XX member table.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C07.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.21. C08 - AP-STAFF

- Nội dung bắt buộc: SSID và WPA2-PSK.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C08.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.22. C09 - AP-MEETING

- Nội dung bắt buộc: SSID và WPA2-PSK.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng C09.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.23. SIM-DHCP - DHCP relay flow

- Nội dung bắt buộc: Discover Offer Request ACK qua relay.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng SIM-DHCP.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.24. SIM-DNS-HTTP - DNS rồi HTTP

- Nội dung bắt buộc: query response TCP HTTP.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng SIM-DNS-HTTP.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.25. T01 - Local gateway ping

- Nội dung bắt buộc: command và success.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T01.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.26. T02 - ADMIN to TECH

- Nội dung bắt buộc: source destination success.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T02.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.27. T03 - MGMT to WEB-SRV

- Nội dung bắt buộc: source destination success.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T03.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.28. T04 - MEETING to ADMIN

- Nội dung bắt buộc: wireless source và success.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T04.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.29. T05 - STAFF to ADMIN

- Nội dung bắt buộc: different subnet và success.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T05.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.30. T06 - DHCP ipconfig all

- Nội dung bắt buộc: năm subnet và server fields.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T06.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.31. T07 - A record lookup

- Nội dung bắt buộc: domain và WEB-SRV IP.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T07.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.32. T08 - CNAME lookup

- Nội dung bắt buộc: www alias và WEB-SRV IP.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T08.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.33. T09 - Website by domain

- Nội dung bắt buộc: URL và member content.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T09.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.34. T10 - Routing tables

- Nội dung bắt buộc: R1-R4 và C L S.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T10.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.35. T11 - R1 to R4 backbone

- Nội dung bắt buộc: 172.XX.255.252 success.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T11.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

### 22.36. T12 - Meeting DHCP

- Nội dung bắt buộc: 172.XX.50.0/27 lease.
- Định dạng ưu tiên: PNG.
- Thư mục: report/images theo nhóm phù hợp.

#### Trước khi chụp

- [ ] Mở đúng thiết bị hoặc view.
- [ ] Thu gọn cửa sổ không liên quan.
- [ ] Đặt zoom để text đọc được.
- [ ] Kiểm tra XX không còn placeholder trong cấu hình thật.
- [ ] Kiểm tra kết quả đang ở trạng thái cuối.
- [ ] Không để notification cá nhân trong khung hình.

#### Sau khi chụp

- [ ] Tên file bắt đầu bằng T12.
- [ ] Ảnh không bị mờ.
- [ ] Ảnh không bị crop mất kết quả.
- [ ] Hostname hoặc source đọc được.
- [ ] IP/domain/lệnh đọc được khi áp dụng.
- [ ] Không có thông tin nhạy cảm ngoài đồ án.
- [ ] Screenshot Index đã cập nhật.
- [ ] Caption đã viết.
- [ ] Report reference đã thêm.
- [ ] Reviewer đã duyệt.

#### Caption checklist

- [ ] Caption có mã hình.
- [ ] Caption nêu thiết bị nguồn.
- [ ] Caption nêu thao tác.
- [ ] Caption nêu kết quả.
- [ ] Caption nêu ý nghĩa đối với yêu cầu.

## 23. Audit theo rubric chấm điểm


### 23.1. Needs analysis và address planning - 1.5 điểm

- Evidence tối thiểu: Analysis, subnet table, justification.

#### Nội dung

- [ ] Yêu cầu đề được trích đúng.
- [ ] Thiết kế khớp yêu cầu.
- [ ] Triển khai khớp thiết kế.
- [ ] Kết quả thật khớp triển khai.
- [ ] Report giải thích được quyết định.
- [ ] Không có placeholder trong phần nộp.
- [ ] Không có mâu thuẫn IP hoặc hostname.

#### Bằng chứng

- [ ] Có ảnh cấu hình.
- [ ] Có ảnh kết quả.
- [ ] Ảnh có caption.
- [ ] Có config text khi áp dụng.
- [ ] Có dòng test matrix khi áp dụng.
- [ ] Có phần demo khi áp dụng.
- [ ] Evidence ID nhất quán.

#### Review

- [ ] Owner tự review.
- [ ] Reviewer review chéo.
- [ ] Mọi comment đã resolve.
- [ ] Regression test đã chạy.
- [ ] Artifact cuối mở được.
- [ ] Điểm rủi ro đã được ghi.
- [ ] Trạng thái cuối là PASS.

### 23.2. Logical diagram và equipment table - 1.5 điểm

- Evidence tối thiểu: D01, diagram, inventory.

#### Nội dung

- [ ] Yêu cầu đề được trích đúng.
- [ ] Thiết kế khớp yêu cầu.
- [ ] Triển khai khớp thiết kế.
- [ ] Kết quả thật khớp triển khai.
- [ ] Report giải thích được quyết định.
- [ ] Không có placeholder trong phần nộp.
- [ ] Không có mâu thuẫn IP hoặc hostname.

#### Bằng chứng

- [ ] Có ảnh cấu hình.
- [ ] Có ảnh kết quả.
- [ ] Ảnh có caption.
- [ ] Có config text khi áp dụng.
- [ ] Có dòng test matrix khi áp dụng.
- [ ] Có phần demo khi áp dụng.
- [ ] Evidence ID nhất quán.

#### Review

- [ ] Owner tự review.
- [ ] Reviewer review chéo.
- [ ] Mọi comment đã resolve.
- [ ] Regression test đã chạy.
- [ ] Artifact cuối mở được.
- [ ] Điểm rủi ro đã được ghi.
- [ ] Trạng thái cuối là PASS.

### 23.3. Cabling và basic config - 1.0 điểm

- Evidence tối thiểu: C01, C02, configs.

#### Nội dung

- [ ] Yêu cầu đề được trích đúng.
- [ ] Thiết kế khớp yêu cầu.
- [ ] Triển khai khớp thiết kế.
- [ ] Kết quả thật khớp triển khai.
- [ ] Report giải thích được quyết định.
- [ ] Không có placeholder trong phần nộp.
- [ ] Không có mâu thuẫn IP hoặc hostname.

#### Bằng chứng

- [ ] Có ảnh cấu hình.
- [ ] Có ảnh kết quả.
- [ ] Ảnh có caption.
- [ ] Có config text khi áp dụng.
- [ ] Có dòng test matrix khi áp dụng.
- [ ] Có phần demo khi áp dụng.
- [ ] Evidence ID nhất quán.

#### Review

- [ ] Owner tự review.
- [ ] Reviewer review chéo.
- [ ] Mọi comment đã resolve.
- [ ] Regression test đã chạy.
- [ ] Artifact cuối mở được.
- [ ] Điểm rủi ro đã được ghi.
- [ ] Trạng thái cuối là PASS.

### 23.4. Static routing - 1.5 điểm

- Evidence tối thiểu: C03, T02-T05, T10-T11.

#### Nội dung

- [ ] Yêu cầu đề được trích đúng.
- [ ] Thiết kế khớp yêu cầu.
- [ ] Triển khai khớp thiết kế.
- [ ] Kết quả thật khớp triển khai.
- [ ] Report giải thích được quyết định.
- [ ] Không có placeholder trong phần nộp.
- [ ] Không có mâu thuẫn IP hoặc hostname.

#### Bằng chứng

- [ ] Có ảnh cấu hình.
- [ ] Có ảnh kết quả.
- [ ] Ảnh có caption.
- [ ] Có config text khi áp dụng.
- [ ] Có dòng test matrix khi áp dụng.
- [ ] Có phần demo khi áp dụng.
- [ ] Evidence ID nhất quán.

#### Review

- [ ] Owner tự review.
- [ ] Reviewer review chéo.
- [ ] Mọi comment đã resolve.
- [ ] Regression test đã chạy.
- [ ] Artifact cuối mở được.
- [ ] Điểm rủi ro đã được ghi.
- [ ] Trạng thái cuối là PASS.

### 23.5. Centralized DHCP và relay - 2.0 điểm

- Evidence tối thiểu: C04, C05, T06, T12, SIM-DHCP.

#### Nội dung

- [ ] Yêu cầu đề được trích đúng.
- [ ] Thiết kế khớp yêu cầu.
- [ ] Triển khai khớp thiết kế.
- [ ] Kết quả thật khớp triển khai.
- [ ] Report giải thích được quyết định.
- [ ] Không có placeholder trong phần nộp.
- [ ] Không có mâu thuẫn IP hoặc hostname.

#### Bằng chứng

- [ ] Có ảnh cấu hình.
- [ ] Có ảnh kết quả.
- [ ] Ảnh có caption.
- [ ] Có config text khi áp dụng.
- [ ] Có dòng test matrix khi áp dụng.
- [ ] Có phần demo khi áp dụng.
- [ ] Evidence ID nhất quán.

#### Review

- [ ] Owner tự review.
- [ ] Reviewer review chéo.
- [ ] Mọi comment đã resolve.
- [ ] Regression test đã chạy.
- [ ] Artifact cuối mở được.
- [ ] Điểm rủi ro đã được ghi.
- [ ] Trạng thái cuối là PASS.

### 23.6. DNS và WEB - 1.5 điểm

- Evidence tối thiểu: C06, C07, T07-T09, SIM-DNS-HTTP.

#### Nội dung

- [ ] Yêu cầu đề được trích đúng.
- [ ] Thiết kế khớp yêu cầu.
- [ ] Triển khai khớp thiết kế.
- [ ] Kết quả thật khớp triển khai.
- [ ] Report giải thích được quyết định.
- [ ] Không có placeholder trong phần nộp.
- [ ] Không có mâu thuẫn IP hoặc hostname.

#### Bằng chứng

- [ ] Có ảnh cấu hình.
- [ ] Có ảnh kết quả.
- [ ] Ảnh có caption.
- [ ] Có config text khi áp dụng.
- [ ] Có dòng test matrix khi áp dụng.
- [ ] Có phần demo khi áp dụng.
- [ ] Evidence ID nhất quán.

#### Review

- [ ] Owner tự review.
- [ ] Reviewer review chéo.
- [ ] Mọi comment đã resolve.
- [ ] Regression test đã chạy.
- [ ] Artifact cuối mở được.
- [ ] Điểm rủi ro đã được ghi.
- [ ] Trạng thái cuối là PASS.

### 23.7. Wi-Fi WPA2 và DHCP - 0.5 điểm

- Evidence tối thiểu: C08, C09, T04, T05, T12.

#### Nội dung

- [ ] Yêu cầu đề được trích đúng.
- [ ] Thiết kế khớp yêu cầu.
- [ ] Triển khai khớp thiết kế.
- [ ] Kết quả thật khớp triển khai.
- [ ] Report giải thích được quyết định.
- [ ] Không có placeholder trong phần nộp.
- [ ] Không có mâu thuẫn IP hoặc hostname.

#### Bằng chứng

- [ ] Có ảnh cấu hình.
- [ ] Có ảnh kết quả.
- [ ] Ảnh có caption.
- [ ] Có config text khi áp dụng.
- [ ] Có dòng test matrix khi áp dụng.
- [ ] Có phần demo khi áp dụng.
- [ ] Evidence ID nhất quán.

#### Review

- [ ] Owner tự review.
- [ ] Reviewer review chéo.
- [ ] Mọi comment đã resolve.
- [ ] Regression test đã chạy.
- [ ] Artifact cuối mở được.
- [ ] Điểm rủi ro đã được ghi.
- [ ] Trạng thái cuối là PASS.

### 23.8. Test matrix, report và demo - 0.5 điểm

- Evidence tối thiểu: T01-T12, Report.pdf, demo.

#### Nội dung

- [ ] Yêu cầu đề được trích đúng.
- [ ] Thiết kế khớp yêu cầu.
- [ ] Triển khai khớp thiết kế.
- [ ] Kết quả thật khớp triển khai.
- [ ] Report giải thích được quyết định.
- [ ] Không có placeholder trong phần nộp.
- [ ] Không có mâu thuẫn IP hoặc hostname.

#### Bằng chứng

- [ ] Có ảnh cấu hình.
- [ ] Có ảnh kết quả.
- [ ] Ảnh có caption.
- [ ] Có config text khi áp dụng.
- [ ] Có dòng test matrix khi áp dụng.
- [ ] Có phần demo khi áp dụng.
- [ ] Evidence ID nhất quán.

#### Review

- [ ] Owner tự review.
- [ ] Reviewer review chéo.
- [ ] Mọi comment đã resolve.
- [ ] Regression test đã chạy.
- [ ] Artifact cuối mở được.
- [ ] Điểm rủi ro đã được ghi.
- [ ] Trạng thái cuối là PASS.

---

## 24. Mẫu nhật ký làm việc hằng ngày

### 24.1. Đầu phiên

- [ ] Xác nhận người giữ topology lock.
- [ ] Pull hoặc đồng bộ bản repository mới nhất.
- [ ] Đọc issue đang nhận.
- [ ] Xác nhận address plan chưa đổi.
- [ ] Xác nhận Packet Tracer version.
- [ ] Tạo nhánh phù hợp.
- [ ] Ghi mục tiêu phiên làm việc.
- [ ] Ghi test sẽ dùng để xác nhận.

### 24.2. Trong phiên

- [ ] Save topology theo checkpoint.
- [ ] Không sửa ngoài phạm vi issue nếu không cần.
- [ ] Ghi lại lỗi thực tế.
- [ ] Chụp ảnh cấu hình có giá trị.
- [ ] Export config sau thay đổi CLI.
- [ ] Chạy test nhỏ sau mỗi thay đổi.
- [ ] Cập nhật tài liệu liên quan.
- [ ] Báo người còn lại khi dependency thay đổi.

### 24.3. Cuối phiên

- [ ] Save topology.
- [ ] Copy running-config sang startup-config.
- [ ] Export config text.
- [ ] Chạy smoke test.
- [ ] Đặt tên ảnh.
- [ ] Cập nhật Screenshot Index.
- [ ] Cập nhật issue.
- [ ] Commit thay đổi.
- [ ] Mở pull request nếu mốc đã hoàn tất.
- [ ] Bàn giao topology lock.

### 24.4. Mẫu ghi nhật ký

```text
Ngày/giờ:
Owner:
Reviewer:
Topology checkpoint:
Mục tiêu:
File thay đổi:
Thiết bị thay đổi:
Test đã chạy:
Kết quả:
Evidence ID:
Issue phát hiện:
Việc tiếp theo:
```

---

## 25. Mẫu review pull request

### 25.1. Scope

- [ ] PR có một mục tiêu rõ.
- [ ] File thay đổi đúng phạm vi.
- [ ] Không có file tạm.
- [ ] Không có secret thật.
- [ ] Không có video thô.
- [ ] Không có zip submission.

### 25.2. Network consistency

- [ ] XX nhất quán.
- [ ] Hostname nhất quán.
- [ ] Port nhất quán.
- [ ] IP nhất quán.
- [ ] Mask nhất quán.
- [ ] Gateway nhất quán.
- [ ] DNS nhất quán.
- [ ] Route next hop nhất quán.

### 25.3. Requirement compliance

- [ ] Không VLAN.
- [ ] Không dynamic routing.
- [ ] Không static client IP tầng 1-3.
- [ ] Không WRT300N.
- [ ] Website test bằng domain.
- [ ] Packet Tracer version đủ mới.

### 25.4. Evidence

- [ ] Ảnh đúng mã.
- [ ] Ảnh đọc được.
- [ ] Caption có ý nghĩa.
- [ ] Actual result được ghi.
- [ ] Config text khớp topology.
- [ ] Reviewer chạy lại test quan trọng.

---

## 26. Bộ lệnh tham chiếu nhanh

### 26.1. Router

```text
show ip interface brief
show interfaces description
show running-config
show startup-config
show ip route
show ip route static
show ip route connected
show arp
ping <destination>
traceroute <destination>
copy running-config startup-config
```

### 26.2. Switch

```text
show interfaces status
show mac address-table
show running-config
show startup-config
copy running-config startup-config
```

### 26.3. Client

```text
ipconfig
ipconfig /all
ipconfig /release
ipconfig /renew
ping <destination>
nslookup mmt-XX.com
nslookup www.mmt-XX.com
```

Packet Tracer có thể hỗ trợ tập con lệnh tùy model và phiên bản.

Nếu một lệnh không tồn tại, dùng lệnh tương đương có sẵn và ghi rõ trong report.

---

## 27. Các lỗi thường gặp và cách khoanh vùng

### 27.1. Interface down/down

Kiểm tra cable.

Kiểm tra port peer.

Kiểm tra thiết bị có nguồn.

Kiểm tra đúng model.

### 27.2. Interface administratively down

Vào interface configuration.

Chạy `no shutdown`.

Chờ trạng thái cập nhật.

### 27.3. Backbone ping không được

So bốn IP `.249-.252`.

So mask `/29`.

So port SW-CORE.

Kiểm tra không dùng network `.248` hoặc broadcast `.255`.

### 27.4. Remote ping một chiều

Kiểm tra return route.

Kiểm tra gateway của host đích.

Kiểm tra route trên router đích.

Kiểm tra mask hai đầu.

### 27.5. DHCP nhận APIPA

Kiểm tra helper.

Kiểm tra route tới DHCP-SRV.

Kiểm tra route về client.

Kiểm tra pool.

Kiểm tra service On.

### 27.6. DHCP cấp sai pool

Kiểm tra gateway interface nhận request.

Kiểm tra AP nối đúng port.

Kiểm tra pool gateway và mask.

Kiểm tra client thực sự nằm đúng segment.

### 27.7. Ping IP pass nhưng nslookup fail

Kiểm tra DNS field từ DHCP.

Kiểm tra DNS service.

Kiểm tra record spelling.

Kiểm tra query đang gửi đúng DNS-SRV.

### 27.8. Nslookup pass nhưng browser fail

Kiểm tra HTTP service.

Kiểm tra URL.

Kiểm tra WEB-SRV gateway.

Kiểm tra index.html Save.

### 27.9. Wi-Fi thấy SSID nhưng không kết nối

So security mode.

So passphrase.

Kiểm tra module wireless.

Kiểm tra AP đang On.

### 27.10. Wi-Fi có IP sai

Kiểm tra AP nối nhầm subnet.

Kiểm tra helper trên router port.

Kiểm tra pool gateway.

Không sửa bằng static IP.

---

## 28. Câu hỏi tự bảo vệ đồ án

### 28.1. Vì sao không dùng VLAN?

Vì đề bắt buộc mỗi subnet là segment vật lý riêng và cấm dùng VLAN để phân đoạn.

### 28.2. Vì sao backbone cần subnet?

Bốn cổng router phải có địa chỉ cùng mạng để làm next hop trực tiếp cho static route.

### 28.3. Vì sao dùng `/29` cho backbone?

`/29` có sáu usable address, đủ bốn router và hai địa chỉ dự phòng.

### 28.4. Vì sao helper đặt ở LAN interface?

Đó là nơi router nhận DHCP broadcast từ client và chuyển thành traffic tới server.

### 28.5. Vì sao client không dùng static IP?

Đề yêu cầu DHCP cho toàn bộ client tầng 1-3 và có trừ điểm nếu vi phạm.

### 28.6. Vì sao dùng CNAME cho www?

CNAME cho phép `www.mmt-XX.com` alias tới tên gốc, còn A record giữ địa chỉ WEB-SRV.

### 28.7. Vì sao next hop phải trực tiếp reachable?

Router cần phân giải và gửi frame tới next hop trên một network trực tiếp kết nối.

### 28.8. Vì sao explicit routes tốt hơn một default route ở R1?

Chúng đưa packet trực tiếp tới router sở hữu subnet trên backbone và tránh đường vòng.

### 28.9. Vì sao Access Point-PT phù hợp hơn WRT300N?

AP bridge traffic vào subnet hiện hữu, còn WRT có NAT và DHCP làm phá vỡ thiết kế tập trung.

### 28.10. Vì sao cần test domain thay vì IP?

Test domain mới chứng minh đồng thời DHCP cấp DNS, DNS resolve và HTTP hoạt động.

---

## 29. Checklist cuối cùng trước khi nộp

### 29.1. Nhận dạng

- [ ] Tên nhóm đúng.
- [ ] Hai MSSV đúng.
- [ ] Hai họ tên đúng.
- [ ] XX đúng.
- [ ] Domain đúng.
- [ ] SSID đúng.
- [ ] Packet Tracer version đúng.

### 29.2. Topology

- [ ] Bốn router 2911.
- [ ] Năm switch 2960-24TT.
- [ ] Hai Access Point-PT.
- [ ] Ba Server-PT.
- [ ] Client đủ test.
- [ ] Cable đúng.
- [ ] Port đúng.
- [ ] Label rõ.

### 29.3. Routing

- [ ] Bảy subnet đúng.
- [ ] Bốn backbone IP đúng.
- [ ] 18 static route đúng.
- [ ] Mọi route table giải thích được.
- [ ] Không dynamic routing.
- [ ] Không VLAN.

### 29.4. Services

- [ ] Năm DHCP pool.
- [ ] Năm helper interface.
- [ ] Ba server static IP.
- [ ] A record.
- [ ] CNAME.
- [ ] HTTP custom page.
- [ ] Hai WPA2 SSID.

### 29.5. Testing

- [ ] T01 pass.
- [ ] T02 pass.
- [ ] T03 pass.
- [ ] T04 pass.
- [ ] T05 pass.
- [ ] T06 pass.
- [ ] T07 pass.
- [ ] T08 pass.
- [ ] T09 pass.
- [ ] T10 pass.
- [ ] T11 pass.
- [ ] T12 pass.
- [ ] SIM-DHCP hoàn tất.
- [ ] SIM-DNS-HTTP hoàn tất.

### 29.6. Report

- [ ] Cover đủ.
- [ ] Phân công đủ.
- [ ] Needs analysis đủ.
- [ ] Diagram đủ annotation.
- [ ] Address table đủ.
- [ ] Equipment table đủ.
- [ ] Deployment screenshot đủ.
- [ ] Test matrix đủ.
- [ ] C, L, S được giải thích.
- [ ] Default route question được trả lời.
- [ ] Ba issues thật.
- [ ] Conclusion.
- [ ] Future extensions.

### 29.7. Deliverables

- [ ] topology.pkt mở được.
- [ ] Report.pdf mở được.
- [ ] Có 4 router config.
- [ ] Có 5 switch config.
- [ ] Demo mở được.
- [ ] Cả hai thành viên có audio.
- [ ] Folder đúng tên.
- [ ] Zip đúng tên.
- [ ] Bản zip được mở thử sau khi tạo.

---

## 30. Kết luận triển khai

Repository chỉ là nền để giảm sai sót; kết quả chấm điểm phụ thuộc topology và bằng chứng thật.

Nhóm nên khóa address plan trước khi cấu hình.

Nhóm nên làm routing trước service.

Nhóm nên làm DHCP trước DNS và WEB.

Nhóm nên test theo lớp và giữ return path trong mọi phân tích.

Nhóm nên export config sau mỗi checkpoint.

Nhóm nên chụp bằng chứng ngay khi test pass.

Nhóm nên review chéo trước khi quay demo.

Nhóm nên mở lại toàn bộ artifact từ chính file zip cuối.

Khi tất cả checklist cuối được xác nhận bằng kết quả thật, đồ án sẵn sàng nộp.
