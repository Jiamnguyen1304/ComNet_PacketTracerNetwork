# Phân công nhóm 2 thành viên

## Bàn giao cuối ngày 29/08/2026

- Nguyễn Minh Khôi thực hiện toàn bộ phần Packet Tracer còn lại: audit topology,
  kiểm tra services, chạy T01-T12, Simulation Mode, chụp ảnh và smoke test sau
  khi save.
- Nguyễn Vũ Đức Duy tập trung viết report trên Prism và nhận bộ ảnh từ Khôi.
- Việc Khôi nhận phần thực hành của Duy không thay đổi lịch sử đóng góp: Duy đã
  phân tích yêu cầu, lập address plan, dựng topology, cấu hình router/switch và
  static routing.
- Khi quay demo, cả hai vẫn phải có audio vì đề yêu cầu mỗi thành viên thuyết
  minh ít nhất một phần.

## Vai trò đề xuất

### Nguyễn Vũ Đức Duy (25127190) - Network lead

- Chịu trách nhiệm address plan và sơ đồ logic.
- Dựng thiết bị, cable, hostname và interface router.
- Cấu hình static routing.
- Export config router và switch.
- Trình bày phần kiến trúc/routing trong video.

### Nguyễn Minh Khôi (25127389) - Services and evidence lead

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
| M0 | Chốt biến dự án và XX | Duy | Khôi |
| M1 | Address plan + sơ đồ | Duy | Khôi |
| M2 | Topology + basic config | Duy | Khôi |
| M3 | Static routing pass | Duy | Khôi |
| M4 | DHCP relay + 5 pool pass | Khôi | Duy |
| M5 | DNS/WEB/Wi-Fi pass | Khôi | Duy |
| M6 | 12 test rows pass | Khôi | Duy |
| M7 | Report + configs complete | Cả hai | Cả hai |
| M8 | Demo + submission audit | Cả hai | Cả hai |

## Quy tắc bàn giao topology

- Không gửi file qua nhiều kênh không kiểm soát phiên bản.
- Trước bàn giao, ghi commit hash hoặc thời điểm file được save.
- Người nhận mở file, kiểm tra version và chạy ba smoke test.
- Smoke test gồm ping gateway, ping WEB-SRV và nslookup domain.
- Chỉ sau khi smoke test pass mới tiếp tục chỉnh topology.
