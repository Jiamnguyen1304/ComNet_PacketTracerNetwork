# Ma trận kiểm thử

Điền cột Actual result, Evidence ID và trạng thái sau khi chạy thật.

| # | Source | Destination | Test | Expected | Actual | Evidence | Status |
|---:|---|---|---|---|---|---|---|
| 1 | PC-HC-01 | 172.XX.10.1 | ping | Success | TODO | T01 | TODO |
| 2 | PC-HC-01 | PC-KT-01 | ping | Success | TODO | T02 | TODO |
| 3 | PC-LD-01 | WEB-SRV | ping | Success | TODO | T03 | TODO |
| 4 | LAP-MEETING-01 | PC-HC-01 | ping | Success | TODO | T04 | TODO |
| 5 | LAP-STAFF-01 | PC-HC-01 | ping | Success | TODO | T05 | TODO |
| 6 | Một client mỗi subnet động | Không áp dụng | ipconfig /all | Đúng pool/DHCP/DNS | TODO | T06A-E | TODO |
| 7 | PC-KT-01 | mmt-XX.com | nslookup | Trả IP WEB-SRV | TODO | T07 | TODO |
| 8 | PC-KT-01 | www.mmt-XX.com | nslookup | Trả IP WEB-SRV | TODO | T08 | TODO |
| 9 | Client mỗi subnet | http://www.mmt-XX.com | Browser | Trang đúng nội dung | TODO | T09A-E | TODO |
| 10 | R1-R4 | Không áp dụng | show ip route | Thấy mọi subnet | TODO | T10A-D | TODO |
| 11 | R1 | 172.XX.255.252 | ping | Success | TODO | T11 | TODO |
| 12 | LAP-MEETING-01 | Không áp dụng | ipconfig | Nhận IP MEETING | TODO | T12 | TODO |

## Luồng Simulation Mode khuyến nghị

- `SIM-DHCP`: Discover, Offer, Request, ACK từ tầng 1 qua R1 tới DHCP-SRV.
- `SIM-DNS-HTTP`: DNS query tới DNS-SRV, sau đó TCP/HTTP tới WEB-SRV.

## Quy tắc bằng chứng

- Ảnh phải thấy rõ source device và lệnh hoặc URL.
- Kết quả không crop mất địa chỉ nguồn, đích hoặc trạng thái.
- Caption giải thích mục tiêu, thao tác, kết quả và kết luận.
- Khi test fail, lưu ảnh chẩn đoán ở `report/images/` với tên có hậu tố `-debug`.
