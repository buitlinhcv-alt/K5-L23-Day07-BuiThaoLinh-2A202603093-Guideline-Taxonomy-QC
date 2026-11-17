# Hồ sơ vòng 1 — HOLD

Đã có v1.1 editorial, guideline_change.json, nhãn ứng viên sau pipeline (COCO/CSV/manifest), snapshot và phiếu vòng 1 full 40 ảnh. Đọc QC_REPORT.md và reviews/round-01/qc_report_auto.md.
114 quyết định ứng viên do AI, 971 mục DEFER_TO_QC. Phiếu review cuối vẫn trống, cờ can_review giữ nguyên. Chưa có review độc lập hay xác nhận tính độc lập. Đủ file để kiểm bàn giao Hold không có nghĩa đã đóng ca hoặc đạt Release.
Lỗi làm tròn xyxy -> xywh khiến vượt mép 0,01 px đã sửa trong scripts/common.py; artifact IAA/consensus trước sửa được giữ ai_calibration_20261117. Hai bộ nhãn đầu vào không đổi; số đo IAA không đổi. 101_original_export.json giữ trước bước loại ảnh Copy.
Tiếp tục chốt ca bằng ảnh gốc, sửa thật nếu cần, cập nhật bảng quyết định và xuất lại theo hash; build_final.py rồi mở vòng 2 khi bản nhãn thay đổi. Chỉ điền reviewer và kết quả khi review thực sự diễn ra.

Hiện hành: vòng 2; đã sửa 3 phát hiện QAI01–QAI03, 488 annotation. Rà soát AI đã xem 40 ảnh nhưng chỉ 9 ảnh đủ để ghi kết luận lượt này; 31 ảnh chờ. Dùng check_submission.py --round 2. Vòng 1 giữ đầy đủ snapshot cũ.
