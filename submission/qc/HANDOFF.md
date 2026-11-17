# Bàn giao bằng chứng — HOLD — 17/11/2026

Repo nguồn: commit ebf26382bb9496aeab24924369e622450e1c7f32.
Đọc QC_REPORT.md trước. Đây là hồ sơ Hold gửi coach, chưa phải bộ nhãn Release.

## Có sẵn

- Nhãn 101 đã loại ảnh Copy ngoài phạm vi; bản gốc và lịch sử chuẩn hóa ở source_evidence/.
- Nhãn 102 export từ CVAT, có nguồn AI hỗ trợ được khai báo trong ghi chú cá nhân.
- Phân công 40 ảnh x 2 người; cấu hình v1.0, giữ nguyên ngưỡng rules.
- IAA, 1.085 ca, nháp đồng thuận do script sinh, 35 ảnh chồng nhãn, bảng quyết định đang trống.
- calibration_review_queue.csv đưa gợi ý công việc cho từng ca; không phải bảng kết luận.

## Còn cần nhóm thực hiện

1. Xác minh tính độc lập và cách dùng AI. Đọc ảnh chồng nhãn theo image_scores.csv; điền quyết định, rule, người chốt, sửa ở đâu và kiểm lại cho từng case_id. Không đánh dấu xong hàng loạt chỉ để script chạy.
2. Chốt guideline v1.1 và guideline_change.json (editorial hoặc semantic theo thay đổi thực tế). Không sửa bản v1.0 hoặc hai bộ nhãn đầu vào sau khi khóa IAA; nếu cần thay đầu vào phải giữ hồ sơ cũ và đo lại.
3. Chạy python scripts/prepare_calibration.py, sửa ảnh trong CVAT và export đúng tên phiên bản được script in ra.
4. Chạy python scripts/build_final.py, python scripts/qc_sample.py --round 1, duyệt toàn bộ 40 ảnh và ghi phiếu thật, rồi python scripts/qc_report.py --round 1.
5. Hoàn thiện báo cáo theo kết quả thật và ghi chú cá nhân; python scripts/check_submission.py --round 1. Giữ mọi vòng cũ nếu sửa tiếp.

Không có guideline_change.json, bản v1.1, nhãn cuối hoặc kết quả QC vòng 1 vì những quyết định đó chưa diễn ra. Các script kiểm tra vẫn giữ nguyên và log lỗi được lưu, không diễn giải exit code lỗi thành thành công.
Ghi chú 101 còn phần tự phản tư chưa điền và ca img_038 được mô tả là phản chiếu trong ghi chú cũ, trong khi ảnh là tủ đồ chơi; thành viên 101 cần tự kiểm và sửa, không viết thay cảm nhận cá nhân.
