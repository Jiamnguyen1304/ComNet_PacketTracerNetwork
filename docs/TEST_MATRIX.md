# Ma trận kiểm thử

Điền cột Actual result, Evidence ID và trạng thái sau khi chạy thật.

| # | Source | Destination | Test | Expected | Actual | Evidence | Status |
|---:|---|---|---|---|---|---|---|
| 1 | PC-HC-01 | 172.90.10.1 | ping | Success | Chưa chạy | T01 | HUMAN_REQUIRED |
| 2 | PC-HC-01 | PC-KT-01 | ping | Success | Chưa chạy | T02 | HUMAN_REQUIRED |
| 3 | PC-LD-01 | 172.90.60.12 | ping | Success | Chưa chạy | T03 | HUMAN_REQUIRED |
| 4 | LAP-MEETING-01 | PC-HC-01 | ping | Success | Chưa chạy | T04 | HUMAN_REQUIRED |
| 5 | LAP-STAFF-01 | PC-HC-01 | ping | Success | Chưa chạy | T05 | HUMAN_REQUIRED |
| 6 | Một client mỗi subnet động | Không áp dụng | ipconfig /all | Đúng pool, gateway và DNS | Chưa chạy | T06A-E | HUMAN_REQUIRED |
| 7 | PC-KT-01 | mmt-90.com | nslookup | Trả 172.90.60.12 | Chưa chạy | T07 | HUMAN_REQUIRED |
| 8 | PC-KT-01 | www.mmt-90.com | nslookup | Trả 172.90.60.12 | Chưa chạy | T08 | HUMAN_REQUIRED |
| 9 | Một client mỗi subnet động | http://www.mmt-90.com | Browser | Trang đúng nội dung | Chưa chạy | T09A-E | HUMAN_REQUIRED |
| 10 | R1-R4 | Không áp dụng | show ip route | Có connected/local và đủ static route | Chưa chạy | T10A-D | HUMAN_REQUIRED |
| 11 | R1 | 172.90.255.252 | ping | Success | Chưa chạy | T11 | HUMAN_REQUIRED |
| 12 | LAP-MEETING-01 | Không áp dụng | ipconfig | Nhận 172.90.50.10-29/27 | Chưa chạy | T12 | HUMAN_REQUIRED |

## Luồng Simulation Mode khuyến nghị

- `SIM-DHCP`: Discover, Offer, Request, ACK từ tầng 1 qua R1 tới DHCP-SRV.
- `SIM-DNS-HTTP`: DNS query tới DNS-SRV, sau đó TCP/HTTP tới WEB-SRV.

## Quy tắc bằng chứng

- Ảnh phải thấy rõ source device và lệnh hoặc URL.
- Kết quả không crop mất địa chỉ nguồn, đích hoặc trạng thái.
- Caption giải thích mục tiêu, thao tác, kết quả và kết luận.
- Khi test fail, lưu ảnh chẩn đoán ở `report/images/` với tên có hậu tố `-debug`.
