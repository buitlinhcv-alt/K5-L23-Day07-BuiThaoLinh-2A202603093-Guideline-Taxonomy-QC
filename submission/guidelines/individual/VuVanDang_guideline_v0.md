# Guideline gán nhãn — v0 — Vũ Văn Đăng

- Phiên bản: v0 cá nhân — bản nháp đề xuất, chưa khóa để gán nhãn.
- Ngày: 16/11/2026.
- Người viết / duyệt: Vũ Văn Đăng / chưa duyệt.
- Mã thành viên: `102` — nhóm RANDOM.
- Loại nhãn: bounding box trên ảnh đường phố; công cụ CVAT, xuất COCO 1.0.
- Căn cứ: `guideline/TEMPLATE_guideline.md`, bản starter, `GUIDE.md`, `RULES.md`, `submission/taxonomy.json` và `docs/QC_CHEATSHEET.md`.
- Hỗ trợ soạn: AI đã đọc tài liệu và xem ảnh tổng quan của 40 ảnh. Người học cần mở ảnh gốc, kiểm tra các đề xuất và tự quyết định trước khi nộp; đây chưa phải bằng chứng đã hoàn thành Pha 1 độc lập.

## 1. Mục tiêu

Tạo nhãn để huấn luyện mô hình phát hiện người và phương tiện thật trong ảnh đường phố 2D, phục vụ phân tích giao thông. Bỏ sót người, nhầm tượng thành người hoặc gộp người với xe có thể làm mô hình học sai đối tượng và vị trí. Chỉ ghi những gì có bằng chứng trong ảnh; không suy đoán vật bị che hoàn toàn.

Phạm vi là 40 ảnh `data/images/img_001.jpg` đến `img_040.jpg`. Các quyết định dưới đây là đề xuất cá nhân để thảo luận ở Pha 2, chưa phải đặc tả đã được nhóm thông qua.

## 2. Lớp và thuộc tính

### Lớp

Mỗi đối tượng chỉ nhận một lớp; không thêm lớp ngoài taxonomy hiện có.

| Lớp | Định nghĩa (một câu) | Ví dụ | Phản ví dụ / dễ nhầm với |
|---|---|---|---|
| `car` | Ô tô chở người dạng sedan, hatchback, SUV hoặc MPV có thân liền, không có thùng hàng tách riêng. | Ô tô con trong `img_023`. | Pickup có thùng hàng riêng là `truck`; van chở hàng là `van`. |
| `truck` | Xe chở hàng có cabin và thùng hoặc sàn chở hàng phân biệt được, gồm pickup và xe chở xe. | Xe chở xe `img_002`, xe tải nhỏ `img_036`. | Xe thùng kín thân liền kiểu van. |
| `van` | Xe thân liền, khoang sau dạng hộp cao phục vụ chở hàng hoặc chở người kiểu minivan/minibus. | Xe van `img_037`. | SUV là `car`; xe tải thùng với cabin tách biệt là `truck`; xe buýt cỡ lớn ngoài phạm vi. |
| `motorcycle` | Xe hai bánh có động cơ, gồm xe máy số, tay ga và mô tô, đang chạy hoặc đỗ. | Xe máy trong `img_005`, `img_030`. | Xe đạp, xích lô và tuk-tuk ba bánh không thuộc lớp này. |
| `person` | Người thật hiện diện trực tiếp trong cảnh, đi bộ, đứng, ngồi hoặc đi trên phương tiện. | Người đi xe máy `img_005`, người qua đường `img_034`. | Tượng, ma-nơ-canh, hình in và người chỉ xuất hiện qua phản chiếu. |
| `ignore_region` | Vùng bỏ qua khi có đối tượng mục tiêu quá nhỏ hoặc không thể tách từng vật theo các quy tắc bên dưới. | Cụm người rất nhỏ ở xa sau khi đo trên ảnh gốc. | Không dùng để che lỗi gán, vật ngoài taxonomy hoặc ca có thể gán nhưng chưa chắc lớp. |

**R-01 — Phân lớp và ngoài phạm vi:** xét hình dạng/cấu trúc nhìn thấy, không dựa riêng vào màu, kích thước biểu kiến hay việc đang chở gì. Xe đạp, xích lô, tuk-tuk và xe buýt cỡ lớn không gán lớp xe; người thật đi trên chúng vẫn gán riêng. Không tạo `ignore_region` chỉ vì một vật ngoài taxonomy. Xe độ hoặc xe trưng bày vẫn có thể là phương tiện thật nếu ảnh cho thấy cấu trúc xe; khi chưa đủ bằng chứng, đánh dấu xem lại. Không gán ô tô đồ chơi, mô hình hay tác phẩm điêu khắc.

