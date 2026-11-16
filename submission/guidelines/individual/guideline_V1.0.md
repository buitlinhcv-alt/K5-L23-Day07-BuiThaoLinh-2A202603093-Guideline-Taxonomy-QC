# Guideline gán nhãn — v1.0 — Nhóm RANDOM

- Phiên bản: v1.0 (Bản gộp chung từ v0 của 101 và 102)
- Ngày: 16/11/2026
- Người viết / duyệt: Bùi Thảo Linh (101) & Vũ Văn Đăng (102)
- Loại nhãn: Bounding box trên ảnh đường phố 2D (CVAT xuất COCO 1.0 hoặc YOLO)
- Căn cứ: `guideline_101.md`, `guideline_102.md`, `RULES.md`, `CHECKPOINTS.md`

## 1. Mục tiêu
Guideline này quy định cách gán nhãn để huấn luyện mô hình (YOLO) phát hiện người và phương tiện thật trong ảnh đường phố. 
Mục tiêu là đảm bảo tính thống nhất, tránh việc mô hình học sai đối tượng/vị trí. Tuyệt đối tránh các lỗi: gán nhầm loại phương tiện, bỏ sót phương tiện/người, vẽ box lỏng (không ôm sát), gộp người với xe chung một box, hoặc gán nhãn vật thể ảo/đồ chơi.

## 2. Lớp và thuộc tính

### Lớp
Mỗi đối tượng chỉ nhận đúng một lớp. Tuyệt đối không thêm lớp ngoài taxonomy hiện có.

| Lớp | Định nghĩa | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con chở người dạng sedan, hatchback, SUV, MPV; thân liền, không có thùng hàng tách riêng. | Xe 4-7 chỗ. | Dễ nhầm với `van`; khác ở dáng xe thấp. Xe bán tải thùng rời là `truck`. |
| `truck` | Xe chuyên chở hàng hóa có cabin và thùng/sàn chở hàng phân biệt rõ, hoặc đầu kéo kéo rơ-moóc. | Xe tải thùng, xe bồn, xe chở xe. | Xe van kín thùng thân liền; xe bán tải độ nắp cao. |
| `van` | Xe tải nhẹ thân liền, khoang sau dạng hộp cao chở hàng, hoặc chở người (minivan), thường có cửa lùa. | Delivery van, minivan. | Dễ nhầm với bus (lớn hơn) và truck (thùng tách rời). |
| `motorcycle` | Xe hai bánh có động cơ, đang chạy hoặc đỗ. | Xe tay ga, xe số, mô tô. | Xe đạp, xe ba bánh (tuk-tuk). |
| `person` | Người thật hiện diện trực tiếp trong cảnh, đi bộ, đứng, hoặc đang điều khiển phương tiện. | Người đi bộ qua đường, người lái xe. | Tượng, ma-nơ-canh, hình in, người phản chiếu trong gương. |
| `ignore_region` | Vùng bỏ qua khi có đám đông mục tiêu quá nhỏ hoặc dày đặc không thể tách từng vật. | Cụm người rất nhỏ ở xa hoặc xe đỗ dày đặc che nhau. | Không dùng để che lỗi, né các ca cần review, hoặc gán cho vật ngoài schema. |

**Vật không thuộc taxonomy:** Các đối tượng như xe đạp, xe buýt cỡ lớn, xích lô, xe ba gác, biển báo, đèn tín hiệu => **Không gán nhãn (0 box)**. Người thật đi trên các xe này vẫn gán box `person` bình thường. 

### Thuộc tính
Tỉ lệ che là ước lượng phần diện tích bị vật khác che khuất (không tính phần nằm ngoài khung ảnh). Gán đầy đủ thuộc tính cho từng box theo các ngưỡng dưới đây:

| Thuộc tính | Giá trị cố định | Ý nghĩa / ngưỡng |
|---|---|---|
| `occluded_level` | `0` | Thấy rõ, không bị vật khác che (0%). |
| `occluded_level` | `1` | Bị che ít: trên 0% đến hết 30%. |
| `occluded_level` | `2` | Bị che nhiều: trên 30% đến hết 70%. |
| `occluded_level` | `3` | Không rõ: Bị che trên 70%, hoặc không đủ bằng chứng ước lượng (cần tick `can_review`). |
| `truncated` | `no` | Vật nằm hoàn toàn trong ảnh (kể cả chạm sát mép nhưng không mất chi tiết). |
| `truncated` | `yes` | Vật bị cắt mép ảnh (một phần nằm ngoài khung). |
| `can_review` | không tick / `false` | Bạn đã chắc chắn quyết định, quy tắc rõ ràng. |
| `can_review` | tick / `true` | Mơ hồ về lớp, mức che khuất, hoặc hình học. Ghi lại để nhóm hoặc reviewer xem lại. |

*(Quy ước riêng cho `ignore_region`: Mặc định gán `occluded_level=0`, `truncated=no`, `can_review=false` cho bản thân vùng đó).*

