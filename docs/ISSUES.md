# Issues and Resolutions thực tế

## Issue 1 — Ánh xạ cổng R2 của TECH/MGMT bị đảo và DHCP pool hết lease

- **Triệu chứng:** client trong phòng TECH nhận `172.90.21.x` thay vì
  `172.90.20.x`. Sau khi đổi hai uplink, yêu cầu DHCP mới ban đầu báo failed.
- **Nguyên nhân gốc:** SW-KT từng nối `R2/G0/1` (gateway `.21.1`) và SW-LD từng
  nối `R2/G0/0` (gateway `.20.1`). Năm client của mỗi nhánh đã giữ toàn bộ năm
  lease trong pool đối diện; sau khi đổi dây, hai pool tạm thời không còn địa
  chỉ trống cho đúng nhóm client.
- **Cách sửa:** nối `R2/G0/0 -> SW-KT` và `R2/G0/1 -> SW-LD`; giữ nguyên
  `POOL-TECH`/`POOL-MGMT` theo address plan; reset lease rồi yêu cầu DHCP lại
  cho toàn bộ client tầng 2.
- **Retest:** TECH nhận `.20.5-.20.9/28`, gateway `.20.1`; MGMT nhận
  `.21.5-.21.9/28`, gateway `.21.1`. T02, T03, DNS, HTTP và Simulation đều PASS.
- **Evidence:** `D01-F2.png`, `C01-T10B-R2-interface-route.png`,
  `T06C-tech-dhcp.png`, `T06D-mgmt-dhcp.png`, `T02-admin-to-tech.png`,
  `T03-mgmt-to-web.png`.

## Issue 2 — Tài liệu dịch vụ từng lệch address plan as-built

- **Triệu chứng:** tài liệu cũ dùng SERVER `172.90.60.0/28` và backbone
  `.249-.252`, trong khi topology cuối dùng SERVER `172.90.40.0/28` và backbone
  `.1-.4`.
- **Nguyên nhân gốc:** hai nhánh phát triển dựa trên hai phiên bản address plan.
- **Cách sửa:** chọn `topology.pkt`, running-config và ảnh nghiệm thu làm nguồn
  chuẩn; đồng bộ address plan, services, test matrix và report draft.
- **Retest:** xác nhận 10 interface IP, 18 static route, 5 DHCP helper và không
  còn địa chỉ cũ trong artifact nộp chính.
- **Bài học:** khóa address plan trước triển khai và cập nhật tài liệu từ
  running-config as-built thay vì bản thiết kế cũ.

## Issue 3 — `serverPool` mặc định không thể xóa

- **Triệu chứng:** nút `Remove` bị khóa đối với `serverPool`; pool hiển thị
  gateway/DNS `0.0.0.0` và maximum users 512.
- **Nguyên nhân:** đây là pool mặc định của Server-PT trong Packet Tracer.
- **Cách xử lý:** giữ nguyên nhưng không dùng. SERVER subnet chỉ có IP tĩnh; năm
  mạng client từ xa được ánh xạ vào năm custom pool bằng DHCP relay.
- **Retest:** DHCP Service On, đủ năm pool nghiệp vụ và T06A-E đều PASS.

## Issue 4 — Tầng 4 vượt giới hạn tối đa 10 host

- **Triệu chứng:** phiên bản ban đầu có 10 PC cùng 3 Server-PT, tổng 13 host.
- **Nguyên nhân:** ba server chưa được tính vào giới hạn host của đề.
- **Cách sửa:** xóa PC18-PC20, giữ `SRV-PC01..07` cùng DHCP/DNS/WEB server;
  gán PC tĩnh `.40.5-.40.11`.
- **Retest:** D01-F4 thể hiện đúng 7 PC + 3 server; C10A-B xác nhận cấu hình và
  kết nối nội bộ SERVER subnet.

## Issue 5 — Tài liệu endpoint và bố cục không khớp topology cuối

- **Triệu chứng:** tài liệu dùng tên minh họa `PC-HC-01`, đường dẫn `.pkt` cũ,
  và ảnh overview quá nhỏ để đọc nhãn.
- **Nguyên nhân:** tài liệu được viết trước khi topology được bố trí dọc và đổi
  display name endpoint.
- **Cách sửa:** dùng `topology.pkt` ở thư mục gốc; chuẩn hóa tên theo vai trò;
  dùng một overview và bốn ảnh zoom D01-F1..F4.
- **Retest:** caption và test matrix dùng đúng client/subnet trên ảnh cuối.

## Issue 6 — Merge conflict khi hợp nhất topology hoàn chỉnh

- **Triệu chứng:** Git conflict ở chín running-config và website khi hợp nhất
  nhánh DuyVu với tài liệu/services của Khôi.
- **Nguyên nhân:** hai thành viên cùng sửa các đường dẫn artifact text.
- **Cách sửa:** giữ topology/running-config/website từ DuyVu, giữ tài liệu và
  service checklist đã review của Khôi; tạo merge commit `62181f9`.
- **Retest:** các nhánh bàn giao không còn khác biệt ở artifact do Duy sở hữu.
- **Bài học:** thống nhất owner từng file và chỉ một người sửa `.pkt` tại một
  thời điểm.

## Ba issue nên ưu tiên trong report

Nếu cần giới hạn độ dài, ưu tiên Issue 1, Issue 2 và Issue 3 vì chúng thể hiện rõ
quy trình chẩn đoán mạng, sửa cấu hình/topology và retest bằng bằng chứng.
