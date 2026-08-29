# Kế hoạch địa chỉ thực tế

Nguồn chuẩn của bảng này là running-config đã export, ảnh topology/DHCP ngày
2026-08-29 và các quyết định cấu hình an toàn đã chốt trong `NEXT_STEPS.md`.

## Quy ước

- Khối gốc: `172.90.0.0/16`.
- Gateway là địa chỉ usable đầu tiên của mỗi LAN.
- Năm mạng client nhận địa chỉ động từ DHCP-SRV qua DHCP relay.
- Ba server dùng địa chỉ tĩnh trong `172.90.40.0/28`.
- Bốn router dùng các địa chỉ `.1-.4` của backbone `172.90.255.0/29`.

## Bảng subnet

| Subnet | Khu vực | Network/prefix | Mask | Gateway | Usable range | Broadcast |
|---|---|---|---|---|---|---|
| ADMIN | Hành chính tầng 1 | `172.90.10.0/28` | `255.255.255.240` | `172.90.10.1` | `.1-.14` | `.15` |
| STAFF | Wi-Fi tầng 1 | `172.90.11.0/27` | `255.255.255.224` | `172.90.11.1` | `.1-.30` | `.31` |
| TECH | Kỹ thuật tầng 2 | `172.90.20.0/28` | `255.255.255.240` | `172.90.20.1` | `.1-.14` | `.15` |
| MGMT | Lãnh đạo tầng 2 | `172.90.21.0/28` | `255.255.255.240` | `172.90.21.1` | `.1-.14` | `.15` |
| MEETING | Wi-Fi phòng họp tầng 3 | `172.90.30.0/27` | `255.255.255.224` | `172.90.30.1` | `.1-.30` | `.31` |
| SERVER | Server room tầng 4 | `172.90.40.0/28` | `255.255.255.240` | `172.90.40.1` | `.1-.14` | `.15` |
| BACKBONE | Transit bốn router | `172.90.255.0/29` | `255.255.255.248` | Không áp dụng | `.1-.6` | `.7` |

## Địa chỉ hạ tầng

| Thiết bị | Interface | IP/prefix | Vai trò |
|---|---|---|---|
| R1 | G0/0 | `172.90.10.1/28` | Gateway ADMIN |
| R1 | G0/1 | `172.90.11.1/27` | Gateway STAFF |
| R1 | G0/2 | `172.90.255.1/29` | Backbone |
| R2 | G0/0 | `172.90.20.1/28` | Gateway TECH |
| R2 | G0/1 | `172.90.21.1/28` | Gateway MGMT |
| R2 | G0/2 | `172.90.255.2/29` | Backbone |
| R3 | G0/0 | `172.90.30.1/27` | Gateway MEETING |
| R3 | G0/1 | `172.90.255.3/29` | Backbone |
| R4 | G0/0 | `172.90.40.1/28` | Gateway SERVER |
| R4 | G0/1 | `172.90.255.4/29` | Backbone |
| DHCP-SRV | Fa0 | `172.90.40.2/28` | DHCP tập trung |
| DNS-SRV | Fa0 | `172.90.40.3/28` | DNS A + CNAME |
| WEB-SRV | Fa0 | `172.90.40.4/28` | HTTP |

Gateway của ba server là `172.90.40.1`. DNS dùng chung là `172.90.40.3`.

### Địa chỉ static còn lại của tầng 4

Sau R4 và ba server, SERVER subnet còn dải `172.90.40.5-172.90.40.14`. Theo yêu
cầu tối đa 10 host, ba server được tính là ba host, nên chỉ gán thêm tối đa bảy
PC/host trong `.40.5-.40.11`; dùng gateway `172.90.40.1` và DNS `172.90.40.3`.

| Vai trò | Dải đề xuất | Số địa chỉ | Ghi chú |
|---|---|---:|---|
| PC/host tĩnh tầng 4 | `172.90.40.5-172.90.40.11` | 7 | Cộng ba server thành tối đa 10 host |
| Dự phòng | `172.90.40.12-172.90.40.14` | 3 | Không gán nếu đề không yêu cầu |

`/28` có 14 địa chỉ usable. Gateway + tối đa 10 host sử dụng 11 địa chỉ, còn ba
địa chỉ `.40.12-.40.14` dự phòng và không được gán trong thiết kế cuối.

## DHCP relay

| Router | Interface nhận broadcast | Helper address |
|---|---|---|
| R1 | G0/0 ADMIN | `172.90.40.2` |
| R1 | G0/1 STAFF | `172.90.40.2` |
| R2 | G0/0 TECH | `172.90.40.2` |
| R2 | G0/1 MGMT | `172.90.40.2` |
| R3 | G0/0 MEETING | `172.90.40.2` |

Năm helper đã được xác nhận từ running-config và ảnh CLI.

## DHCP scope

| Pool | Start IP cuối | End theo Max users | Max | Mask | Gateway | DNS |
|---|---|---|---:|---|---|---|
| POOL-ADMIN | `172.90.10.4` | `172.90.10.13` | 10 | `/28` | `172.90.10.1` | `172.90.40.3` |
| POOL-STAFF | `172.90.11.4` | `172.90.11.23` | 20 | `/27` | `172.90.11.1` | `172.90.40.3` |
| POOL-TECH | `172.90.20.5` | `172.90.20.9` | 5 | `/28` | `172.90.20.1` | `172.90.40.3` |
| POOL-MGMT | `172.90.21.5` | `172.90.21.9` | 5 | `/28` | `172.90.21.1` | `172.90.40.3` |
| POOL-MEETING | `172.90.30.4` | `172.90.30.23` | 20 | `/27` | `172.90.30.1` | `172.90.40.3` |

`serverPool` là pool mặc định không xóa được của Server-PT. Nó không được dùng
cho client vì toàn bộ thiết bị ở SERVER subnet dùng IP tĩnh; năm request từ xa
được chọn pool theo gateway/relay information.

## Lý do chọn prefix

- `/28` có 14 địa chỉ usable. ADMIN cần 10 client và một gateway; TECH/MGMT
  cần tối đa 5 client. SERVER dùng một gateway và tối đa 10 host tính cả ba
  service server, nên vẫn còn ba địa chỉ usable dự phòng.
- `/27` có 30 địa chỉ usable, đủ tối đa 20 wireless client, gateway và phần
  địa chỉ dự phòng cho STAFF và MEETING.
- `/29` có 6 địa chỉ usable, đủ bốn router backbone và còn hai địa chỉ dự phòng.

Thiết kế VLSM này tiết kiệm địa chỉ hơn việc cấp `/27` cho mọi subnet, đồng
thời vẫn dễ nhận diện tầng qua third octet `10/11`, `20/21`, `30`, `40`.
