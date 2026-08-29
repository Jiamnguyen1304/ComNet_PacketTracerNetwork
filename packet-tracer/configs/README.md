# Running-config thiết bị mạng

Chín file hiện tại là output `show running-config` thực tế được export từ bản
`topology.pkt` hoàn chỉnh của Duy.

Danh sách cuối gồm:

- Router: `R1.txt`, `R2.txt`, `R3.txt`, `R4.txt`.
- Switch: `SW-CORE.txt`, `SW-HC.txt`, `SW-KT.txt`, `SW-LD.txt`, `SW-SRV.txt`.

Đã kiểm tra tự động: bốn router có tổng cộng 18 static route, năm DHCP helper và
10 địa chỉ interface. Không có RIP, OSPF hoặc EIGRP trong running-config.
