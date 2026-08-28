# Cấu hình thiết bị mạng

Các file hiện tại là deployment script để Duy copy/paste vào CLI của đúng thiết
bị. Không dán nhiều file vào cùng một thiết bị.

Sau khi tích hợp và kiểm thử xong, chạy `show running-config` trên từng
router/switch rồi thay deployment script bằng output thực tế để tạo bộ config
nộp cuối cùng.

Danh sách cuối gồm:

- Router: `R1.txt`, `R2.txt`, `R3.txt`, `R4.txt`.
- Switch: `SW-CORE.txt`, `SW-HC.txt`, `SW-KT.txt`, `SW-LD.txt`, `SW-SRV.txt`.

Trước khi nộp, xác nhận mỗi file là output thực tế, có đúng hostname và khớp với
phiên bản `topology.pkt` cuối.
