# Prompt chỉnh report trên Prism

Sao chép phần bên dưới vào Prism sau khi upload đề, context, report draft và bộ
ảnh. Không cần đưa tiêu đề Markdown này vào report.

---

Hãy chỉnh trực tiếp report hiện tại bằng các file và ảnh tôi đã tải lên.

## Nguyên tắc ưu tiên

1. Giữ nguyên trang bìa, header/footer, font, margin, mục lục, heading, bảng và
   phong cách trình bày hiện có.
2. Không viết lại toàn bộ tài liệu. Ưu tiên sửa cục bộ và thay marker
   `[[IMAGE: ...]]`/`[[IMAGE-GROUP: ...]]` trong `REPORT_DRAFT_CONTENT.md`.
3. Tự chọn phương án tốt nhất giữa chèn, chỉnh, ghi đè hoặc xóa:
   - Giữ đoạn đúng và chỉ thêm hình/dẫn chiếu.
   - Ghi đè đoạn cũ nếu mâu thuẫn với `PRISM_CONTEXT.md` hoặc bằng chứng cuối.
   - Xóa placeholder, nội dung trùng, trạng thái cũ và ảnh trùng.
   - Không xóa cả section hoặc tái thiết kế report nếu không cần thiết.
4. Khi nguồn mâu thuẫn, dùng thứ tự: đề Project3.pdf cho yêu cầu; ảnh thật và
   as-built config cho kết quả; `PRISM_CONTEXT.md`/`ADDRESSING_PLAN.md` cho số
   liệu; `REPORT_DRAFT_CONTENT.md` cho văn phong và vị trí chèn.
5. Chỉ kết luận PASS khi ảnh thực tế thể hiện thành công; không tạo ảnh hoặc
   chỉnh sửa output CLI.

## Chèn và định dạng ảnh

1. Dùng `SCREENSHOT_INDEX.md` làm manifest vị trí/caption của 51 ảnh duy nhất.
2. Mỗi ảnh chỉ chèn một lần; section khác dùng cross-reference.
3. Nhóm D01-F1..F4, C01A-D, S01A-D, T06A-E và T09A-E bằng subfigure/lưới 2 cột.
4. Giữ tỷ lệ, căn giữa, đủ lớn để đọc IP/lệnh/URL; không tách ảnh và caption qua
   hai trang.
5. Đánh số hình tự động theo thứ tự xuất hiện, dùng caption dạng
   “Hình [số]. [Thao tác, kết quả và ý nghĩa]”, rồi cập nhật mọi cross-reference
   và danh mục hình.
6. Crop trường passphrase trong C08/C09 nếu hiển thị, nhưng giữ SSID,
   WPA2-PSK và AES.
7. Không tìm hoặc tạo C07C, C11, T10 hay T12 như file riêng:
   - T09C chứng minh nội dung website thay C07C.
   - Bốn ảnh C01 đồng thời là T10.
   - T06E đồng thời là T12.
8. Hai ảnh C07B `_1` và `_2` là subfigure của cùng cấu hình HTTP/index; kiểm tra
   nội dung thật để mô tả đúng, không suy đoán thứ tự từ tên.

## Nội dung bắt buộc phải đúng

- TECH: `172.90.20.0/28`, `R2/G0/0 -> SW-KT`, gateway `.20.1`.
- MGMT: `172.90.21.0/28`, `R2/G0/1 -> SW-LD`, gateway `.21.1`.
- SERVER: `172.90.40.0/28`; DHCP `.40.2`, DNS `.40.3`, WEB `.40.4`.
- Backbone: `172.90.255.0/29`, router `.1-.4`.
- Tầng 4: 7 PC + 3 Server-PT, tổng 10 host.
- 18 static route; 5 helper về `172.90.40.2`; 5 custom DHCP pool.
- DNS A/CNAME và HTTP tại `www.mmt-90.com`.
- Hai WLAN dùng WPA2-PSK/AES; không dùng VLAN hoặc dynamic routing.
- T01-T12, SIM-DHCP, SIM-DNS-HTTP và S02 đều có evidence PASS.

## Issue phải cập nhật

Bổ sung issue ánh xạ cổng TECH/MGMT bị đảo và DHCP pool hết lease sau khi đổi
dây. Dùng nội dung chính xác trong `ISSUES.md`, dẫn chiếu D01-F2, C01-T10B,
T06C, T06D, T02 và T03; không chèn lại ảnh.

## Kiểm tra cuối

- Không còn `HUMAN_REQUIRED`, `CẦN_CHỤP`, `CẦN_CHẠY_LẠI`, “chưa chạy”,
  “sau khi hoàn tất” hoặc marker `[[IMAGE...]]`.
- Không có ảnh trùng hoặc filename thô trong caption.
- Không đảo TECH `.20` và MGMT `.21`.
- Bảng test có T01-T12 PASS với đúng evidence.
- Kết luận viết ở trạng thái đã hoàn thành, không viết ở thì tương lai.
- Giữ cấu trúc report trước đó và cập nhật mục lục/danh mục hình.

Sau khi chỉnh xong, báo lại ngắn gọn: section đã sửa, ảnh đã chèn, nội dung cũ
đã ghi đè, ảnh bị bỏ qua và vấn đề còn tồn tại.
