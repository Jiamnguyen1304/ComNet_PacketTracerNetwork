# Đồ án mạng văn phòng bốn tầng

Mô hình Cisco Packet Tracer 9.0.1 triển khai static routing, DHCP tập trung,
DNS, HTTP và hai WLAN WPA2-PSK cho văn phòng bốn tầng. Topology kỹ thuật và bộ
ảnh nghiệm thu đã hoàn thành; report trên Prism và video là các deliverable còn
được hoàn thiện riêng.

## Nguồn chuẩn

1. `topology.pkt` — topology as-built cuối.
2. `docs/ADDRESSING_PLAN.md` — subnet, interface, DHCP pool.
3. `packet-tracer/configs/` — running-config của 4 router và 5 switch.
4. `packet-tracer/web/index.html` — nội dung WEB-SRV.
5. `docs/TEST_MATRIX.md` — kết quả T01-T12 và Simulation.
6. `report/images/` — 51 ảnh nghiệm thu duy nhất.
7. `report/PRISM_CONTEXT.md` — context ngắn gọn để đưa lên Prism.

## Thiết kế cuối

| Khu vực | Network | Gateway | Cấp IP |
|---|---|---|---|
| ADMIN tầng 1 | `172.90.10.0/28` | `172.90.10.1` | DHCP |
| STAFF Wi-Fi tầng 1 | `172.90.11.0/27` | `172.90.11.1` | DHCP |
| TECH tầng 2 | `172.90.20.0/28` | `172.90.20.1` | DHCP |
| MGMT tầng 2 | `172.90.21.0/28` | `172.90.21.1` | DHCP |
| MEETING Wi-Fi tầng 3 | `172.90.30.0/27` | `172.90.30.1` | DHCP |
| SERVER tầng 4 | `172.90.40.0/28` | `172.90.40.1` | Static |
| BACKBONE | `172.90.255.0/29` | Không áp dụng | Static |

Ánh xạ R2 cuối: `G0/0 -> SW-KT/TECH`, `G0/1 -> SW-LD/MGMT`,
`G0/2 -> SW-CORE`. Hệ thống có 18 static route, 5 DHCP helper, 5 custom pool,
DNS A/CNAME và website tại `www.mmt-90.com`.

## Cấu trúc cần bàn giao

```text
Packet_tracer_project/
|-- topology.pkt
|-- ARCHITECTURE.md
|-- docs/
|   |-- PROJECT_INFO.md
|   |-- PROJECT_ANALYSIS.md
|   |-- ADDRESSING_PLAN.md
|   |-- DEVICE_LIST.md
|   |-- LOGICAL_TOPOLOGY.md
|   |-- TEST_MATRIX.md
|   |-- WORK_PLAN.md
|   `-- ISSUES.md
|-- packet-tracer/
|   |-- configs/
|   `-- web/index.html
|-- report/
|   |-- PRISM_CONTEXT.md
|   |-- PRISM_PROMPT.md
|   |-- REPORT_DRAFT_CONTENT.md
|   |-- REPORT_TEMPLATE.md
|   |-- SCREENSHOT_INDEX.md
|   `-- images/
`-- demo/
    |-- SCRIPT.md
    `-- demo-link.txt
```

## Trạng thái

- Topology, services, routing và T01-T12: hoàn thành.
- Simulation DHCP/DNS-HTTP và smoke test: hoàn thành.
- Ảnh nghiệm thu: hoàn thành, đã loại ảnh trùng.
- Report trên Prism: đang chèn ảnh và hoàn thiện định dạng.
- Video demo/link nộp: chưa thực hiện.

Tên file nén, deadline và link video phải lấy từ LMS/thông báo giảng viên;
không dùng placeholder trong artifact nộp cuối.
