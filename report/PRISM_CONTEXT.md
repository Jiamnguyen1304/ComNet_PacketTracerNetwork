# Canonical context cho Prism

## Cách sử dụng

Đây là nguồn context ngắn gọn, as-built, dùng cùng `REPORT_DRAFT_CONTENT.md`,
`SCREENSHOT_INDEX.md`, đề `Project3.pdf` và bộ ảnh. Khi tài liệu khác mâu thuẫn,
ưu tiên theo thứ tự:

1. Đề `Project3.pdf` cho yêu cầu/rubric.
2. `topology.pkt` cuối và ảnh thật cho trạng thái triển khai.
3. `docs/ADDRESSING_PLAN.md` và running-config cho số liệu kỹ thuật.
4. File này và `REPORT_DRAFT_CONTENT.md` cho nội dung report.

Không dùng các checklist/audit cũ để suy ra trạng thái hiện tại.

## Nhận dạng dự án

- Đề tài: mạng văn phòng bốn tầng bằng Cisco Packet Tracer.
- Packet Tracer: 9.0.1 64-bit, build 9.0.1.858.
- Thành viên: Nguyễn Vũ Đức Duy — 25127190; Nguyễn Minh Khôi — 25127389.
- `XX = 90`; domain `mmt-90.com`; website `www.mmt-90.com`.
- SSID: `MMT-90-STAFF`, `MMT-90-MEETING`.

## Kiến trúc cuối

| Segment | Router/interface | Network | Gateway | IP client |
|---|---|---|---|---|
| ADMIN | R1/G0/0 | `172.90.10.0/28` | `.10.1` | DHCP `.10.4-.13` |
| STAFF | R1/G0/1 | `172.90.11.0/27` | `.11.1` | DHCP `.11.4-.23` |
| TECH | R2/G0/0 -> SW-KT | `172.90.20.0/28` | `.20.1` | DHCP `.20.5-.9` |
| MGMT | R2/G0/1 -> SW-LD | `172.90.21.0/28` | `.21.1` | DHCP `.21.5-.9` |
| MEETING | R3/G0/0 | `172.90.30.0/27` | `.30.1` | DHCP `.30.4-.23` |
| SERVER | R4/G0/0 | `172.90.40.0/28` | `.40.1` | Static |
| BACKBONE | R1/R2 G0/2; R3/R4 G0/1 | `172.90.255.0/29` | Không áp dụng | Router `.1-.4` |

Server: DHCP `.40.2`, DNS `.40.3`, WEB `.40.4`. Tầng 4 có bảy PC
`.40.5-.11` cùng ba server, tổng 10 host. Không dùng VLAN, serial, dynamic
routing hoặc WRT300N.

## Trạng thái triển khai

- 18 static route: R1/R2 mỗi router 4; R3/R4 mỗi router 5.
- 5 DHCP helper về `172.90.40.2`: R1 2, R2 2, R3 1.
- 5 custom DHCP pool; `serverPool` mặc định được giữ nhưng không dùng.
- DNS A: `mmt-90.com -> 172.90.40.4`.
- DNS CNAME: `www.mmt-90.com -> mmt-90.com`.
- WEB-SRV bật HTTP và dùng nội dung `packet-tracer/web/index.html`.
- Hai AP dùng WPA2-PSK/AES, chỉ bridge Layer 2.
- T01-T12, SIM-DHCP, SIM-DNS-HTTP và S02 đều PASS, có ảnh thật.

## Issue quan trọng cần có trong report

TECH từng nhận `.21.x` vì hai uplink R2 bị đảo. Nhóm sửa thành
`R2/G0/0 -> SW-KT/TECH` và `R2/G0/1 -> SW-LD/MGMT`. Sau khi đổi dây, hai pool
tối đa năm địa chỉ bị giữ bởi lease cũ nên DHCP request tạm fail; nhóm reset
lease và cấp lại. Retest xác nhận TECH `.20.x`, MGMT `.21.x`, routing/DNS/HTTP
đều PASS. Dẫn chiếu D01-F2, C01-T10B, T06C, T06D, T02 và T03.

## Quy tắc ảnh đặc biệt

- Có 51 PNG duy nhất sau khi bỏ bản sao T06E.
- `T06E-meeting-dhcp.png` dùng cho cả T06 và T12.
- Bốn ảnh C01 dùng cho T10.
- `T09C-web-tech.png` dùng làm minh chứng nội dung website; không có C07C riêng.
- `C07B-web-http_1.png` và `_2.png` là hai phần của cấu hình HTTP/index.
- Không có ảnh C11; không tự tạo hoặc nhắc như một file tồn tại.
- Chèn ảnh theo `SCREENSHOT_INDEX.md`; tránh chèn trùng bằng cross-reference.

## Nguồn nội dung nên upload cùng

- `REPORT_DRAFT_CONTENT.md`, `REPORT_TEMPLATE.md`, `SCREENSHOT_INDEX.md`.
- `docs/PROJECT_INFO.md`, `PROJECT_ANALYSIS.md`, `ADDRESSING_PLAN.md`,
  `DEVICE_LIST.md`, `LOGICAL_TOPOLOGY.md`, `WORK_PLAN.md`, `ISSUES.md`,
  `TEST_MATRIX.md`.
- Chín running-config trong `packet-tracer/configs/` và
  `packet-tracer/web/index.html` nếu cần trích lệnh/code thật.

Không upload file audit/checklist cũ, `.git`, `.pkt` cho mục đích đọc nội dung,
ảnh trùng hoặc file debug.
