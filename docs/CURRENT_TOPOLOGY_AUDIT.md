# Audit topology hiện tại trước khi nghiệm thu

Nguồn đối chiếu: ảnh overview hiện tại, `topology.pkt`, running-config R1-R4 và
address plan đã xác nhận ngày 29/08/2026. File này chỉ phục vụ phần Packet
Tracer; report do Duy thực hiện trên Prism.

## Kiến trúc nhìn thấy trên canvas

- Bố cục từ trên xuống: tầng 4, tầng 3, tầng 2, tầng 1.
- SW-CORE nằm bên phải trung tâm và tạo topology hình sao với R1-R4.
- Tầng 4: R4, SW-SRV, cụm host có dây và DHCP/DNS/WEB server.
- Tầng 3: R3, AP-MEETING và wireless client.
- Tầng 2: R2, hai switch riêng cho TECH và MGMT cùng các PC có dây.
- Tầng 1: R1, SW-HC cho ADMIN và AP-STAFF cho WLAN.

Kiến trúc logic vẫn là bảy subnet vật lý riêng; việc xoay và xếp lại canvas
không làm thay đổi địa chỉ hoặc static route.

## Kết quả đọc từ ba ảnh lúc 18:11

| Hạng mục | Quan sát thực tế | Kết luận |
|---|---|---|
| Link có dây | Các tam giác trạng thái đều xanh | PASS vật lý tại thời điểm chụp |
| Wireless tầng 3 | Smartphone10(3) và Laptop0 association AP-MEETING | PASS association, chưa chứng minh DHCP |
| Wireless tầng 1 | Ba smartphone association AP-STAFF | PASS association, chưa chứng minh DHCP |
| ADMIN | 10 PC qua SW-HC | Đủ số lượng; cần đổi display name |
| MGMT | 5 PC PC21-PC25 qua SW-LD | Đủ số lượng; cần đổi display name |
| TECH | 5 PC tên có hậu tố `(1)` qua SW-KT | Đủ số lượng; tên dễ nhầm |
| SERVER | 10 PC PC11-PC20 và 3 Server-PT | 13 host, vượt giới hạn 10 host |
| Nhãn tầng 1 | Ghi “Phòng quản trị” | Sai/nhầm với MGMT; đổi thành “Phòng hành chính” |
| Nhãn mạng | Chưa thấy network/prefix/gateway trên canvas | Cần bổ sung trước ảnh cuối |

Ba ảnh gốc đã được lưu trong `report/images/audit/` với hậu tố
`before-fix`; không sử dụng chúng làm ảnh nghiệm thu cuối.

## Quyết định sửa an toàn đã chốt

1. Xóa `PC18`, `PC19`, `PC20` khỏi tầng 4; giữ `PC11-PC17` và ba server, tổng
   cộng đúng 10 host.
2. Gán PC tầng 4 lần lượt `172.90.40.5-.40.11`, mask `/28`, gateway `.40.1`,
   DNS `.40.3`; tất cả dùng Static.
3. Đổi nhãn khu vực có dây tầng 1 từ “Phòng quản trị” thành “Phòng hành chính”.
4. Đổi display name để ảnh test không nhầm:
   - ADMIN: `ADMIN-PC01` đến `ADMIN-PC10`.
   - MGMT: `MGMT-PC01` đến `MGMT-PC05`.
   - TECH: `TECH-PC01` đến `TECH-PC05`.
   - SERVER: `SRV-PC01` đến `SRV-PC07`.
   - STAFF: `STAFF-PHONE01` đến `STAFF-PHONE03`.
   - MEETING: `MEETING-PHONE01`, `MEETING-LAPTOP01`.
5. Thêm network label ở mỗi khu vực và một label backbone theo bảng bên dưới.

### Bảng đổi tên trực tiếp

| Tên hiện tại | Tên cuối |
|---|---|
| `PC0`, `PC2`, `PC3`, `PC4`, `PC5` | `ADMIN-PC01` đến `ADMIN-PC05` theo thứ tự |
| `PC6`, `PC7`, `PC8`, `PC9`, `PC10` | `ADMIN-PC06` đến `ADMIN-PC10` theo thứ tự |
| `PC21`, `PC22`, `PC23`, `PC24`, `PC25` | `MGMT-PC01` đến `MGMT-PC05` |
| `PC21(1)`, `PC22(1)`, `PC23(1)`, `PC24(1)`, `PC25(1)` | `TECH-PC01` đến `TECH-PC05` |
| `PC11`, `PC12`, `PC13`, `PC14`, `PC15`, `PC16`, `PC17` | `SRV-PC01` đến `SRV-PC07` |
| `PC18`, `PC19`, `PC20` | Xóa khỏi topology |
| `Smartphone10(2)`, `Smartphone10`, `Smartphone10(1)` | `STAFF-PHONE01` đến `STAFF-PHONE03` |
| `Smartphone10(3)` | `MEETING-PHONE01` |
| `Laptop0` | `MEETING-LAPTOP01` |

