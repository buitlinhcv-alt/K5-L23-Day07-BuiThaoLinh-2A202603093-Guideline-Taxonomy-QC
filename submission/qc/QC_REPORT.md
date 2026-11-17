# Báo cáo QC — RANDOM — vòng 1 — 17/11/2026

- Mục đích và giới hạn sử dụng: bộ nhãn ứng viên sau pipeline, có AI hỗ trợ; chỉ bàn giao Hold, chưa được xác nhận chất lượng nội dung.
- Nguồn và phép sử dụng dữ liệu: data/ATTRIBUTION.csv, data/README.md; giữ nguyên nguồn ảnh.
- Manifest và checksum nhãn cuối: submission/qc/batch_manifest.json; SHA-256 labels_calibrated.json: 3e0a9359c349923c6aa2375eb3dfe1727e761eafb2a11efb1fe5055b1865e73b. Tên nhãn cuối là artifact pipeline, không phải chứng nhận Release.
- Guideline và taxonomy khi gán / khi bàn giao: v1.0 khi gán; v1.1 thêm phụ lục trạng thái và provenance, không đổi nghĩa quy tắc; taxonomy v1.0, rules giữ nguyên.
- Lượt hoàn thành / được giao: 80/80 về cấu trúc. Nguồn AI của 102 được công khai; tính độc lập của 101 được người dùng 102 xác nhận ngày 17/11/2026, chưa kiểm chứng độc lập bởi AI.
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 260; 0,780958; 256/260 (98,46%). IoU chưa đạt 0,85. IAA giữ số đo trước calibration.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 50/199 box chưa ghép; 4 lượt bỏ qua do ignore; 59 ca vùng ignore.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: chưa nhận xác nhận đối chiếu ca tham chiếu coach.
- Ca đã xử lý, rule ID, bản sửa và người xác nhận: 114 mục có quyết định ứng viên AI trên 13 ảnh; 971 mục được ghi DEFER_TO_QC, chưa giải quyết nội dung. Xem calibration_decisions.csv, pending_calibration_cases.csv. Không coi điền đủ cột là đã sửa lỗi.
- Tác động v1.1 lên nhãn và ảnh: không đổi ngữ nghĩa. Bộ ứng viên giữ hình học 102, không tuyên bố sửa qua CVAT; 35 ảnh calibration dùng file theo hash và 5 ảnh không bị cờ dùng consensus của script. Xem ai_calibration_20261117/pipeline_disclosure.json.
- Tham chiếu bằng chứng khi Rework: chưa có vòng sửa sau review. Nhãn gốc 101/102 giữ nguyên sau lần chuẩn hóa có lưu bản gốc; các artifact trước sửa lỗi làm tròn được lưu ai_calibration_20261117/.
- Vòng QC: 3
- Người review và ngày: Codex chạy pipeline theo ủy quyền 102 ngày 17/11/2026; chưa có phiếu người học review độc lập. Không ghi giả mã reviewer.
- Đã duyệt / cả lô: lượt rà soát AI hiện hành vòng 3 có kết luận 15/40 ảnh; 25/40 chưa chốt. Chưa có review người học độc lập.
- Ảnh lỗi / ảnh đã duyệt: vòng 3 ghi 0/15; không suy ra 0/40. Cờ review và 25 ảnh chưa chốt vẫn dẫn tới Hold.
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: vòng 3 ghi 0/60 trên phần AI đã kết luận; không áp dụng cho toàn bộ 488 annotation.
- Vật bỏ sót, ảnh và vùng: 219 mục bỏ sót/thừa còn cần đối chiếu; chưa phải số vật bỏ sót đã xác nhận.
- Ca khó / phần còn lại, lỗi lặp và ví dụ: 35 ảnh có cờ; ưu tiên img_021, img_019, img_032. 971 mục chưa đủ bằng chứng để kết luận.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: ảnh Copy ngoài phân công và sai số làm tròn 0,01 px bị máy chặn; biên vật che khuất và bỏ sót người cần review ảnh.
- Quyết định: Hold
- Lý do, người nhận và phạm vi sử dụng: đủ artifact để coach xem hồ sơ; còn cờ review, phiếu cuối trống và calibration trì hoãn. Không tuyên bố đạt Release hoặc gán độc lập hoàn toàn.
- Nếu còn việc: người học kiểm 40 ảnh và điền phiếu thật, xử lý 971 ca; ghi provenance và tính độc lập; khi sửa nhãn mở vòng mới để giữ bằng chứng vòng 1. Ghi chú tự phản tư cần thành viên tự xác nhận.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: C001 -> calibration_decisions.csv -> img_001 -> R-03/R-05 -> labels_calibrated.json, reviews/round-01/labels_snapshot.json. Mục quyết định là AI; QC cuối vẫn chờ.

`check_submission.py` kiểm tính đầy đủ và đồng bộ artifact. Kết quả 0 lỗi với quyết định Hold không chứng minh nhãn đúng hoặc hoàn tất review nội dung. Không có dòng ảnh sạch được tạo thay người học.

## Cập nhật lượt rà soát AI và vòng 2

Đã xem 40/40 ảnh có box trong lượt rà soát AI; 9/40 ảnh có kết luận hẹp không thấy lỗi xác nhận ở lượt này; 31/40 còn cần rà sâu, không ghi sạch. Đây không phải 40 ảnh đạt QC cuối. 3 phát hiện trên 3 ảnh được ghi vòng 1 và sửa trong QAI01–QAI03: thêm person img_002, mở rộng box người lái img_004, thêm motorcycle img_010. Tọa độ là kết quả rà soát AI, cần kiểm cuối.
Vòng 1 giữ snapshot nhãn cũ; phiếu vòng 1 có 3 ảnh lỗi và 8 ảnh không thấy lỗi, 29 ảnh chưa chốt. Vì còn can_review/guideline_gap, trạng thái vẫn Hold.
Vòng 2 dùng nhãn mới: 488 annotation; phiếu có 9 ảnh không thấy lỗi và 31 ảnh chưa chốt. Hai ảnh vừa thêm đối tượng vẫn để chờ kiểm độc lập. Tỉ lệ lỗi 0/9 chỉ trên phần được điền phiếu, không phải 0/40 và không chứng minh cả lô sạch. Còn 971 mục trì hoãn, chưa xác nhận tính độc lập hoặc phản tư cá nhân. Xem reviews/round-02/qc_report_auto.md và ai_calibration_20261117/image_review_40.csv.

## Cập nhật vòng 3 — hiện hành

Thành viên 102 xác nhận ngày 17/11/2026 rằng 101 được Linh tự gán độc lập, chưa xem/dùng 102 trước khi hoàn tất. Đây là xác nhận từ người dùng, không phải kiểm chứng độc lập của AI. Nguồn AI của 102 vẫn được công khai.
Đã sửa thêm 5 annotation: 4 mức che khuất ở img_001 và lớp pickup đỏ img_002. Chốt thêm 72 mục từng trì hoãn; còn 899 mục. 15/40 ảnh có kết luận AI ở vòng 3; 25/40 chưa chốt nội dung. Đã bỏ cờ review ở những ảnh AI vừa kết luận, giữ cờ ở phần còn lại. Quyết định vẫn Hold; không diễn giải 0/15 lỗi đã ghi thành 0/40 lỗi.
