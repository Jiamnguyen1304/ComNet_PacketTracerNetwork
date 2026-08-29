# Kiến trúc hệ thống

## 1. Mục tiêu kiến trúc

Hệ thống mô phỏng văn phòng 4 tầng, tách từng khu vực thành một mạng vật lý
riêng, định tuyến qua router của từng tầng và dùng chung một mạng backbone
Ethernet. Thiết kế ưu tiên đúng ràng buộc đề bài, dễ giải thích trong báo cáo và
dễ chia việc cho hai thành viên.

## 2. Sơ đồ logic cấp cao

```text
PC-HC -- SW-HC -- G0/0 R1 G0/2 --+
AP-STAFF ------- G0/1 R1           |
                                     |
PC-KT -- SW-KT -- G0/0 R2 G0/2 ----+-- SW-CORE -- backbone /29
PC-LD -- SW-LD -- G0/1 R2           |
                                     |
AP-MEETING ------ G0/0 R3 G0/1 ----+
                                     |
PC/host tĩnh + 3 server -- SW-SRV -- G0/0 R4 G0/1 --+
```

`SW-CORE` chỉ phục vụ transit giữa bốn router. Không nối end-station vào
`SW-CORE` và không tạo VLAN.

### Bố cục vật lý hiện tại trên canvas

Topology hiện tại xếp các vùng theo chiều dọc, từ trên xuống là tầng 4, tầng 3,
tầng 2 và tầng 1. `SW-CORE` nằm lệch bên phải khu vực trung tâm; R1-R4 tạo bốn
nhánh Ethernet riêng về core. Bố cục này chỉ thay đổi cách trình bày, không thay
đổi sơ đồ logic, interface hay address plan.

- Tầng 4: một access switch nối cụm host có dây và ba server, đi qua R4.
- Tầng 3: AP-MEETING cùng wireless client, đi trực tiếp qua R3.
- Tầng 2: hai access switch TECH và MGMT, cùng đi qua R2.
- Tầng 1: access switch ADMIN và AP-STAFF là hai LAN vật lý riêng của R1.

## 3. Phân rã theo tầng

### Tầng 1

- `R1/G0/0` phục vụ mạng hành chính qua `SW-HC`.
- `R1/G0/1` phục vụ WLAN staff/guest qua `AP-STAFF`.
- `R1/G0/2` nối `SW-CORE`.
- Cả hai cổng LAN cấu hình `ip helper-address` trỏ về `DHCP-SRV`.

### Tầng 2

- `R2/G0/0` phục vụ mạng kỹ thuật qua `SW-KT`.
- `R2/G0/1` phục vụ mạng lãnh đạo qua `SW-LD`.
- `R2/G0/2` nối `SW-CORE`.
- Cả hai cổng LAN cấu hình `ip helper-address` trỏ về `DHCP-SRV`.

### Tầng 3

- `R3/G0/0` phục vụ WLAN phòng họp qua `AP-MEETING`.
- `R3/G0/1` nối `SW-CORE`.
- Cổng LAN cấu hình `ip helper-address` trỏ về `DHCP-SRV`.
- `R3/G0/2` để trống và shutdown.

### Tầng 4

- `R4/G0/0` phục vụ toàn bộ server room qua `SW-SRV`, gồm ba server dịch vụ và
  các PC/host có dây đang được vẽ trong vùng tầng 4.
- `R4/G0/1` nối `SW-CORE`.
- `R4/G0/2` để trống và shutdown.
- Mọi thiết bị trong SERVER subnet dùng static IP; DHCP không cấp địa chỉ cho
  subnet này.

## 4. Mặt phẳng địa chỉ

| Segment | Router interface | Network | Gateway/router IP |
|---|---|---|---|
| Administration | `R1/G0/0` | `172.90.10.0/28` | `172.90.10.1` |
| Staff Wi-Fi | `R1/G0/1` | `172.90.11.0/27` | `172.90.11.1` |
| Technical | `R2/G0/0` | `172.90.20.0/28` | `172.90.20.1` |
| Management | `R2/G0/1` | `172.90.21.0/28` | `172.90.21.1` |
| Meeting Wi-Fi | `R3/G0/0` | `172.90.30.0/27` | `172.90.30.1` |
| Server | `R4/G0/0` | `172.90.40.0/28` | `172.90.40.1` |
| Backbone | `R1-4 backbone ports` | `172.90.255.0/29` | `.1` đến `.4` |

Server được gán:

- `DHCP-SRV`: `172.90.40.2/28`.
- `DNS-SRV`: `172.90.40.3/28`.
- `WEB-SRV`: `172.90.40.4/28`.
- Default gateway của cả ba: `172.90.40.1`.
- DNS của server: `172.90.40.3`.

Theo nghĩa mạng chuẩn, “tối đa 10 host” bao gồm cả ba server. Vì vậy tầng 4 chỉ
nên có tối đa bảy PC/host bổ sung, dùng `.40.5-.40.11`; `.40.12-.40.14` để dự
phòng. Thiết kế cuối không vượt quá 10 endpoint và không để hai thiết bị trùng IP.