### Thuộc tính

Các ngưỡng che khuất sau đây là đề xuất cá nhân cần được nhóm chốt. Tỉ lệ che là ước lượng phần diện tích hình chiếu của vật bị vật khác che; không cộng phần nằm ngoài khung ảnh vào tỉ lệ này.

| Thuộc tính | Giá trị cố định | Ý nghĩa / ngưỡng |
|---|---|---|
| `occluded_level` | `0` | Không bị vật khác che (0%). |
| `occluded_level` | `1` | Bị che trên 0% đến hết 30%. |
| `occluded_level` | `2` | Bị che trên 30% đến hết 70%. |
| `occluded_level` | `3` | Bị che trên 70%, hoặc biết có che nhưng không đủ bằng chứng để ước lượng mức; trường hợp sau phải bật `can_review`. |
| `truncated` | `no` | Không có bằng chứng vật bị cắt bởi mép ảnh. |
| `truncated` | `yes` | Một phần vật nằm ngoài khung và đường bao bị cắt ở ít nhất một mép ảnh. Chạm mép nhưng vẫn thấy toàn bộ vật chưa đủ để chọn `yes`. |
| `can_review` | không tick / `false` | Quy tắc và bằng chứng đủ để quyết định. |
| `can_review` | tick / `true` | Cần kiểm lại lớp, hình học, mức che hoặc cách áp dụng quy tắc; ghi kèm ảnh và lý do. |

**R-02 — Thuộc tính:** gán đầy đủ thuộc tính cho từng box. Với `ignore_region`, đề xuất quy ước `occluded_level=0`, `truncated=no`, `can_review=false`; đây là giá trị quy ước của vùng, không mô tả từng vật bên trong. Nếu chính phạm vi vùng còn mơ hồ thì bật `can_review` và ghi lý do.

## 3. Quy tắc vẽ box

1. **R-03 — Phần nhìn thấy:** dùng box thẳng trục ôm sát hợp của các phần nhìn thấy thuộc cùng một vật. Bao gồm đầu, tay chân, mũ bảo hiểm đang đội của người; bánh xe, gương và phần thân gắn liền của xe. Không bao bóng đổ, khói, hành lý rời hoặc hàng hóa nhô ra. Không kéo box đến vị trí phần khuất được suy đoán. Box bao hai phần rời có thể chứa vùng che ở giữa, nhưng không gán vùng che thành một đối tượng mới.
2. **R-04 — Che và cắt mép:** vật bị che vẫn có box nếu nhận diện được và đủ ngưỡng kích thước. Chỉ bao phần nhìn thấy; chọn mức che theo R-02. Vật bị cắt mép có box giới hạn trong khung, `truncated=yes`. Vật vừa bị che vừa bị cắt mép có cả hai thuộc tính tương ứng. Vật khuất hoàn toàn không có box.
3. **R-05 — Người và xe:** mỗi người thật một box `person`, mỗi xe một box theo lớp xe; không tạo một box chung cho người và xe. Hai người trên một xe là hai box người và một box xe nếu từng vật đủ điều kiện. Không vẽ box người chỉ vì thấy bóng tối trong cabin mà chưa nhận ra người.
4. **R-06 — Xe chở xe:** xe vận chuyển và từng xe được chở là các vật riêng, gán theo lớp từng vật. Box xe chở hàng không mở rộng để ôm toàn bộ các xe được chở. Nếu phần rơ-moóc tách rời không thuộc định nghĩa lớp, ghi vấn đề để nhóm chốt thay vì tự thêm lớp.
5. **R-07 — Tượng, mô hình, hình in, phản chiếu:** không gán đối tượng chỉ xuất hiện trong tranh, ảnh, biển quảng cáo, gương hoặc bề mặt phản chiếu; không gán tượng, ma-nơ-canh, mô hình, đồ chơi. Nếu đồng thời thấy vật thật và ảnh phản chiếu, chỉ gán vật thật một lần. Không tạo vùng bỏ qua cho các ca đã loại rõ theo quy tắc.
6. **R-08 — Xe chủ (ego):** bỏ qua phần xe đang mang camera, như nắp ca-pô hoặc gương của chính xe đó. Nếu chỉ nghi ngờ đó là ego mà chưa xác định được thì ghi vấn đề cấp ảnh để reviewer chốt.
7. **R-09 — Vật nhỏ:** đo chiều cao box phần nhìn thấy trên ảnh gốc, không đo trên thumbnail hay ảnh đã zoom. Cao dưới 20 px: không gán box lớp đối tượng, tạo `ignore_region` sát vùng có vật nhỏ. Cao đúng 20 px trở lên: gán bình thường nếu nhận diện được. Không suy luận kích thước vật bị che để vượt ngưỡng.
8. **R-10 — Đám đông:** tách box từng vật đủ 20 px khi còn phân biệt được. Chỉ dùng `ignore_region` cho cụm không thể tách biên từng vật; khoanh sát cụm, không phủ cả ảnh hoặc lấn sang vật gán rõ được. Nếu không tránh được chồng lấn với box hợp lệ, bật cờ và đưa vào calibration. Nhiều xe đỗ sát nhau chưa phải lý do tự động bỏ qua cả cụm.
9. **R-11 — Mơ hồ:** có thể vẽ box nhưng chưa chắc thì chọn lớp có bằng chứng mạnh nhất, bật `can_review` và ghi vấn đề. Nếu chưa xác định được vật hay chưa thể vẽ box có căn cứ, không tạo box phỏng đoán; ghi `image,annotator,region,note` để nhóm thu vào `submission/qc/image_issues.csv` sau khi khóa nhãn độc lập. Không dùng `ignore_region` để né ca cần thảo luận.

