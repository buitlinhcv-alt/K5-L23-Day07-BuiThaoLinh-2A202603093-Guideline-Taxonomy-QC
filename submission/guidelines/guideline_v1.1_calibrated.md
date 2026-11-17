# Guideline gán nhãn — v1.1 — Nhóm RANDOM

- Phiên bản: v1.1 — bổ sung chú thích hồ sơ calibration; quy tắc gán giữ nguyên v1.0.
- Ngày: 16/11/2026
- Người viết / duyệt: Bùi Thảo Linh (101) & Vũ Văn Đăng (102)
- Loại nhãn: Bounding box trên ảnh đường phố 2D; gán trong CVAT, xuất COCO 1.0.
- Căn cứ: `submission/guidelines/individual/guideline_101.md`, `submission/guidelines/individual/guideline_102.md`, `RULES.md`, `GUIDE.md`, `docs/QC_CHEATSHEET.md`, `submission/taxonomy.json`.

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
| `person` | Người thật hiện diện trực tiếp trong cảnh, đi bộ, đứng, ngồi hoặc đi trên phương tiện, gồm cả hành khách. | Người đi bộ qua đường, người lái xe và người ngồi sau. | Tượng, ma-nơ-canh, hình in, người phản chiếu trong gương. |
| `ignore_region` | Vùng bỏ qua khi có đám đông mục tiêu quá nhỏ hoặc dày đặc không thể tách từng vật. | Cụm người rất nhỏ ở xa hoặc xe đỗ dày đặc che nhau. | Không dùng để che lỗi, né các ca cần review, hoặc gán cho vật ngoài schema. |

**R-01 — Vật không thuộc taxonomy:** Xe đạp, xe buýt cỡ lớn, xích lô, xe ba gác, tuk-tuk, biển báo và đèn tín hiệu không có box lớp đối tượng; không tạo `ignore_region` chỉ vì ngoài taxonomy. Người thật đi trên các xe này vẫn gán box `person` nếu đủ điều kiện. Chỉ hỏi reviewer khi chưa xác định được loại vật hoặc chưa áp dụng được quy tắc.

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
4. **Vật quá nhỏ / Đám đông (R-09, R-10):** Đo chiều cao box phần nhìn thấy theo pixel trên ảnh gốc. Cao dưới 20 px: không gán box lớp đối tượng, vẽ `ignore_region` sát vùng chứa vật mục tiêu nhỏ. Cao đúng 20 px trở lên: gán bình thường nếu nhận diện được. Đám đông không thể tách biên từng vật: dùng `ignore_region` sát cụm; không phủ vật đủ điều kiện có thể gán riêng. Đông hoặc đỗ sát nhau chưa đủ để bỏ qua cả cụm.
5. **Mơ hồ (R-11):** Nếu vẽ được box có căn cứ nhưng chưa chắc lớp, mức che hoặc hình học: chọn lớp có bằng chứng mạnh nhất, tick `can_review` và ghi lý do. Nếu chưa thể xác định vật hoặc vẽ box có căn cứ, không tạo box phỏng đoán; ghi `image,annotator,region,note` để nhóm thu vào `submission/qc/image_issues.csv` sau khi khóa nhãn độc lập. Không dùng `ignore_region` để né ca cần review.

## 4. Ca khó

| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| `img_002` | Xe lồng chở nhiều ô tô con trên thùng. | R-03, R-06 | Gán box `truck` cho phần nhìn thấy của xe vận chuyển và box `car` riêng cho từng ô tô đủ điều kiện; không mở rộng box truck để ôm các xe được chở. | Mỗi xe thật là một vật độc lập, kể cả khi đang đỗ trên sàn xe khác. |
| `img_005` | Người ngồi trên xe máy che khuất nhau. | R-04, R-05 | **Tách box người và box xe máy riêng**. | Cấu trúc taxonomy yêu cầu tách riêng người và phương tiện. |
| `img_004`, `img_009` | Kiểm tra phương tiện ngoài taxonomy hoặc khó nhận dạng. | R-01, R-11 | Xe xác định là tuk-tuk/ba bánh hoặc xe buýt cỡ lớn thì không gán lớp xe; người thật vẫn gán riêng nếu đủ điều kiện. Chỉ hỏi reviewer khi chưa nhận dạng được phương tiện. | Không ép vật ngoài taxonomy vào lớp gần giống. |
| `img_035`, `img_037` | Cần kiểm cấu trúc xe trước khi phân lớp. | R-01, R-11 | Cabin và thùng hàng riêng: `truck`; thân liền kiểu van: `van`. Nếu phần nhìn thấy chưa đủ phân biệt, bật `can_review` hoặc ghi vấn đề cấp ảnh. | Không mặc định các xe trong ảnh đều là bán tải độ nắp cao. |
| `img_017`, `020`, `033`, `038` | Tượng, hình phản chiếu, ma-nơ-canh, đồ chơi. | R-07 | **Tuyệt đối không gán nhãn.** | Chỉ gán người/phương tiện thật hiện diện trực tiếp. |
| Nhiều ảnh | Vật mục tiêu có chiều cao phần nhìn thấy dưới 20 px, hoặc cụm không thể tách biên từng vật. | R-09, R-10 | Vẽ `ignore_region` sát vùng cần bỏ qua; vẫn gán riêng vật đủ điều kiện có biên phân biệt được. | Phân biệt vật quá nhỏ với cụm không thể tách, tránh bỏ sót vật gán được. |

## 5. QC và phiên bản

