# Báo cáo QC — RANDOM — hồ sơ Hold ngày 17/11/2026

## 1. Mục đích, dữ liệu và phiên bản

- Mục đích và giới hạn sử dụng: thực hành nhãn 2D trên 40 ảnh; hồ sơ trung gian để coach đánh giá, chưa dùng làm bộ nhãn cuối.
- Nguồn và phép sử dụng dữ liệu: giữ data/ATTRIBUTION.csv và data/README.md.
- Manifest và checksum nhãn cuối: chưa có nhãn cuối; hash nhãn đầu vào, ảnh, guideline và taxonomy được script ghi trong iaa_summary.json, cùng handoff_manifest.json.
- Guideline và taxonomy khi gán / khi bàn giao: guideline chung v1.0; taxonomy nhận từ repo còn v0, đã đồng bộ mô tả lớp và version thành v1.0 trước IAA, giữ nguyên rules/attributes. Bản v0 gốc lưu source_evidence/taxonomy_original_v0.json; chưa có guideline sau calibration được người học chốt.

## 2. Phân công và đồng thuận trước calibration

- Lượt hoàn thành / được giao: 80/80 hợp lệ về cấu trúc (101: 40; 102: 40). Điều này không chứng minh tính độc lập của quá trình gán.
- Số cặp box ghép, IoU TB, đồng thuận lớp và mẫu số: 260 cặp; IoU TB 0.780958; cùng lớp 256/260 = 98.46%. IoU chưa đạt 0,85; đồng thuận lớp đạt 95%.
- Thuộc tính: cùng occluded_level 87/260; cùng truncated 237/260.
- Box chưa ghép, loại do ignore, bất đồng vùng ignore: 101 có 50; 102 có 199; 4 lượt bỏ qua do ignore; 59 ca bất đồng vùng ignore. Xem số liệu nguyên bản trong iaa_summary.json/.md.
- Ca hai người có thể cùng sai sau khi đối chiếu tham chiếu: chưa nhận/kiểm ca tham chiếu coach; không suy ra cùng đúng từ việc cùng lớp hoặc cùng bỏ qua ảnh.
- Nguồn nhãn: 102 có AI hỗ trợ tạo box; chưa xác nhận nhãn 101 được tạo độc lập với bộ 102. IAA ở đây mô tả hai file, chưa chứng minh độ đồng thuận của hai người gán độc lập.

## 3. Calibration và kiểm lại

- Ca đã xử lý, rule ID, bản sửa và người xác nhận: chưa có ca nội dung nào được nhóm xác nhận đóng; 0/1085. Bảng calibration_decisions.csv do build_final.py tạo giữ trống các trường quyết định. Queue bổ sung gợi ý rule và việc cần làm, không giả làm quyết định đã chốt.
- Tác động v1.1 lên nhãn và ảnh: chưa chốt thay đổi; 35/40 ảnh đang bị cờ. Không tạo giả guideline_v1.1_calibrated.md, guideline_change.json hoặc nhãn cuối để vượt kiểm tra.
- Tham chiếu bằng chứng khi Rework: chưa có vòng nhãn cuối. Xem visualizations/, calibration_candidates.csv và source_evidence/normalization_log.json.
- Chuẩn hóa dữ liệu đã thực hiện: loại riêng img_034 - Copy.jpg và 22 annotation ngoài phân công khỏi bản 101; giữ 40 ảnh và 320 annotation. Giữ nguyên ảnh chuẩn img_034.jpg với 21 annotation, mọi ID/box còn lại. Bản gốc 41 ảnh/342 annotation và phần loại được lưu làm bằng chứng.
- Cảnh báo kích thước từ validator: 101 có box #228, #229 ở img_031 và #333 ở img_039 cao dưới 20 px; phải kiểm R-09 khi calibration. Chưa tự sửa nhãn đầu vào đã đo IAA.

## 4. Kết quả duyệt

- Vòng QC: 1
- Trạng thái vòng: dự kiến, chưa mở được vòng 1 vì chưa có nhãn cuối và manifest sau calibration.
- Người review và ngày: Codex chạy kiểm tra tự động ngày 17/11/2026; 102 là reviewer được phân công, chưa có phiếu xác nhận review cuối của 102.
- Đã duyệt / cả lô: 0/40 ảnh có phiếu QC cuối; 40/40 ảnh đã kiểm cấu trúc và so sánh hai bộ nhãn bằng script.
- Ảnh lỗi / ảnh đã duyệt: N/A (mẫu số review cuối = 0), không ghi 0%.
- Annotation đối tượng hiện có bị lỗi / annotation đối tượng đã duyệt: N/A, chưa có bản nhãn cuối được duyệt.
- Vật bỏ sót, ảnh và vùng: 219 mục bỏ sót/thừa đang cần đối chiếu; chưa xác nhận số vật bỏ sót thực tế.
- Ca khó / phần còn lại, lỗi lặp và ví dụ: 1085 mục trên 35 ảnh, gồm 465 cờ review; không coi từng mục là một lỗi. Ưu tiên img_021 (132 mục), img_019 (86), img_032 (85), theo image_scores.csv.
- Một lỗi máy chặn được và một lỗi cần người đọc ảnh: máy chặn ảnh Copy ngoài danh mục; biên box che khuất/đồ chơi so với xe thật cần đối chiếu ảnh và quyết định thực tế.

## 5. Quyết định bàn giao

- Quyết định: Hold
- Lý do, người nhận và phạm vi sử dụng: gửi coach hồ sơ có đủ hai file nhãn và IAA thực đo, còn thiếu calibration và review nhãn cuối; chưa đủ bằng chứng Release. IoU hiện thấp hơn ngưỡng; không gán quyết định Rework theo tỉ lệ ảnh lỗi khi chưa có vòng duyệt.
- Nếu còn việc: 101/102 chốt 1.085 ca, xác minh nguồn và tính độc lập, sửa 35 ảnh bị cờ, lưu v1.1 và guideline_change.json theo quyết định thật; chạy prepare_calibration/build_final, duyệt toàn bộ 40 ảnh ở vòng 1; hoàn thiện ghi chú học tập cá nhân, đối chiếu ca tham chiếu coach. Chỉ người thực sự kiểm mới được ghi mã reviewer.
- Kiểm ngược từ báo cáo tới ảnh, rule và nhãn: ví dụ C001 trong calibration_candidates.csv -> img_001.jpg, box person chỉ có ở 101 -> visualizations/img_001.jpg -> R-03/R-05 và submission/labels/101.json; đường dẫn đã có, kết luận nội dung đang chờ calibration.

## 6. Kiểm tra và cách tiếp tục

Xem logs/ và HANDOFF.md. check_submission.py dự kiến còn lỗi do chưa có bằng chứng vòng 1; giữ nguyên lỗi và không sửa script để cho qua. Hồ sơ Hold được phép bàn giao bằng chứng hiện có theo SUBMISSION.md.

## Cập nhật calibration AI — 17/11/2026

Đã ghi quyết định ứng viên 114/1085 mục trên 13 ảnh, còn 971 mục trên 22 ảnh. Xem ai_calibration_20261117/REVIEW.md. Các con số 0/1085 ở mô tả bàn giao ban đầu bên trên là trạng thái trước lượt rà soát này; trạng thái hiện hành dùng cập nhật này. Chưa có QC cuối độc lập; quyết định Hold giữ nguyên.
