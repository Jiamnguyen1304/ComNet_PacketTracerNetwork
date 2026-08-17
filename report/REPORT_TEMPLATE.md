# Khung báo cáo kỹ thuật

## 1. Trang bìa

- Tên môn học.
- Tên đồ án.
- Tên nhóm.
- MSSV và họ tên hai thành viên.
- Giá trị `XX` đã chọn.
- Domain `mmt-XX.com`.
- Phiên bản Cisco Packet Tracer thực tế sử dụng.

## 2. Phân công công việc

Chuyển bảng phân công đã hoàn thành từ `docs/WORK_PLAN.md` vào báo cáo.

## 3. Phân tích yêu cầu

Trình bày nhu cầu từng tầng, số lượng người dùng, kiểu kết nối, dịch vụ và các
ràng buộc. Dùng nội dung đã kiểm tra trong `docs/PROJECT_ANALYSIS.md`.

## 4. Thiết kế hệ thống

- Sơ đồ logic tự vẽ có hostname, port, IP và subnet mask.
- Bảng kế hoạch địa chỉ từ `docs/ADDRESSING_PLAN.md`.
- Bảng thiết bị từ `docs/DEVICE_LIST.md`.
- Giải thích lựa chọn prefix, ưu điểm và nhược điểm.

## 5. Triển khai Packet Tracer

Chèn ảnh có caption cho các bước:

- Đặt thiết bị và nối cable.
- Đặt hostname và IP router interface.
- Kết nối bốn router vào SW-CORE.
- Cấu hình static route.
- Cấu hình `ip helper-address`.
- Tạo năm DHCP pool.
- Tạo DNS A record và CNAME.
- Thay nội dung website.
- Cấu hình SSID và WPA2 trên hai AP.

## 6. Kiểm thử

Chèn bảng đã hoàn thành từ `docs/TEST_MATRIX.md`, actual result và ảnh tương
ứng. Giải thích kết quả `show ip route`, ký hiệu `C`, `L`, `S`, câu hỏi default
route trên R1, luồng DHCP relay và luồng DNS rồi HTTP.

## 7. Issues and Resolutions

Trình bày tối thiểu ba vấn đề có thật từ `docs/ISSUES.md`, gồm triệu chứng,
nguyên nhân gốc, cách sửa và kết quả retest.

## 8. Kết luận và hướng phát triển

Đối chiếu kết quả với yêu cầu. Nêu hạn chế và đề xuất mở rộng trong tương lai,
nhưng không đưa VLAN hoặc dynamic routing vào topology hiện tại.

