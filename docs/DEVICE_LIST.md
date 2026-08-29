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
| Tầng 4 | Access switch | SW-SRV | Cisco 2960-24TT | Kết nối cụm host tĩnh và ba server |

## Wireless

| Vị trí | Tên | Model | SSID | Bảo mật |
|---|---|---|---|---|
| Tầng 1 | AP-STAFF | Access Point-PT | MMT-90-STAFF | WPA2-PSK/AES |
| Tầng 3 | AP-MEETING | Access Point-PT | MMT-90-MEETING | WPA2-PSK/AES |

## Server

| Vị trí | Tên | Model | Vai trò | IP |
|---|---|---|---|---|
| Tầng 4 | DHCP-SRV | Server-PT | 5 DHCP pool | 172.90.40.2/28 |
| Tầng 4 | DNS-SRV | Server-PT | A + CNAME | 172.90.40.3/28 |
| Tầng 4 | WEB-SRV | Server-PT | HTTP | 172.90.40.4/28 |

## End devices theo kiến trúc hiện tại

| Subnet | Cụm thiết bị thể hiện trên canvas | Mục cần xác nhận |
|---|---|---|
| ADMIN | Cụm PC-PT có dây qua SW-HC | Có đủ 10 PC và tất cả dùng DHCP |
| STAFF | Các wireless client qua AP-STAFF | Đúng SSID; ít nhất một client nhận DHCP |
| TECH | Cụm PC-PT có dây qua SW-KT | Có đủ 5 PC và tất cả dùng DHCP |
| MGMT | Cụm PC-PT có dây qua SW-LD | Không quá 5 PC và tất cả dùng DHCP |
| MEETING | Các wireless client qua AP-MEETING | Đúng SSID; ít nhất một client nhận DHCP |
| SERVER | Tối đa 7 PC/host có dây cùng 3 Server-PT qua SW-SRV | Tổng tối đa 10 host; tất cả static, không trùng IP |

Ảnh overview hiện tại bị thu nhỏ nên không dùng nó để khẳng định display name
hoặc đếm từng wireless client. Hai việc đó phải được xác nhận trực tiếp trong
Packet Tracer trước khi chốt inventory.
