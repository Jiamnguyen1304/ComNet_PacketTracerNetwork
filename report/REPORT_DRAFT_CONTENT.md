# Nội dung report sẵn để đưa lên Prism

Các đoạn dưới đây dùng cấu hình thực tế của topology cuối. Những chỗ
`HUMAN_REQUIRED` chỉ được thay sau khi chạy/chụp kết quả thật.

## 1. Thông tin dự án

- Đề tài: Thiết kế và mô phỏng mạng văn phòng bốn tầng.
- Nền tảng: Cisco Packet Tracer 9.0.1 64-bit, build 9.0.1.858.
- Khối địa chỉ: `172.90.0.0/16`, với `XX = 90` lấy từ MSSV 25127190.
- Domain: `mmt-90.com` và `www.mmt-90.com`.
- Thành viên:
  - Nguyễn Vũ Đức Duy - 25127190.
  - Nguyễn Minh Khôi - 25127389.

## 2. Phân công

| Thành viên | Nhiệm vụ và mức đóng góp |
|---|---|
| Nguyễn Vũ Đức Duy - 25127190 | Phân tích yêu cầu; lập kế hoạch địa chỉ và subnet; dựng topology; cấu hình router, switch, DHCP relay và static routing; export running-config. |
| Nguyễn Minh Khôi - 25127389 | Cấu hình/kiểm tra DHCP, DNS, WEB và Wi-Fi; thực hiện test matrix; quản lý ảnh minh chứng; tổng hợp nội dung report và demo. |

Hai thành viên review chéo: Khôi đối chiếu interface/route của Duy; Duy kiểm tra
pool, DNS, website và Wi-Fi. File nhị phân `.pkt` chỉ do một người chỉnh tại một
thời điểm; cấu hình text được đồng bộ qua Git.

## 3. Phân tích yêu cầu

Văn phòng gồm bốn tầng và sáu khu vực nghiệp vụ. Tầng 1 có mạng có dây cho hành
chính và Wi-Fi nhân viên; tầng 2 có mạng kỹ thuật và lãnh đạo; tầng 3 có Wi-Fi
phòng họp; tầng 4 đặt ba server DHCP, DNS và WEB. Năm client subnet phải nhận
địa chỉ động từ một DHCP Server tập trung. Các server sử dụng IP tĩnh.

Theo ràng buộc đề bài, mỗi subnet nối vào một cổng router vật lý riêng; bốn
router dùng chung một Ethernet backbone. Nhóm không dùng VLAN, serial link,
RIP, OSPF hay EIGRP. Hai mạng không dây sử dụng Access Point-PT và WPA2-PSK.
Website được nghiệm thu bằng domain thay vì địa chỉ IP.

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

Prefix `/28` cung cấp 14 địa chỉ usable. Kích thước này đủ cho ADMIN với 10
client và gateway, đủ cho TECH/MGMT với tối đa 5 client, và đủ cho server room
gồm gateway cùng ba server. Prefix `/27` cung cấp 30 địa chỉ usable, đáp ứng tối
đa 20 thiết bị cho mỗi WLAN STAFF và MEETING, đồng thời còn địa chỉ dự phòng.
Backbone `/29` có 6 địa chỉ usable, đủ cho bốn router và hai địa chỉ dự phòng.

Cách đặt third octet `10/11`, `20/21`, `30`, `40` giúp nhận biết tầng/khu vực
nhanh khi đọc route table. Thiết kế VLSM không có subnet chồng lấn.

### Địa chỉ server

| Server | IP | Gateway | DNS |
|---|---|---|---|
| DHCP-SRV | `172.90.40.2/28` | `172.90.40.1` | `172.90.40.3` |
| DNS-SRV | `172.90.40.3/28` | `172.90.40.1` | `172.90.40.3` |
| WEB-SRV | `172.90.40.4/28` | `172.90.40.1` | `172.90.40.3` |

## 5. Kiến trúc và thiết bị

Mỗi tầng có một Cisco 2911. R1 làm gateway cho ADMIN/STAFF; R2 cho TECH/MGMT;
R3 cho MEETING; R4 cho SERVER. Bốn router nối SW-CORE 2960-24TT bằng backbone
`172.90.255.0/29`, lần lượt dùng `.1`, `.2`, `.3`, `.4`. Bốn access switch nối
các mạng có dây; hai Access Point-PT bridge các WLAN vào R1 và R3.

`[CHÈN D01: toàn cảnh topology]`

Caption đề xuất: **Hình D01.** Topology văn phòng bốn tầng gồm bốn router 2911,
năm switch 2960-24TT, hai Access Point-PT, ba Server-PT và bảy subnet độc lập.

## 6. Static routing