## 3. Quy tắc vẽ box

1. **Nguyên tắc 1 box (R-03, R-05, R-06):** Box thẳng trục, ôm sát phần nhìn thấy của vật (kể cả mũ bảo hiểm của người). Không lấy dư ra môi trường, khói, bóng đổ hay hàng hóa rời. 
   - Không gộp chung người và xe thành 1 box; mỗi người 1 box `person`, mỗi xe 1 box xe.
   - Xe chở xe: Box xe vận chuyển riêng, box từng chiếc xe được chở phía trên riêng (theo đúng lớp của xe đó).
2. **Che khuất & Cắt mép (R-04):** Vẽ box ôm sát **phần còn nhìn thấy**, không suy đoán và vẽ lấn ra phần bị khuất hay nằm ngoài ảnh. Cập nhật chính xác `occluded_level` và `truncated`. Vật khuất hoàn toàn không vẽ.
3. **Hiện vật giả & Ego (R-07, R-08):** Tuyệt đối không gán nhãn cho các vật giả như xe đồ chơi, hình in quảng cáo, tượng điêu khắc, ma-nơ-canh, hay hình bóng phản chiếu trong gương/nước. Bỏ qua phần nắp ca-pô/gương của xe chủ (ego) đang gắn camera.
4. **Vật quá nhỏ / Đám đông (R-09, R-10):** Đo theo ảnh gốc. Kích thước box nhìn thấy dưới 20 px: không gán lớp, vẽ vùng `ignore_region` bao quanh. Đám đông đan xen dày đặc không thể tách biên: dùng `ignore_region` sát vùng cụm.
5. **Mơ hồ (R-11):** Gặp ca chưa chắc chắn: Chọn lớp có bằng chứng mạnh nhất, tick `can_review` và ghi chú ảnh vào nhật ký QC để thảo luận nhóm, không gán lụi hoặc dùng ignore_region bừa bãi.

## 4. Ca khó

| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| `img_002` | Xe lồng chở nhiều ô tô con trên thùng. | R-03, R-06 | **Gán 1 box truck lớn, gán các box car nhỏ** cho các xe nằm trên. | Mỗi xe thật là một vật độc lập, kể cả khi đang đỗ trên sàn xe khác. |
| `img_005` | Người ngồi trên xe máy che khuất nhau. | R-04, R-05 | **Tách box người và box xe máy riêng**. | Cấu trúc taxonomy yêu cầu tách riêng người và phương tiện. |
| `img_004`, `img_009` | Tuk-tuk, xe 3 bánh. | R-01, R-11 | **Không tự gán bừa**. Đánh dấu hỏi Reviewer. | Chưa có lớp khớp trong hệ thống, tránh làm nhiễu dữ liệu. |
| `img_035`, `img_037` | Xe bán tải độ nắp thùng cao. | R-01 | **Hỏi Reviewer**. | Ranh giới giữa van và truck bị xóa mờ. |
| `img_017`, `020`, `033`, `038` | Tượng, hình phản chiếu, ma-nơ-canh, đồ chơi. | R-07 | **Tuyệt đối không gán nhãn.** | Chỉ gán người/phương tiện thật hiện diện trực tiếp. |
| Nhiều ảnh | Cụm người/xe dưới 20px hoặc quá dày. | R-09, R-10 | **Vẽ `ignore_region`.** | Không thể tách biên vật để mô hình học. |

## 5. QC và phiên bản

### Ngưỡng và Tiêu chí QC
- Kích thước hợp lệ để gán: ≥ 20 px.
- Ghép box đánh giá IAA: Chấp nhận IoU ≥ 0.5 (không phân biệt lớp).
- **Ngưỡng chất lượng nhãn:** Đạt F1-score ≥ 0.80 và mean IoU ≥ 0.70 khi so khớp với bộ nhãn chuẩn (gold labels).
- **Lỗi tỷ lệ:** Không vượt quá 2% số ảnh lỗi trên tổng số ảnh duyệt. Một lỗi (bỏ sót, sai lớp, box lỏng, gán sai đồ chơi) trên lô 40-50 ảnh là đủ để yêu cầu Rework toàn bộ.

### Quy trình và Người quyết định
- Annotator: Chạy lệnh `tools/check_yolo_labels.py` trước khi nộp để rà soát format. Tự mở ngẫu nhiên >= 10 ảnh kiểm tra trực quan. 
- Reviewer: Bùi Thảo Linh & Vũ Văn Đăng rà soát chéo các ca `can_review`. Quyết định nhóm là quyết định cuối để giải quyết ca mơ hồ. 
- Trạng thái: Hold (thiếu bằng chứng), Rework (lỗi > 2%), Release (đạt các ngưỡng trên).

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.0 | Hợp nhất guideline cá nhân 101 và 102 thành bản chung nhóm. | Thống nhất định nghĩa 4 mức che khuất, xử lý xe chở xe, đồ chơi, và cách áp dụng ignore_region. |