| Vị trí label | Nội dung nên ghi |
|---|---|
| Tầng 1 ADMIN | `ADMIN 172.90.10.0/28 - GW 172.90.10.1 - DHCP` |
| Tầng 1 STAFF | `STAFF 172.90.11.0/27 - GW 172.90.11.1 - MMT-90-STAFF` |
| Tầng 2 TECH | `TECH 172.90.20.0/28 - GW 172.90.20.1 - DHCP` |
| Tầng 2 MGMT | `MGMT 172.90.21.0/28 - GW 172.90.21.1 - DHCP` |
| Tầng 3 MEETING | `MEETING 172.90.30.0/27 - GW 172.90.30.1 - MMT-90-MEETING` |
| Tầng 4 SERVER | `SERVER 172.90.40.0/28 - GW 172.90.40.1 - STATIC` |
| Gần SW-CORE | `BACKBONE 172.90.255.0/29 - R1 .1, R2 .2, R3 .3, R4 .4` |

## Checklist bắt buộc trước khi chạy T01-T12

### 1. File và trạng thái link

- [ ] Chỉ mở `topology.pkt` ở thư mục gốc, đóng các bản copy khác.
- [ ] Chuyển Realtime và chờ STP hội tụ; tất cả link đang dùng phải xanh.
- [ ] Bốn link R1-R4 về SW-CORE đều hoạt động; core không nối end-device.
- [ ] Các wireless client có đường chấm association tới đúng AP.

### 2. Đúng nhánh vật lý

- [ ] R1/G0/0 -> ADMIN/SW-HC; R1/G0/1 -> STAFF/AP-STAFF.
- [ ] R2/G0/0 -> TECH/SW-KT; R2/G0/1 -> MGMT/SW-LD.
- [ ] R3/G0/0 -> MEETING/AP-MEETING.
- [ ] R4/G0/0 -> SERVER/SW-SRV.
- [ ] Các cổng backbone là R1/R2 G0/2 và R3/R4 G0/1.
- [ ] Không có VLAN, serial link, WRT300N hoặc dynamic routing.

### 3. Số lượng và tên endpoint

- [ ] ADMIN có 10 PC dùng DHCP; TECH có 5 PC dùng DHCP; MGMT có 5 PC
  và dùng DHCP.
- [ ] STAFF và MEETING mỗi mạng có ít nhất một client association và nhận DHCP.
- [ ] Ghi display name thật của một client đại diện mỗi subnet; không dùng tên
  minh họa trong guide nếu canvas không mang tên đó.
- [ ] Tầng 4 còn đúng `SRV-PC01..07` và ba server, tổng cộng 10 host; không còn
  `PC18`, `PC19`, `PC20`.

### 4. IP tầng 4

- [ ] R4 `172.90.40.1/28`; DHCP `.40.2`; DNS `.40.3`; WEB `.40.4`.
- [ ] Mọi PC/host khác ở tầng 4 dùng Static, IP duy nhất trong `.40.5-.40.11`,
  mask `255.255.255.240`, gateway `.40.1`, DNS `.40.3`.
- [ ] Không client tầng 4 nào nhận từ `serverPool` mặc định.

### 5. Router smoke test

Trên R1 chạy:

```text
show ip interface brief
show ip route
ping 172.90.255.4
ping 172.90.40.2
```

- [ ] G0/0, G0/1, G0/2 đều `up/up`.
- [ ] Có bốn static route trên R1.
- [ ] Hai lệnh ping thành công; có thể chạy lại sau lần đầu chờ ARP.

### 6. Khả năng đọc của sơ đồ

- [ ] Overview cho thấy rõ bốn tầng và bốn nhánh core.
- [ ] Không yêu cầu mọi hostname đọc được trong cùng overview vì canvas quá dài.
- [ ] Chuẩn bị bốn ảnh zoom D01-F1 đến D01-F4, đọc được thiết bị và khu vực.
- [ ] Kiểm tra label không đè lên cable, icon hoặc ranh giới tầng.

Nếu một mục trong phần 1-4 sai, sửa và save topology trước khi bắt đầu chụp test
T01-T12. Không sửa static route chỉ vì một client chưa nhận DHCP; kiểm tra đúng
LAN, helper, pool và gateway trước.
