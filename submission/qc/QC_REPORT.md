# Báo cáo QC — RANDOM — vòng 1 — 17/11/2026

- Mục đích và giới hạn sử dụng: bộ nhãn ứng viên sau pipeline, có AI hỗ trợ; chỉ bàn giao Hold, chưa được xác nhận chất lượng nội dung.
- Nguồn và phép sử dụng dữ liệu: data/ATTRIBUTION.csv, data/README.md; giữ nguyên nguồn ảnh.
- Manifest và checksum nhãn cuối: submission/qc/batch_manifest.json; SHA-256 labels_calibrated.json: 3e0a9359c349923c6aa2375eb3dfe1727e761eafb2a11efb1fe5055b1865e73b. Tên nhãn cuối là artifact pipeline, không phải chứng nhận Release.
- Guideline và taxonomy khi gán / khi bàn giao: v1.0 khi gán; v1.1 thêm phụ lục trạng thái và provenance, không đổi nghĩa quy tắc; taxonomy v1.0, rules giữ nguyên.
- Lượt hoàn thành / được giao: 80/80 về cấu trúc. Nguồn AI của 102 được công khai; tính độc lập của hai người chưa được xác nhận.
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 260; 0,780958; 256/260 (98,46%). IoU chưa đạt 0,85. IAA giữ số đo trước calibration.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 50/199 box chưa ghép; 4 lượt bỏ qua do ignore; 59 ca vùng ignore.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: chưa nhận xác nhận đối chiếu ca tham chiếu coach.
- Ca đã xử lý, rule ID, bản sửa và người xác nhận: 114 mục có quyết định ứng viên AI trên 13 ảnh; 971 mục được ghi DEFER_TO_QC, chưa giải quyết nội dung. Xem calibration_decisions.csv, pending_calibration_cases.csv. Không coi điền đủ cột là đã sửa lỗi.
- Tác động v1.1 lên nhãn và ảnh: không đổi ngữ nghĩa. Bộ ứng viên giữ hình học 102, không tuyên bố sửa qua CVAT; 35 ảnh calibration dùng file theo hash và 5 ảnh không bị cờ dùng consensus của script. Xem ai_calibration_20261117/pipeline_disclosure.json.
- Tham chiếu bằng chứng khi Rework: chưa có vòng sửa sau review. Nhãn gốc 101/102 giữ nguyên sau lần chuẩn hóa có lưu bản gốc; các artifact trước sửa lỗi làm tròn được lưu ai_calibration_20261117/.
- Vòng QC: 1
- Người review và ngày: Codex chạy pipeline theo ủy quyền 102 ngày 17/11/2026; chưa có phiếu người học review độc lập. Không ghi giả mã reviewer.
- Đã duyệt / cả lô: 0/40 có phiếu review cuối; đã mở vòng full 40/40 ảnh, snapshot và phiếu trống trong reviews/round-01/.
- Ảnh lỗi / ảnh đã duyệt: N/A, mẫu số 0. Không phải 0% lỗi.
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: N/A, chưa có mẫu số review nội dung.
- Vật bỏ sót, ảnh và vùng: 219 mục bỏ sót/thừa còn cần đối chiếu; chưa phải số vật bỏ sót đã xác nhận.
- Ca khó / phần còn lại, lỗi lặp và ví dụ: 35 ảnh có cờ; ưu tiên img_021, img_019, img_032. 971 mục chưa đủ bằng chứng để kết luận.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: ảnh Copy ngoài phân công và sai số làm tròn 0,01 px bị máy chặn; biên vật che khuất và bỏ sót người cần review ảnh.
- Quyết định: Hold
- Lý do, người nhận và phạm vi sử dụng: đủ artifact để coach xem hồ sơ; còn cờ review, phiếu cuối trống và calibration trì hoãn. Không tuyên bố đạt Release hoặc gán độc lập hoàn toàn.
- Nếu còn việc: người học kiểm 40 ảnh và điền phiếu thật, xử lý 971 ca; ghi provenance và tính độc lập; khi sửa nhãn mở vòng mới để giữ bằng chứng vòng 1. Ghi chú tự phản tư cần thành viên tự xác nhận.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: C001 -> calibration_decisions.csv -> img_001 -> R-03/R-05 -> labels_calibrated.json, reviews/round-01/labels_snapshot.json. Mục quyết định là AI; QC cuối vẫn chờ.

`check_submission.py` kiểm tính đầy đủ và đồng bộ artifact. Kết quả 0 lỗi với quyết định Hold không chứng minh nhãn đúng hoặc hoàn tất review nội dung. Không có dòng ảnh sạch được tạo thay người học.