## 5. Static routing

Mỗi router có route explicit tới các mạng không trực tiếp kết nối. Next hop luôn
là địa chỉ backbone của router đích sở hữu mạng đó:

- R1 đi hai mạng tầng 2 qua `172.90.255.2`, tầng 3 qua `.3`, tầng 4 qua `.4`.
- R2 đi hai mạng tầng 1 qua `172.90.255.1`, tầng 3 qua `.3`, tầng 4 qua `.4`.
- R3 đi hai mạng tầng 1 qua `172.90.255.1`, hai mạng tầng 2 qua `.2` và tầng 4 qua `.4`.
- R4 đi hai mạng tầng 1 qua `172.90.255.1`, hai mạng tầng 2 qua `.2` và tầng 3 qua `.3`.

Không khai báo route tới mạng `connected`. Không bật dynamic routing.

## 6. DHCP

`DHCP-SRV` tại `172.90.40.2` giữ năm pool. Mỗi pool trả về gateway của chính
subnet và DNS `172.90.40.3`. Năm LAN interface trên R1-R3 đều relay tới
`172.90.40.2`.

| Pool | Start IP | Mask | Gateway | Max users |
|---|---|---|---|---:|
| `POOL-ADMIN` | `172.90.10.4` | `255.255.255.240` | `172.90.10.1` | 10 |
| `POOL-STAFF` | `172.90.11.4` | `255.255.255.224` | `172.90.11.1` | 20 |
| `POOL-TECH` | `172.90.20.5` | `255.255.255.240` | `172.90.20.1` | 5 |
| `POOL-MGMT` | `172.90.21.5` | `255.255.255.240` | `172.90.21.1` | 5 |
| `POOL-MEETING` | `172.90.30.4` | `255.255.255.224` | `172.90.30.1` | 20 |

## 7. DNS và Web

- DNS A record: `mmt-90.com` -> `172.90.40.4`.
- DNS CNAME: `www.mmt-90.com` -> `mmt-90.com`.
- HTTP bật trên `WEB-SRV`.
- `index.html` phải hiển thị công ty, tên nhóm, `XX`, MSSV và họ tên.
- Client nhận DNS bằng DHCP rồi truy cập `http://www.mmt-90.com`.

## 8. Wi-Fi

- `AP-STAFF` phát `MMT-90-STAFF`.
- `AP-MEETING` phát `MMT-90-MEETING`.
- Cả hai dùng WPA2-PSK.
- Mật khẩu thật không commit nếu nhóm coi đó là secret; báo cáo có thể che một
  phần nhưng demo phải chứng minh client kết nối được.
- Laptop-PT cần gắn module `WPC300N` trước khi liên kết.

## 9. Ranh giới artifact

| Artifact | Nguồn chuẩn | Cách hợp nhất |
|---|---|---|
| Topology | `topology.pkt` | Một người sửa tại một thời điểm |
| Router config | `packet-tracer/configs/` | Text review được qua Git |
| Switch config | `packet-tracer/configs/` | Text review được qua Git |
| Website | `packet-tracer/web/index.html` | Hai thành viên kiểm tra chéo |
| Báo cáo | `report/REPORT_TEMPLATE.md` | Hai người chia phần rồi ghép chung |
| Ảnh | `report/images/` | Theo mã trong screenshot index |
| Demo | `demo/` | Ghi hình sau khi test matrix pass |

## 10. Quyết định kiến trúc

- Dùng hybrid prefix thay vì một prefix duy nhất để cân bằng dễ nhớ và hiệu quả.
- Dùng third octet theo tầng: `10/11` cho tầng 1, `20/21` cho tầng 2, `30`
  cho tầng 3 và `40` cho tầng 4.
- Dùng first usable làm gateway trên toàn hệ thống.
- Dùng server riêng cho DHCP thay vì R4 để vai trò dịch vụ tách bạch và dễ chụp
  minh chứng trên giao diện Packet Tracer.
- Dùng route explicit đến router sở hữu subnet để đường đi trực tiếp trên
  backbone, dễ giải thích và tránh phụ thuộc một router trung gian.
- Không cấu hình IP quản trị cho switch nếu đề không yêu cầu, tránh tạo thêm
  phạm vi địa chỉ và route không cần thiết.

## 11. Tiêu chí hoàn tất kiến trúc

- Ảnh overview thể hiện đúng thứ tự tầng 4 -> 3 -> 2 -> 1 và bốn nhánh về
  `SW-CORE`; ảnh chi tiết từng tầng phải đọc được hostname, port, IP/mask và tên
  subnet.
- Bảy subnet không chồng lấn.
- Mỗi cổng router đúng vai trò trong bảng port allocation.
- Tất cả interface đang dùng ở trạng thái up/up.
- Mọi client động nhận đúng pool.
- Mọi route C, L, S trong `show ip route` giải thích được.
- Tên miền A và CNAME trả về đúng `WEB-SRV`.
- Hai SSID dùng đúng format và WPA2-PSK.
- Toàn bộ 12 dòng test matrix có kết quả và minh chứng.
