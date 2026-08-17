# Running-config exports

Sau mỗi checkpoint cấu hình, mở CLI của từng router/switch và lấy kết quả
`show running-config` để thay nội dung placeholder trong file cùng tên.

Danh sách cuối gồm:

- Router: `R1.txt`, `R2.txt`, `R3.txt`, `R4.txt`.
- Switch: `SW-CORE.txt`, `SW-HC.txt`, `SW-KT.txt`, `SW-LD.txt`, `SW-SRV.txt`.

Trước khi nộp, xác nhận mỗi file có đúng hostname, không còn dòng Placeholder,
và khớp với phiên bản `topology.pkt` cuối.