Nhóm sử dụng static route explicit tới mọi remote subnet. Next hop luôn là địa
chỉ backbone của router trực tiếp sở hữu subnet đích. R1/R2 có bốn static route;
R3/R4 có năm static route. Tổng cộng toàn hệ thống có 18 route tĩnh.

Trong `show ip route`:

- `C` là connected network hình thành từ interface đang up/up.
- `L` là host route `/32` tới chính địa chỉ interface router.
- `S` là static route được cấu hình bằng lệnh `ip route`.

`Gateway of last resort is not set` không phải lỗi. Mô hình không có Internet và
mỗi router đã có route cụ thể tới tất cả sáu LAN, nên không cần default route.
Nếu dùng default route trên R1 về R4, traffic tới R2/R3 sẽ đi vòng và vẫn cần
route bổ sung/đường trả về; explicit route rõ ràng và trực tiếp hơn.

`[CHÈN C01A-D/T10A-D]`

Kết quả quan sát: mọi interface đang dùng đều `up/up`; R1/R2 có bốn dòng `S`,
R3/R4 có năm dòng `S`. Không có RIP/OSPF/EIGRP trong running-config.

## 7. DHCP tập trung và relay

DHCP-SRV tại `172.90.40.2` phục vụ năm pool. Vì DHCP Discover là broadcast và
không đi qua router, các interface LAN trên R1-R3 dùng
`ip helper-address 172.90.40.2`. Router nhận broadcast, thêm thông tin gateway
của subnet nguồn và chuyển request dạng unicast tới server. DHCP-SRV dựa vào
thông tin relay để chọn đúng pool và trả về IP, mask, gateway cùng DNS
`172.90.40.3`.

`[CHÈN C04A-C, C05A-D]`

Ảnh CLI xác nhận R1/R2 mỗi router có hai helper, R3 có một helper. Cả bốn router
ping DHCP-SRV thành công 5/5, chứng minh routing hai chiều tới server room.

Pool `serverPool` mặc định của Server-PT không thể xóa. Pool này không được dùng
vì server subnet chỉ có thiết bị IP tĩnh; các request từ xa được ánh xạ vào năm
pool nghiệp vụ theo relay information.

## 8. DNS, WEB và Wi-Fi

DNS-SRV dự kiến có A record `mmt-90.com -> 172.90.40.4` và CNAME
`www.mmt-90.com -> mmt-90.com`. Client nhận DNS `172.90.40.3` từ DHCP. Khi người
dùng mở `www.mmt-90.com`, client truy vấn DNS, nhận IP WEB-SRV, thiết lập TCP và
gửi HTTP request. Website hiển thị `XX = 90`, họ tên và MSSV hai thành viên.

`[CHÈN C06A-B, C07A-B, T07-T09]`

AP-STAFF phát `MMT-90-STAFF`; AP-MEETING phát `MMT-90-MEETING`. Hai AP hoạt động
ở chế độ bridge, không NAT/routing/DHCP và dùng WPA2-PSK/AES. Địa chỉ wireless
client do DHCP-SRV tập trung cấp.

`[CHÈN C08-C09, T04-T05, T06B/T06E]`

## 9. Kiểm thử

Dùng bảng `docs/TEST_MATRIX.md` sau khi điền Actual/Status thật. T10 đã PASS từ
bốn ảnh route table. Các dòng còn lại chỉ chuyển sang PASS sau khi có output và
ảnh tương ứng.

Hai luồng Simulation Mode cần trình bày:

1. DHCP Discover -> Offer -> Request -> ACK qua R1 và DHCP-SRV.
2. DNS query/response -> TCP three-way handshake -> HTTP request/response tới
   WEB-SRV.

## 10. Issues and Resolutions

Ba issue thật đã được ghi trong `docs/ISSUES.md`. Khi đưa vào Prism, bổ sung ảnh
hoặc commit hash và thời điểm thực tế nếu giảng viên yêu cầu.

## 11. Kết luận

Topology đã triển khai đúng kiến trúc bốn tầng bằng static routing và các subnet
vật lý riêng. Running-config xác nhận đủ 18 static route và năm DHCP relay,
không dùng dynamic routing hoặc VLAN. DHCP Server tập trung có đủ năm pool và
đã cấp địa chỉ thành công. Sau khi nhóm hoàn tất ảnh DNS/WEB/Wi-Fi cùng test
matrix T01-T12, mô hình có đủ bằng chứng để nghiệm thu.

Hướng phát triển có thể gồm redundancy cho server/router, ACL, monitoring và
IPv6. Đây chỉ là đề xuất tương lai; không thêm VLAN hoặc dynamic routing vào
topology hiện tại vì trái ràng buộc đề bài.
