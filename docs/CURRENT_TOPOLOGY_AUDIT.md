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

- [ ] ADMIN có 10 PC dùng DHCP; TECH có 5 PC dùng DHCP; MGMT không quá 5 PC
  và dùng DHCP.
- [ ] STAFF và MEETING mỗi mạng có ít nhất một client association và nhận DHCP.
- [ ] Ghi display name thật của một client đại diện mỗi subnet; không dùng tên
  minh họa trong guide nếu canvas không mang tên đó.
- [ ] Đếm trực tiếp host tầng 4: “tối đa 10 host” tính cả ba server, nên chỉ có
  tối đa bảy PC/host bổ sung. Nếu đang có hơn 10 endpoint, giảm số icon tham gia
  hoặc xin giảng viên xác nhận cách hiểu trước khi nghiệm thu.

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
