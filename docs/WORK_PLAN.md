# Phân công nhóm 2 thành viên

## Vai trò đề xuất

### Thành viên A - Network lead

- Chịu trách nhiệm address plan và sơ đồ logic.
- Dựng thiết bị, cable, hostname và interface router.
- Cấu hình static routing.
- Export config router và switch.
- Trình bày phần kiến trúc/routing trong video.

### Thành viên B - Services and evidence lead

- Cấu hình DHCP server và relay checklist.
- Cấu hình DNS, WEB và hai Access Point.
- Quản lý test matrix, ảnh và caption.
- Tổng hợp report, video và submission.
- Trình bày phần service/acceptance trong video.

## Review chéo

- B review address plan và static route của A.
- A review DHCP pool, DNS record, website và Wi-Fi của B.
- Cả hai cùng chạy test matrix từ đầu sau mỗi thay đổi lớn.
- Người không giữ `.pkt` ưu tiên viết tài liệu, review config text và chuẩn bị ảnh.

## Mốc làm việc

| Mốc | Kết quả | Owner | Reviewer |
|---|---|---|---|
| M0 | Chốt biến dự án và XX | A | B |
| M1 | Address plan + sơ đồ | A | B |
| M2 | Topology + basic config | A | B |
| M3 | Static routing pass | A | B |
| M4 | DHCP relay + 5 pool pass | B | A |
| M5 | DNS/WEB/Wi-Fi pass | B | A |
| M6 | 12 test rows pass | B | A |
| M7 | Report + configs complete | Cả hai | Cả hai |
| M8 | Demo + submission audit | Cả hai | Cả hai |

## Quy tắc bàn giao topology

- Không gửi file qua nhiều kênh không kiểm soát phiên bản.
- Trước bàn giao, ghi commit hash hoặc thời điểm file được save.
- Người nhận mở file, kiểm tra version và chạy ba smoke test.
- Smoke test gồm ping gateway, ping WEB-SRV và nslookup domain.
- Chỉ sau khi smoke test pass mới tiếp tục chỉnh topology.