## 4. Ca khó

Các ví dụ được nhận diện trên ảnh tổng quan; quyết định là đề xuất để kiểm tra lại trên ảnh gốc, chưa phải nhật ký đã gán nhãn. Chưa đưa ra số box hay số đo pixel khi chưa kiểm trực tiếp.

| Ảnh | Tình huống | Rule ID | Quyết định | Lý do |
|---|---|---|---|---|
| `img_002` | Xe tải chở nhiều ô tô. | R-03, R-06 | Tách xe vận chuyển và các ô tô thật được chở, kiểm phần nhìn thấy từng xe. | Không coi tất cả xe trên sàn là một vật. |
| `img_004` | Tuk-tuk ba bánh. | R-01, R-05 | Không gán tuk-tuk vào `car` hoặc `motorcycle`; gán người thật đủ điều kiện nếu có. | Taxonomy đề xuất chưa có lớp xe ba bánh. |
| `img_005` | Người ngồi trên xe máy, nhiều phần che nhau. | R-04, R-05 | Tách người và xe; chọn mức che riêng cho từng đối tượng. | Người và phương tiện là hai loại vật khác nhau. |
| `img_015` | Xe bị cắt ở mép trái và phải ảnh. | R-04 | Box chỉ bao phần trong ảnh, bật `truncated` khi thấy đường bao bị cắt. | Không suy đoán phần xe ngoài khung. |
| `img_017` | Tượng hình người bằng kim loại. | R-07 | Không gán `person` cho tượng. | Hình dáng người chưa đủ để xác định là người thật. |
| `img_019` | Xe vàng kiểu giả tưởng trong đám đông. | R-01, R-11 | Kiểm ảnh gốc để phân biệt xe thật được độ và mô hình; nếu chưa rõ, ghi vấn đề chờ reviewer. | Kiểu dáng lạ không tự chứng minh là đồ chơi hay xe thật. |
| `img_020` | Cảnh phản chiếu trong gương chiếu hậu. | R-07, R-08 | Không gán đối tượng trong gương; bỏ phần xe chủ nếu xác nhận được. | Phản chiếu không phải vật hiện diện trực tiếp ở vị trí đó. |
| `img_021` | Dòng xe máy đông, nhiều người xa. | R-05, R-09, R-10 | Tách vật nhận diện được; đo ảnh gốc trước khi dùng vùng bỏ qua. | Không bỏ toàn cảnh chỉ vì đông xe. |
| `img_027` | Hình xe pickup đặt trên bệ trưng bày. | R-07, R-11 | Kiểm ảnh gốc: nếu là tác phẩm/mô hình thì loại; nếu chưa phân biệt được với xe thật, ghi vấn đề. | Bệ trưng bày là dấu hiệu cần kiểm, không đủ để tự kết luận. |
| `img_030` | Nhiều xe máy đỗ sát nhau. | R-03, R-10 | Gán riêng xe có biên phân biệt được, dù không có người lái. | Xe đỗ vẫn thuộc phạm vi phát hiện. |
| `img_033` | Ma-nơ-canh sau cửa kính. | R-07 | Không gán `person` cho ma-nơ-canh. | Không phải người thật. |
| `img_038` | Bộ sưu tập ô tô đồ chơi trong tủ. | R-07 | Không gán `car` cho các mô hình. | Đồ chơi ngoài phạm vi. |

