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
Servers -- SW-SRV - G0/0 R4 G0/1 --+
```

`SW-CORE` chỉ phục vụ transit giữa bốn router. Không nối end-station vào
`SW-CORE` và không tạo VLAN.

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

- `R4/G0/0` phục vụ server room qua `SW-SRV`.
- `R4/G0/1` nối `SW-CORE`.
- `R4/G0/2` để trống và shutdown.
- `DHCP-SRV`, `DNS-SRV`, `WEB-SRV` dùng static IP.

## 4. Mặt phẳng địa chỉ

| Segment | Router interface | Network | Gateway/router IP |
|---|---|---|---|
| Administration | `R1/G0/0` | `172.XX.10.0/27` | `172.XX.10.1` |
| Staff Wi-Fi | `R1/G0/1` | `172.XX.20.0/27` | `172.XX.20.1` |
| Technical | `R2/G0/0` | `172.XX.30.0/28` | `172.XX.30.1` |
| Management | `R2/G0/1` | `172.XX.40.0/28` | `172.XX.40.1` |
| Meeting Wi-Fi | `R3/G0/0` | `172.XX.50.0/27` | `172.XX.50.1` |
| Server | `R4/G0/0` | `172.XX.60.0/28` | `172.XX.60.1` |
| Backbone | `R1-4 backbone ports` | `172.XX.255.248/29` | `.249` đến `.252` |

Server được gán:

- `DHCP-SRV`: `172.XX.60.10/28`.
- `DNS-SRV`: `172.XX.60.11/28`.
- `WEB-SRV`: `172.XX.60.12/28`.
- Default gateway của cả ba: `172.XX.60.1`.
- DNS của server: `172.XX.60.11` khi giao diện Packet Tracer cho phép.

## 5. Static routing

Mỗi router có route explicit tới các mạng không trực tiếp kết nối. Next hop luôn
là địa chỉ backbone của router đích sở hữu mạng đó:

- R1 đi mạng tầng 2 qua `172.XX.255.250`.
- R1 đi mạng tầng 3 qua `172.XX.255.251`.
- R1 đi mạng tầng 4 qua `172.XX.255.252`.
- R2 đi mạng tầng 1 qua `172.XX.255.249`.
- R2 đi mạng tầng 3 qua `172.XX.255.251`.
- R2 đi mạng tầng 4 qua `172.XX.255.252`.
- R3 đi mạng tầng 1 qua `172.XX.255.249`.
- R3 đi mạng tầng 2 qua `172.XX.255.250`.
- R3 đi mạng tầng 4 qua `172.XX.255.252`.
- R4 đi mạng tầng 1 qua `172.XX.255.249`.
- R4 đi mạng tầng 2 qua `172.XX.255.250`.
- R4 đi mạng tầng 3 qua `172.XX.255.251`.

Không khai báo route tới mạng `connected`. Không bật dynamic routing.

## 6. DHCP

`DHCP-SRV` giữ năm pool. Mỗi pool trả về gateway của chính subnet và DNS
`172.XX.60.11`. Pool bắt đầu từ host `.10` để dành `.1-.9` cho gateway, AP và
hạ tầng tương lai.

| Pool | Start IP | Mask | Gateway | Max users |
|---|---|---|---|---:|
| `POOL-ADMIN` | `172.XX.10.10` | `255.255.255.224` | `172.XX.10.1` | 10 |
| `POOL-STAFF` | `172.XX.20.10` | `255.255.255.224` | `172.XX.20.1` | 20 |
| `POOL-TECH` | `172.XX.30.10` | `255.255.255.240` | `172.XX.30.1` | 5 |
| `POOL-MGMT` | `172.XX.40.10` | `255.255.255.240` | `172.XX.40.1` | 5 |
| `POOL-MEETING` | `172.XX.50.10` | `255.255.255.224` | `172.XX.50.1` | 20 |

## 7. DNS và Web

- DNS A record: `mmt-XX.com` -> `172.XX.60.12`.
- DNS CNAME: `www.mmt-XX.com` -> `mmt-XX.com`.
- HTTP bật trên `WEB-SRV`.
- `index.html` phải hiển thị công ty, tên nhóm, `XX`, MSSV và họ tên.
- Client nhận DNS bằng DHCP rồi truy cập `http://www.mmt-XX.com`.

## 8. Wi-Fi

- `AP-STAFF` phát `MMT-XX-STAFF`.
- `AP-MEETING` phát `MMT-XX-MEETING`.
- Cả hai dùng WPA2-PSK.
- Mật khẩu thật không commit nếu nhóm coi đó là secret; báo cáo có thể che một
  phần nhưng demo phải chứng minh client kết nối được.
- Laptop-PT cần gắn module `WPC300N` trước khi liên kết.

## 9. Ranh giới artifact

| Artifact | Nguồn chuẩn | Cách hợp nhất |
|---|---|---|
| Topology | `packet-tracer/topology.pkt` | Một người sửa tại một thời điểm |
| Router config | `packet-tracer/configs/` | Text review được qua Git |
| Switch config | `packet-tracer/configs/` | Text review được qua Git |
| Website | `packet-tracer/web/index.html` | Hai thành viên kiểm tra chéo |
| Báo cáo | `report/REPORT_TEMPLATE.md` | Hai người chia phần rồi ghép chung |
| Ảnh | `report/images/` | Theo mã trong screenshot index |
| Demo | `demo/` | Ghi hình sau khi test matrix pass |

## 10. Quyết định kiến trúc

- Dùng hybrid prefix thay vì một prefix duy nhất để cân bằng dễ nhớ và hiệu quả.
- Dùng third octet theo bội số 10 để đọc ảnh cấu hình nhanh.
- Dùng first usable làm gateway trên toàn hệ thống.
- Dùng server riêng cho DHCP thay vì R4 để vai trò dịch vụ tách bạch và dễ chụp
  minh chứng trên giao diện Packet Tracer.
- Dùng route explicit đến router sở hữu subnet để đường đi trực tiếp trên
  backbone, dễ giải thích và tránh phụ thuộc một router trung gian.
- Không cấu hình IP quản trị cho switch nếu đề không yêu cầu, tránh tạo thêm
  phạm vi địa chỉ và route không cần thiết.

## 11. Tiêu chí hoàn tất kiến trúc

- Sơ đồ tự vẽ có đủ hostname, port, IP, mask và tên subnet.
- Bảy subnet không chồng lấn.
- Mỗi cổng router đúng vai trò trong bảng port allocation.
- Tất cả interface đang dùng ở trạng thái up/up.
- Mọi client động nhận đúng pool.
- Mọi route C, L, S trong `show ip route` giải thích được.
- Tên miền A và CNAME trả về đúng `WEB-SRV`.
- Hai SSID dùng đúng format và WPA2-PSK.
- Toàn bộ 12 dòng test matrix có kết quả và minh chứng.
