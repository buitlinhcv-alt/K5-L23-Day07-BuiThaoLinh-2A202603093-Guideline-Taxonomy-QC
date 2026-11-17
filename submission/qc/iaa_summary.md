# Tóm tắt IAA (taxonomy v1.0)

- Ảnh được so sánh (có >=2 người gán): **40**; số người gán: 2
- Lượt gán đủ **80/80**.
- IoU trung bình và % cùng lớp **chỉ tính trên các cặp box đã ghép** (IoU >= 0.5, ghép 1-1 không phân biệt lớp). Box chưa ghép được thống kê riêng ở cột bỏ sót/thừa.
- Mẫu số: 260 cặp box ghép (tổng mọi cặp người gán, ảnh chung).

| Cặp | Ảnh | Cặp box ghép | IoU TB | Cùng lớp | Cùng occluded_level | Cùng truncated | Chưa ghép (A/B) | Bỏ qua do ignore | Đạt IAA |
|---|---|---|---|---|---|---|---|---|---|
| **TỔNG** | 40 | 260 | 0.781 | 98.5% | 33.5% | 91.2% | 50/199 | 4 | CHƯA ĐẠT |
| 101|102 | 40 | 260 | 0.781 | 98.5% | 33.5% | 91.2% | 50/199 | 4 | CHƯA ĐẠT |

Ngưỡng IAA: IoU TB >= 0.85, đồng thuận lớp >= 95%.

Số ca bất thường: 1085 ({'bo_sot_thua': 219, 'co_can_review': 465, 'iou_thap': 153, 'sai_thuoc_tinh': 185, 'bat_dong_ignore': 59, 'sai_lop': 4}), trên 35 ảnh cần calibration.
