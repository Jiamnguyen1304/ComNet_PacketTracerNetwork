# Danh mục thiết bị

## Router

| Vị trí | Loại | Tên | Model | Chức năng | Port dùng |
|---|---|---|---|---|---|
| Tầng 1 | Router | R1 | Cisco 2911 | Routing, DHCP relay | G0/0, G0/1, G0/2 |
| Tầng 2 | Router | R2 | Cisco 2911 | Routing, DHCP relay | G0/0, G0/1, G0/2 |
| Tầng 3 | Router | R3 | Cisco 2911 | Routing, DHCP relay | G0/0, G0/1 |
| Tầng 4 | Router | R4 | Cisco 2911 | Routing | G0/0, G0/1 |

## Switch

| Vị trí | Loại | Tên | Model | Chức năng |
|---|---|---|---|---|
| Technical closet | Backbone switch | SW-CORE | Cisco 2960-24TT | Transit bốn router |
| Tầng 1 | Access switch | SW-HC | Cisco 2960-24TT | Kết nối PC hành chính |
| Tầng 2 | Access switch | SW-KT | Cisco 2960-24TT | Kết nối PC kỹ thuật |
| Tầng 2 | Access switch | SW-LD | Cisco 2960-24TT | Kết nối PC lãnh đạo |
| Tầng 4 | Access switch | SW-SRV | Cisco 2960-24TT | Kết nối ba server |

## Wireless

| Vị trí | Tên | Model | SSID | Bảo mật |
|---|---|---|---|---|
| Tầng 1 | AP-STAFF | Access Point-PT | MMT-90-STAFF | WPA2-PSK/AES |
| Tầng 3 | AP-MEETING | Access Point-PT | MMT-90-MEETING | WPA2-PSK/AES |

## Server

| Vị trí | Tên | Model | Vai trò | IP |
|---|---|---|---|---|
| Tầng 4 | DHCP-SRV | Server-PT | 5 DHCP pool | 172.90.60.10/28 |
| Tầng 4 | DNS-SRV | Server-PT | A + CNAME | 172.90.60.11/28 |
| Tầng 4 | WEB-SRV | Server-PT | HTTP | 172.90.60.12/28 |

## End devices đề xuất

| Subnet | Thiết bị kiểm thử | Tên đề xuất |
|---|---|---|
| Administration | 2 PC-PT | PC-HC-01, PC-HC-02 |
| Staff Wi-Fi | 1 Laptop-PT, 1 Smartphone-PT | LAP-STAFF-01, PHONE-STAFF-01 |
| Technical | 2 PC-PT | PC-KT-01, PC-KT-02 |
| Management | 1 PC-PT | PC-LD-01 |
| Meeting Wi-Fi | 1 Laptop-PT, 1 Smartphone-PT | LAP-MEETING-01, PHONE-MEETING-01 |