Trước khi nộp Pha 1, người viết phải chọn ít nhất ba ca khó từ ảnh gốc, kiểm tra lại và ghi quyết định thực sự của mình trong ghi chú cá nhân. Nếu chưa chốt, giữ trạng thái cần review, không ghi như đã có đồng thuận.

## 5. QC và phiên bản

### Ngưỡng áp dụng

Giữ nguyên mục `rules` trong `submission/taxonomy.json`.

| Nội dung | Ngưỡng / cách áp dụng |
|---|---|
| Chiều cao tối thiểu | 20 px; nhỏ hơn dùng R-09. |
| Ghép box để đo IAA | Ghép 1–1 không phân biệt lớp, IoU ≥ 0,5 theo script của lab. |
| IoU box lớn / nhỏ | Chiều cao ≥ 50 px: ≥ 0,85; dưới 50 px: ≥ 0,7. |
| IAA trước calibration | IoU trung bình ≥ 0,85 và đồng thuận lớp ≥ 95%, chỉ trên cặp ghép được; không có cặp ghi N/A. |
| Độ phủ | Đủ 100% lượt gán theo phân công trước khi đo IAA. |
| Lỗi theo ảnh | Số ảnh có ít nhất một lỗi / số ảnh đã duyệt ≤ 2%; báo kèm tử số và mẫu số. |
| Lỗi theo đối tượng | Báo số annotation lỗi / số annotation đã duyệt. |
| Phạm vi duyệt | Toàn bộ lô; với 40 ảnh, 1 ảnh lỗi là 2,5%, đã vượt ngưỡng. |

Ghi riêng box không ghép được và ca bất đồng vùng bỏ qua; IAA cao không chứng minh cả hai người đều đúng. Reviewer kiểm năm nhóm lỗi: bỏ sót, thừa, sai lớp, sai vị trí/kích thước và sai thuộc tính.

### Người chốt và quyết định bàn giao

- Người điều phối: Bùi Thảo Linh (`101`); người review bản cuối: Vũ Văn Đăng (`102`), theo `TEAM.md`.
- Pha 2: cả nhóm chốt các đề xuất và lưu v1.0, cập nhật taxonomy tương ứng rồi khóa đặc tả. Chỉ bắt đầu gán độc lập theo bản đã khóa.
- Pha 3: tự vẽ box, giữ độc lập, chưa xem nhãn của người khác. AI không quyết định nhãn hoặc pre-label thay người học.
- Pha 5: ghi từng ca vào bảng calibration, có mã quy tắc, người chốt, phạm vi sửa và người kiểm lại. Người kiểm nên khác người sửa; nhóm hai người thì đổi vai.
- **Hold:** thiếu bằng chứng, guideline chưa chốt, dữ liệu có vấn đề hoặc chỉ duyệt mẫu.
- **Rework:** trên 2% ảnh lỗi hoặc có lỗi lặp theo quy tắc lab; sửa và duyệt vòng mới, giữ vòng cũ.
- **Release:** duyệt toàn lô, đủ bằng chứng và đạt ngưỡng, reviewer xác nhận.
- Hiện chỉ có bản nháp guideline, chưa có nhãn hay QC để kết luận chất lượng lô. Không điền số đo, kết quả hoặc quyết định bàn giao giả định.

### Lịch sử phiên bản

| Phiên bản | Thay đổi | Lý do (ca nào) |
|---|---|---|
| v0 cá nhân — nháp | Đề xuất định nghĩa lớp, ngưỡng che, quy tắc box và bảng ca khó theo mẫu 5 phần. | Bản starter chưa giải quyết người đi xe máy, xe chở xe, phản chiếu, tượng và đồ chơi. |

Giữ nguyên bản cá nhân sau khi nộp Pha 1. Quyết định nhóm lưu thành `guideline_v1.0_agreed.md`; sau calibration lưu `guideline_v1.1_calibrated.md` và `guideline_change.json`. Thay đổi ý nghĩa quy tắc cần rà lại cả lô theo quy trình lab.

Tên file hiện tại làm theo yêu cầu cá nhân. Khi đóng gói nộp, dùng mã ba chữ số theo `SUBMISSION.md` và tên `guideline_<mã>.md`; rà lại thông tin cá nhân trong gói nộp theo hướng dẫn lớp.
