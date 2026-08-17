# Phân tích đề Project 3

## Bài toán

Nhóm đóng vai đội kỹ sư mạng xây hệ thống cho văn phòng mới gồm bốn tầng. Phạm
vi bao gồm phân tích nhu cầu, thiết kế, triển khai mô phỏng, nghiệm thu, báo cáo
và demo có thuyết minh.

## Nhu cầu theo khu vực

| Tầng | Khu vực | Số người/host | Kết nối | Cấp IP |
|---:|---|---:|---|---|
| 1 | Hành chính | 10 | Wired | DHCP |
| 1 | Staff và khách vãng lai | Tối đa 20 | Wireless | DHCP |
| 2 | Kỹ thuật | 5 | Wired | DHCP |
| 2 | Lãnh đạo | Tối đa 5 | Wired | DHCP |
| 3 | Phòng họp | Tối đa 20 | Wireless | DHCP |
| 4 | Server room | Tối đa 10 host | Wired | Static |

## Thiết bị bắt buộc

- 4 router Cisco 2911: `R1`, `R2`, `R3`, `R4`.
- 1 switch Cisco 2960-24TT: `SW-CORE`.
- 4 switch Cisco 2960-24TT: `SW-HC`, `SW-KT`, `SW-LD`, `SW-SRV`.
- 2 Access Point-PT: `AP-STAFF`, `AP-MEETING`.
- 3 Server-PT: `DHCP-SRV`, `DNS-SRV`, `WEB-SRV`.
- 1-2 PC mỗi subnet để kiểm thử.
- 1-2 laptop hoặc smartphone mỗi SSID để kiểm thử.

## Dịch vụ bắt buộc

- DHCP tập trung trên một thiết bị tầng 4.
- Năm DHCP pool cho năm subnet động.
- DHCP relay trên các cổng LAN của R1, R2, R3.
- DNS A record và CNAME cho domain yêu cầu.
- HTTP website tùy biến, không giữ trang mặc định.
- Kết nối liên subnet hoàn chỉnh bằng static routing.
- WPA2-PSK cho cả hai SSID.

## Ràng buộc cấm

- Không VLAN.
- Không RIP, OSPF hoặc dynamic routing.
- Không serial link.
- Không Home Wireless Router/WRT300N.
- Không gán static IP cho client tầng 1-3.
- Không nghiệm thu website bằng IP.

## Minh chứng bắt buộc

- Gán IP cho các cổng router.
- Kết nối bốn router vào backbone switch.
- Static route trên bốn router.
- `ip helper-address` trên năm cổng LAN động.
- Năm pool trên DHCP device.
- DNS A và CNAME record.
- Nội dung website đã sửa.
- SSID và WPA2 trên hai AP.
- `ipconfig /all` ít nhất một client mỗi subnet động.
- `show ip route` trên bốn router.
- Mười hai dòng test matrix có actual result và screenshot.

## Deliverables

- `topology.pkt` được save bằng Packet Tracer 8.2 trở lên.
- `Report.pdf` đủ nội dung theo đề.
- Thư mục `configs/` chứa running-config của mỗi router và switch.
- `demo.mp4` hoặc link video dài 6-10 phút, có audio.
- Mỗi thành viên thuyết minh ít nhất một phần.

## Điểm cần xác nhận

Đề dùng mẫu tên file có ba MSSV nhưng repository này phục vụ nhóm hai người.
Nhóm cần hỏi giảng viên tên zip chính xác và ghi lại câu trả lời trước khi nộp.

