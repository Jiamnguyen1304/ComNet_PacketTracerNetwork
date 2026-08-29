# Phân công và mức đóng góp thực tế

## Nguyễn Vũ Đức Duy (25127190) — Network lead

- Phân tích yêu cầu và lập kế hoạch địa chỉ/subnet ban đầu.
- Dựng topology, bố trí thiết bị và kết nối cable.
- Cấu hình hostname, interface router, switch và static routing.
- Export running-config của bốn router và năm switch.
- Đồng bộ bản topology nền qua nhánh `DuyVu`.
- Soạn và hoàn thiện report trên Prism.
- Phụ trách phần kiến trúc/routing trong video demo.

## Nguyễn Minh Khôi (25127389) — Services and evidence lead

- Đối chiếu topology với đề bài và address plan as-built.
- Cấu hình/kiểm tra DHCP-SRV, năm pool và DHCP relay.
- Cấu hình/kiểm tra DNS-SRV, WEB-SRV và hai Access Point.
- Phát hiện và sửa ánh xạ cổng R2 giữa TECH/MGMT; reset DHCP lease và retest.
- Chạy T01-T12, Simulation Mode và smoke test sau khi mở lại topology.
- Chụp, đặt tên và bàn giao toàn bộ ảnh minh chứng cho report.
- Phụ trách phần services/acceptance trong video demo.

## Review chéo và phối hợp

- Khôi review address plan, interface, static route và topology do Duy triển khai.
- Duy review DHCP pool, DNS record, website, Wi-Fi và nội dung report từ bộ ảnh.
- Hai thành viên cùng chịu trách nhiệm tính nhất quán giữa `.pkt`, config text,
  report và artifact nộp cuối.
- File `.pkt` chỉ do một người sửa tại một thời điểm; file text được review và
  đồng bộ qua Git.

## Trạng thái mốc công việc

| Mốc | Kết quả | Owner chính | Trạng thái |
|---|---|---|---|
| M0 | Chốt XX, domain và thông tin nhóm | Duy | Hoàn thành |
| M1 | Address plan và sơ đồ logic | Duy | Hoàn thành |
| M2 | Topology và basic config | Duy | Hoàn thành |
| M3 | Static routing | Duy | Hoàn thành |
| M4 | DHCP relay và năm pool | Khôi | Hoàn thành |
| M5 | DNS, WEB và Wi-Fi | Khôi | Hoàn thành |
| M6 | T01-T12 và Simulation Mode | Khôi | Hoàn thành |
| M7 | Bộ ảnh và context cho report | Khôi | Hoàn thành |
| M8 | Report trên Prism | Duy, Khôi review | Đang hoàn thiện |
| M9 | Video và audit gói nộp | Cả hai | Chưa thực hiện |

Phân công trên phản ánh công việc thực tế, không xóa lịch sử đóng góp của thành
viên đã dựng topology/config trước khi người còn lại tiếp quản nghiệm thu.
