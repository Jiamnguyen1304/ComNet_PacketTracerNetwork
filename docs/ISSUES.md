# Issues and Resolutions đã gặp thật

## Issue 4 - Tài liệu endpoint và đường dẫn không khớp topology vật lý hiện tại

- Triệu chứng: một số file dùng tên client minh họa như `PC-HC-01`, chỉ mô tả
  ba server ở tầng 4 và trỏ nhầm tới `packet-tracer/topology.pkt`; ảnh overview
  bố cục dọc cũng quá nhỏ để đọc label.
- Nguyên nhân: tài liệu nền được viết trước khi topology cuối được bố trí lại và
  thêm các cụm endpoint đầy đủ trên canvas.
- Cách sửa: dùng `topology.pkt` ở thư mục gốc làm nguồn chuẩn; mô tả client theo
  vai trò và tên/IP thật khi test; bổ sung cụm host static tầng 4; tách ảnh
  overview và bốn ảnh zoom theo tầng.
- Phòng ngừa: không đưa display name hoặc port chưa xác nhận từ GUI vào report;
  sau mỗi lần đổi bố cục phải review `LOGICAL_TOPOLOGY.md`, `DEVICE_LIST.md` và
  `SCREENSHOT_INDEX.md`.

## Issue 5 - Tầng 4 vượt giới hạn host và canvas khó dùng khi nghiệm thu

- Thời điểm: 2026-08-29 18:11, khi review ba ảnh zoom topology.
- Triệu chứng: tầng 4 có PC11-PC20 cùng DHCP/DNS/WEB server, tổng 13 host trong
  khi đề giới hạn tối đa 10 host; nhãn tầng 1 ghi “Phòng quản trị”; canvas chưa
  có network/prefix/gateway label và client có nhiều tên trùng dạng `(1)`.
- Quyết định: tính Server-PT là host; xóa PC18-PC20, giữ bảy PC và ba server;
  đổi tên endpoint theo khu vực, đổi nhãn tầng 1 thành “Phòng hành chính” và bổ
  sung label cho sáu LAN cùng backbone.
- Retest bắt buộc: kiểm đếm lại, kiểm tra IP static `.40.5-.40.11`, chờ link
  xanh, chụp lại D01-F1..F4 rồi mới chạy test matrix.

## Issue 1 - Tài liệu dịch vụ lệch address plan của topology cuối

- Thời điểm: 2026-08-29, sau khi pull bản hoàn chỉnh từ DuyVu.
- Người phát hiện: Khôi trong quá trình kiểm tra route/DHCP.
- Triệu chứng: file service/checklist cũ dùng SERVER `172.90.60.0/28` và
  backbone `.249-.252`, trong khi CLI thực tế hiển thị SERVER
  `172.90.40.0/28` và backbone `.1-.4`.
- Phạm vi ảnh hưởng: DHCP helper, server IP, DNS A record, test matrix và số
  liệu đưa vào report có nguy cơ sai dù topology hoạt động.
- Chẩn đoán: đối chiếu `show ip interface brief`, `show ip route`,
  `show running-config` của R1-R4 và ảnh DHCP-SRV.
- Nguyên nhân gốc: hai nhánh phát triển dựa trên hai phiên bản address plan; tài
  liệu Khôi chưa được cập nhật sau khi Duy tối ưu subnet theo tầng.
- Cách sửa: chọn running-config/topology cuối làm nguồn chuẩn; đồng bộ toàn bộ
  address plan, service checklist, test matrix và report draft.
- Retest: kiểm tra tự động xác nhận 10 router interface IP, 18 static route, năm
  helper `172.90.40.2`; tìm kiếm lại không còn địa chỉ `.60.x` trong artifact
  nộp chính.
- Bài học: khóa address plan trước khi cấu hình và luôn cập nhật tài liệu từ
  as-built running-config thay vì từ bản thiết kế cũ.

## Issue 2 - Merge conflict khi hợp nhất bản DuyVu hoàn chỉnh

- Thời điểm: 2026-08-29.
- Người phát hiện: Khôi khi pull nhánh DuyVu.
- Triệu chứng: Git báo conflict ở 9 file router/switch config và
  `packet-tracer/web/index.html`; topology mới được stage riêng.
- Phạm vi ảnh hưởng: không thể hoàn tất merge/push lên Khoi nếu chưa chọn nguồn
  đúng cho artifact cấu hình.
- Nguyên nhân gốc: Khôi đã chuẩn bị deployment script trong khi Duy export
  running-config thật vào cùng các path.
- Cách sửa: giữ phiên bản DuyVu cho topology, chín running-config và website;
  giữ tài liệu/checklist riêng của Khôi; tạo merge commit `62181f9`.
- Retest: so sánh các artifact do Duy sở hữu giữa `origin/DuyVu` và
  `origin/Khoi` không còn khác biệt; push Khoi thành công.
- Bài học: tách deployment script khỏi thư mục running-config hoặc thống nhất
  owner file trước khi hai người cùng chỉnh.

## Issue 3 - Không thể xóa pool mặc định serverPool

- Thời điểm: 2026-08-29 khi kiểm tra DHCP-SRV.
- Người phát hiện: Khôi.
- Triệu chứng: nút `Remove` bị khóa đối với `serverPool`; pool hiển thị gateway
  và DNS `0.0.0.0`, start address `172.90.40.0` và maximum users 512.
- Phạm vi ảnh hưởng: có thể gây nhầm rằng DHCP Server đang có pool thừa hoặc
  không hợp lệ.
- Nguyên nhân gốc: `serverPool` là pool mặc định do Server-PT tạo và không cho
  xóa trong phiên bản Packet Tracer đang dùng.
- Cách xử lý: giữ nguyên pool mặc định; không có dynamic client trong SERVER
  subnet; năm client subnet từ xa được ánh xạ vào năm custom pool thông qua
  DHCP relay.
- Retest: DHCP Service vẫn On, đủ năm custom pool và client nhận địa chỉ
  `172.90.x.x` thành công.
- Bài học: phân biệt artifact mặc định của simulator với pool nghiệp vụ; dùng
  kết quả lease thực tế để xác nhận thay vì cố xóa thành phần hệ thống.
