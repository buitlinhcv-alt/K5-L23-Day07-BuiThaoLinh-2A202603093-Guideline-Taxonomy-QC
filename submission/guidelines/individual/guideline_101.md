# Guideline gán nhãn — V0 — 101

> Copy file này (hoặc `guideline_v0_starter.md`) rồi viết. Một guideline đủ gồm **5 phần**. Câu nào của v0
> làm hai người đọc ra hai cách khác nhau thì phải viết lại cho một cách hiểu duy nhất.

- Phiên bản: v0
- Ngày: 16/11/2026
- Người viết / duyệt: Bui Thao Linh
- Loại nhãn: bounding box (hộp chữ nhật) trên ảnh đường phố
- Công cụ: CVAT, xuất COCO 1.0

## 1. Mục tiêu
Guideline này quy định cách gán nhãn phương tiện giao thông trong ảnh đường phố, phục vụ huấn luyện mô hình phát hiện đối tượng YOLO.
Mục tiêu là đảm bảo các annotator gán nhãn thống nhất, hạn chế nhầm lẫn giữa các loại phương tiện và nâng cao chất lượng dữ liệu huấn luyện.
Các lỗi cần tránh:
- Gán nhầm loại phương tiện.
- Bỏ sót phương tiện đủ điều kiện gán nhãn.
- Vẽ bounding box không sát đối tượng.
- Gán nhãn các vật thể không thuộc phạm vi dữ liệu.

## 2. Lớp và thuộc tính
### Lớp
| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô con, xe hơi cá nhân chở người, kích thước phổ thông. | Xe 4-7 chỗ, sedan, SUV, hatchback. | Dễ nhầm với van; phân biệt nhờ dáng xe car thấp và chủ yếu chở người. |
| `truck` | Xe chuyên chở hàng hóa, thường có thùng hàng phía sau hoặc đầu kéo kéo theo rơ-moóc. | Xe tải thùng, xe bồn, xe đầu kéo, xe lồng chở xe. | Xe bán tải độ nắp thùng cao (mơ hồ với van), xe van kín thùng (nhỏ hơn). |
| `van` | Xe thùng kín cỡ nhỏ, xe đa dụng có khoang thân xe dạng hộp kín, thường có cửa trượt. | Delivery van, minivan, xe van chở hàng. | Dễ nhầm với bus (bus lớn hơn) và truck (truck tách biệt đầu/thùng rõ hơn). |
| `motorcycle` | Xe máy, mô tô. | Xe tay ga, xe số, mô tô phân khối lớn. | Xe đạp, người đi bộ. |
| `person` | Người (người đi bộ hoặc đang điều khiển phương tiện). | Người đi bộ qua đường, người lái xe máy. | Tượng, ma-nơ-canh, hình in. |
| `ignore_region` | Vùng không cần gán từng vật (vùng quá đông đúc, xa, hoặc mơ hồ). | Đám đông người đi bộ ở xa, khu vực đỗ xe dày đặc không rõ từng xe. | Các phương tiện/người có thể nhìn rõ và tách biệt từng thực thể. |

Vật **không thuộc các lớp trên** (như xe đạp, xe buýt, biển báo, đèn giao thông, biển quảng cáo, ...): **Không gán nhãn**. Không tạo bounding box hoặc class ID cho các đối tượng này[cite: 2, 4].

### Thuộc tính
| Thuộc tính | Giá trị | Ý nghĩa |
|---|---|---|
| `occluded_level` | `0`, `1`, `2`, `3` | Mức bị che: 0 là thấy rõ, 1 là bị che ít, 2 là bị che nhiều, 3 là không rõ. |
| `truncated` | `no`, `yes` | Vật bị cắt ở mép ảnh. |
| `can_review` | tick / không | Tick khi bạn không chắc chắn, để người khác xem lại. |
## 3. Quy tắc vẽ box
- **Nguyên tắc chung:**
  - Bounding box phải ôm sát phần thân phương tiện nhìn thấy được.
  - Không lấy thêm vùng nền xung quanh phương tiện.
  - Không gộp nhiều phương tiện riêng biệt vào một bounding box. Mỗi phương tiện hợp lệ tương ứng với một bounding box.
  - Không tự suy đoán phần bị che khuất hoặc nằm ngoài khung hình.
- **Phương tiện bị che khuất:** Chỉ vẽ box bao quanh phần phương tiện nhìn thấy được.
- **Phương tiện bị cắt bởi mép ảnh:** Vẽ box đến sát mép ảnh, không mở rộng ra ngoài ảnh.
- **Phương tiện xuất hiện dưới dạng hình in, tranh ảnh, phản chiếu:** Không gán nhãn.
- **Phương tiện là đồ chơi, mô hình, tượng hoặc mannequin:** Không gán nhãn.
- **Một xe chở nhiều xe khác trên thùng:** Gán nhãn xe vận chuyển và từng xe được nhìn thấy rõ nếu đủ điều kiện.
- **Phương tiện có kích thước quá nhỏ (phần nhìn thấy dưới 20 pixel):** Ghi nhận mã ảnh và chuyển Reviewer xem xét.
## 4. Ca khó
| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| Các ảnh chỉ có xe đạp, xe buýt, biển báo, đèn giao thông, biển quảng cáo, ... | Đối tượng không thuộc 5 lớp | R_IGNORE | **Không gán nhãn** | Không thuộc phạm vi dataset. |
| img_002 | Xe chở nhiều ô tô trên thùng | R_MULTI | **Gán nhãn xe tải và các ô tô nhìn thấy rõ** | Mỗi phương tiện là một đối tượng độc lập. |
| img_011, 017, 020, 027, 033, 038 | Đồ chơi, phản chiếu, tượng, mannequin | R_FAKE | **Không gán nhãn** | Không phải phương tiện thật đang tham gia giao thông. |
| Các ảnh có phương tiện rất nhỏ | Phần phương tiện nhìn thấy dưới 20 pixel | R_TINY | **Chuyển Reviewer xem xét** | Khó xác định chính xác loại phương tiện. |
| img_004, 009 | Xe ba bánh, tuk-tuk | R_EDGE | **Chuyển Reviewer xem xét** | Chưa có lớp tương ứng trong bộ nhãn hiện tại. |
| img_035, 037 | Xe bán tải có nắp thùng cao hoặc cải tiến | R_EDGE | **Chuyển Reviewer xem xét** | Có thể nhầm giữa car và truck. |
## 5. QC và phiên bản
**Tiêu chí kiểm tra chất lượng:**
- F1-score ≥ 0.80
- Mean IoU ≥ 0.70
(Các tiêu chí được đối chiếu với bộ nhãn chuẩn - Gold Labels).

**Quy trình QC:**
1. Annotator tự kiểm tra lại bounding box sau khi gán nhãn.
2. Kiểm tra định dạng file YOLO và class ID.
3. Sử dụng script check_yolo_labels.py để phát hiện lỗi định dạng và tọa độ.
4. Các trường hợp mơ hồ được chuyển Reviewer / Giảng viên xem xét.
5. Những lỗi được phát hiện phải được sửa trước khi hoàn thành bộ dữ liệu.

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| | | |
