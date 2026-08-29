# Ma trận kiểm thử

Toàn bộ kết quả cũ đã được reset ngày 29/08/2026. Điền cột Actual, Evidence và
Status chỉ sau khi chạy lại trên `topology.pkt` cuối; không kế thừa PASS từ ảnh
hoặc lần kiểm tra trước.

| # | Source | Destination | Test | Expected | Actual | Evidence | Status |
|---:|---|---|---|---|---|---|---|
| 1 | PC đại diện ADMIN (ghi tên thật) | 172.90.10.1 | ping | Success | Chưa chạy lại | T01 | CẦN_CHẠY_LẠI |
| 2 | PC đại diện ADMIN | IP thật của PC TECH đại diện | ping | Success | Chưa chạy lại | T02 | CẦN_CHẠY_LẠI |
| 3 | PC đại diện MGMT | 172.90.40.4 | ping | Success | Chưa chạy lại | T03 | CẦN_CHẠY_LẠI |
| 4 | Wireless client MEETING | IP thật của PC ADMIN đại diện | ping | Success | Chưa chạy lại | T04 | CẦN_CHẠY_LẠI |
| 5 | Wireless client STAFF | IP thật của PC ADMIN đại diện | ping | Success | Chưa chạy lại | T05 | CẦN_CHẠY_LẠI |
| 6 | Một client mỗi subnet động | Không áp dụng | ipconfig /all | Đúng pool, gateway và DNS `172.90.40.3` | Chưa chạy lại | T06A-E | CẦN_CHẠY_LẠI |
| 7 | PC TECH đại diện | mmt-90.com | nslookup | Trả `172.90.40.4` | Chưa chạy lại | T07 | CẦN_CHẠY_LẠI |
| 8 | PC TECH đại diện | www.mmt-90.com | nslookup | Trả `172.90.40.4` | Chưa chạy lại | T08 | CẦN_CHẠY_LẠI |
| 9 | Một client mỗi subnet động | http://www.mmt-90.com | Browser | Trang đúng nội dung | Chưa chạy lại | T09A-E | CẦN_CHẠY_LẠI |
| 10 | R1-R4 | Không áp dụng | show ip route | R1/R2: 4 S; R3/R4: 5 S | Chưa chạy lại | T10A-D | CẦN_CHẠY_LẠI |
| 11 | R1 | 172.90.255.4 | ping | Success | Chưa chạy lại | T11 | CẦN_CHẠY_LẠI |
| 12 | Wireless client MEETING đã association | Không áp dụng | ipconfig | Nhận IP thuộc `172.90.30.0/27` | Chưa chạy lại | T12 | CẦN_CHẠY_LẠI |

## Luồng Simulation Mode khuyến nghị

- `SIM-DHCP`: Discover, Offer, Request, ACK từ tầng 1 qua R1 tới DHCP-SRV.
- `SIM-DNS-HTTP`: DNS query tới DNS-SRV, sau đó TCP/HTTP tới WEB-SRV.

## Quy tắc bằng chứng

- Ảnh phải thấy rõ source device và lệnh hoặc URL.
- Kết quả không crop mất địa chỉ nguồn, đích hoặc trạng thái.
- Caption giải thích mục tiêu, thao tác, kết quả và kết luận.
- Khi test fail, lưu ảnh chẩn đoán ở `report/images/` với tên có hậu tố `-debug`.