### Ngưỡng và Tiêu chí QC
- Giữ nguyên mục `rules` trong `submission/taxonomy.json`.
- Chiều cao box phần nhìn thấy hợp lệ để gán lớp đối tượng: ≥ 20 px trên ảnh gốc.
- Đủ 100% lượt gán theo `assignment.csv` trước khi đo IAA.
- Ghép box đánh giá IAA: ghép 1–1, không phân biệt lớp, IoU ghép ≥ 0,5 theo script lab.
- IAA trước calibration: IoU trung bình ≥ 0,85 và đồng thuận lớp ≥ 95%, chỉ trên cặp box ghép được. Không có cặp thì ghi N/A; box chưa ghép và bất đồng vùng bỏ qua được ghi riêng.
- Ngưỡng IoU box: chiều cao ≥ 50 px dùng 0,85; dưới 50 px dùng 0,7. Phân biệt các ngưỡng này với ngưỡng ghép 0,5 và chỉ số IAA trung bình.
- QC bàn giao phải duyệt toàn bộ 40 ảnh. Tỉ lệ ảnh lỗi = ảnh có ít nhất một lỗi / ảnh đã duyệt, yêu cầu ≤ 2%; 1/40 = 2,5%, vượt ngưỡng. Báo thêm annotation lỗi / annotation đã duyệt, luôn kèm tử số và mẫu số.
- Lỗi lặp vẫn yêu cầu Rework. Phạm vi sửa theo nguyên nhân và quyết định calibration; thay đổi ý nghĩa quy tắc cần rà lại cả lô, lỗi cục bộ không mặc định phải sửa toàn lô.

### Quy trình và Người quyết định
- Chuẩn bị: cập nhật taxonomy khớp v1.0, giữ nguyên `rules`, chạy `python scripts/make_cvat_labels.py`, rồi khóa guideline và taxonomy trước khi gán độc lập.
- Annotator: tự vẽ box trong CVAT theo v1.0, không xem nhãn của người khác; xuất COCO 1.0 vào `submission/labels/<mã>.json`. Chạy `python scripts/validate_labels.py submission/labels/102.json` cho thành viên 102; thành viên 101 thay bằng `101.json`. Khi cả hai khóa nhãn mới tập hợp và chạy `python scripts/validate_labels.py --all`.
- Người điều phối: Bùi Thảo Linh (101). Reviewer bản cuối: Vũ Văn Đăng (102). Nhóm chốt ca mơ hồ qua calibration, có người chốt, quy tắc, phạm vi sửa và kết quả kiểm lại. Nhóm hai người đổi vai kiểm tra chéo để người kiểm khác người sửa và ghi rõ.
- Lưu IAA trước calibration. Sau calibration lưu `guideline_v1.1_calibrated.md` và `guideline_change.json`; thay đổi ngữ nghĩa phải có quyết định phạm vi lô và rà lại cả lô.
- Chạy `qc_sample.py` và `qc_report.py` trong `scripts/` theo từng vòng; giữa hai lệnh duyệt toàn lô và điền phiếu QC. Giữ các vòng cũ khi sửa và duyệt lại.
- **Hold:** thiếu bằng chứng, guideline/dữ liệu còn vấn đề, review lệch phiên bản hoặc chỉ lấy mẫu.
- **Rework:** tỉ lệ ảnh lỗi > 2% hoặc có lỗi lặp; ghi nguyên nhân và phạm vi sửa, kiểm lại ở vòng mới.
- **Release:** duyệt toàn lô, đủ bằng chứng đạt yêu cầu lab và reviewer xác nhận. Không suy ra Release chỉ từ kết quả unit test hoặc IAA cao.

| Phiên bản | Thay đổi | Lý do |
|---|---|---|
| v1.0 | Hợp nhất bản cá nhân 101 và 102; hoàn thiện trước khi khóa: xuất COCO, ngưỡng IAA/QC của lab, chiều cao vật nhỏ, xử lý vật ngoài taxonomy và ca mơ hồ. | Loại bỏ mâu thuẫn về tuk-tuk và quy trình YOLO/Gold Labels; làm rõ phạm vi review và Rework. |

## Phụ lục v1.1 — trạng thái calibration ngày 17/11/2026

Bản này bổ sung mô tả quy trình và trạng thái, không thay đổi định nghĩa lớp, ngưỡng, hình học box hoặc cách xử lý ngoài phạm vi của v1.0. Taxonomy vẫn v1.0, rules không đổi.

AI đã xem 35 ảnh chồng nhãn theo ủy quyền thành viên 102; 114 mục trên 13 ảnh có quyết định ứng viên. 971 mục trên 22 ảnh được quyết định trì hoãn sang QC, giữ nhãn 102 và cờ review; đây không phải 971 ca nội dung đã được xác nhận giải quyết.

Tên artifact labels_calibrated.json chỉ biểu thị đầu ra pipeline đang được duyệt. Bộ hiện tại là ứng viên có nguồn AI, chưa được phép ghi Release. Không loại cờ can_review nhằm làm báo cáo đạt. Người quyết định thực tế được ghi là AI trong calibration_decisions.csv; chưa có xác nhận hai người họp hoặc reviewer độc lập.

971 mục tồn đọng được giữ trong pending_calibration_cases.csv. Khi có quyết định nội dung mới, cập nhật nhật ký, chạy lại prepare_calibration.py và xuất bản sửa đúng tên hash; chạy build_final.py và mở vòng QC mới để giữ bằng chứng vòng cũ.
