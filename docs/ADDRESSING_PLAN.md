# Kế hoạch địa chỉ

## Quy ước

- Khối gốc: `172.90.0.0/16`.
- Gateway: first usable address của mỗi subnet.
- Host động bắt đầu từ `.10`.
- Địa chỉ `.2-.9` dành cho AP và hạ tầng tương lai.
- Server dùng địa chỉ `.10-.12` trong subnet server.

## Bảng subnet

| Subnet | Khu vực | Network/prefix | Mask | Gateway | Usable range | Broadcast |
|---|---|---|---|---|---|---|
| ADMIN | Hành chính | `172.90.10.0/27` | `255.255.255.224` | `172.90.10.1` | `.1-.30` | `.31` |
| STAFF | Wi-Fi tầng 1 | `172.90.20.0/27` | `255.255.255.224` | `172.90.20.1` | `.1-.30` | `.31` |
| TECH | Kỹ thuật | `172.90.30.0/28` | `255.255.255.240` | `172.90.30.1` | `.1-.14` | `.15` |
| MGMT | Lãnh đạo | `172.90.40.0/28` | `255.255.255.240` | `172.90.40.1` | `.1-.14` | `.15` |
| MEETING | Wi-Fi tầng 3 | `172.90.50.0/27` | `255.255.255.224` | `172.90.50.1` | `.1-.30` | `.31` |
| SERVER | Server room | `172.90.60.0/28` | `255.255.255.240` | `172.90.60.1` | `.1-.14` | `.15` |
| BACKBONE | Transit router | `172.90.255.248/29` | `255.255.255.248` | Không áp dụng | `.249-.254` | `.255` |

## Địa chỉ hạ tầng

| Thiết bị | Interface | IP/prefix | Gateway |
|---|---|---|---|
| R1 | G0/0 | `172.90.10.1/27` | Connected |
| R1 | G0/1 | `172.90.20.1/27` | Connected |
| R1 | G0/2 | `172.90.255.249/29` | Connected |
| R2 | G0/0 | `172.90.30.1/28` | Connected |
| R2 | G0/1 | `172.90.40.1/28` | Connected |
| R2 | G0/2 | `172.90.255.250/29` | Connected |
| R3 | G0/0 | `172.90.50.1/27` | Connected |
| R3 | G0/1 | `172.90.255.251/29` | Connected |
| R4 | G0/0 | `172.90.60.1/28` | Connected |
| R4 | G0/1 | `172.90.255.252/29` | Connected |
| AP-STAFF | Management | `172.90.20.2/27` nếu hỗ trợ | `172.90.20.1` |
| AP-MEETING | Management | `172.90.50.2/27` nếu hỗ trợ | `172.90.50.1` |
| DHCP-SRV | Fa0 | `172.90.60.10/28` | `172.90.60.1` |
| DNS-SRV | Fa0 | `172.90.60.11/28` | `172.90.60.1` |
| WEB-SRV | Fa0 | `172.90.60.12/28` | `172.90.60.1` |

## DHCP scope

| Pool | Start | End theo Max users | Max | Gateway | DNS |
|---|---|---|---:|---|---|
| POOL-ADMIN | `.10` | `.19` | 10 | `172.90.10.1` | `172.90.60.11` |
| POOL-STAFF | `.10` | `.29` | 20 | `172.90.20.1` | `172.90.60.11` |
| POOL-TECH | `.10` | `.14` | 5 | `172.90.30.1` | `172.90.60.11` |
| POOL-MGMT | `.10` | `.14` | 5 | `172.90.40.1` | `172.90.60.11` |
| POOL-MEETING | `.10` | `.29` | 20 | `172.90.50.1` | `172.90.60.11` |

## Lý do chọn prefix

`/27` có 30 usable host, phù hợp hai WLAN tối đa 20 client và còn không gian cho
gateway, AP, thiết bị kiểm thử và tăng trưởng. Administration cũng dùng `/27`
để có headroom rõ ràng cho phòng 10 người và thiết bị bổ sung.

`/28` có 14 usable host, đủ cho hai phòng 5 người và server room tối đa 10 host
sau khi dành một địa chỉ gateway. `/29` có 6 usable host, đủ bốn cổng router ở
backbone và hai địa chỉ dự phòng.

Third octet theo bội số 10 giúp đọc topology và ảnh nhanh. Nhược điểm là có các
khoảng địa chỉ không dùng giữa subnet; với khối `/16` rất lớn, nhóm chấp nhận
đổi một phần hiệu quả địa chỉ lấy khả năng ghi nhớ và giảm lỗi thao tác.

