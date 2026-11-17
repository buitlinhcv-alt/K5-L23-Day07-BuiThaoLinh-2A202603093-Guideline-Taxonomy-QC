# Rà soát calibration do AI thực hiện — 17/11/2026

Người dùng 102 ủy quyền AI xem ảnh và chốt ca. AI đã xem 35 ảnh chồng nhãn, đối chiếu guideline v1.0. Không ghi người học đã review thay AI.

- Có quyết định ứng viên: 114/1085 mục trên 13 ảnh rõ.
- Còn cần rà sâu: 971/1085 mục trên 22 ảnh. Ảnh tổng quan không đủ để khẳng định mọi biên vật nhỏ/che khuất.
- Không đổi hai bộ nhãn đầu vào hoặc IAA. Không đánh dấu ca chưa đủ bằng chứng là đã xử lý.
- File nhãn ứng viên chỉ chứa 13 ảnh đã ghi quyết định; chưa phải export hiệu chỉnh theo hash của prepare_calibration.py. Giữ can_review để kiểm cuối.
- Những quyết định có điều kiện vẫn cần kiểm ở QC cuối; chưa tuyên bố đo IoU đạt ngưỡng hay không bỏ sót vật.
- Chưa tạo v1.1 hoặc guideline_change.json vì còn quyết định nội dung chưa chốt; không đóng cả 1085 ca bằng quy tắc chọn một người hàng loạt.

## Quyết định theo ảnh

- img_001.jpg: Tách bảy người và pickup; không gộp người vào xe. Người ngồi bị che có mức che riêng; người đứng và xe cắt mép dưới/phải. Chọn hình học 102 làm ứng viên.
- img_004.jpg: Hai tuk-tuk ngoài taxonomy; giữ hai người lái, không gán motorcycle cho xe ba bánh. Người lái bị che nhiều.
- img_007.jpg: Hai xe có cabin và thùng riêng: truck. Xe bên phải bị cắt mép; xe bên trái thấy rõ. Giữ 102.
- img_012.jpg: Pickup tiền cảnh là truck bị cắt mép; xe con bên trái car; xe hàng giữa truck. Không bao hàng rời thành đối tượng. Giữ 102.
- img_015.jpg: Hai ô tô tiền cảnh cắt mép và các ô tô bên kia đường đều là car; không gán phản chiếu dưới mặt đường. Giữ 102, cần kiểm sát pixel khi review cuối.
- img_016.jpg: Loại tuk-tuk; giữ người lái/người tiền cảnh, xe máy hai bánh, van trắng và pickup bị che. Giữ đề xuất 102.
- img_018.jpg: Không gán chân dung quảng cáo; gán xe thật phía dưới, hàng rào gây che khuất. Giữ 102; loại biến thể box không bao đủ phần xe nhìn thấy.
- img_022.jpg: Giữ riêng phần mô tô hai bánh trong 102, không gộp thùng bên vào box; việc đưa toàn bộ tổ hợp sidecar vào lớp là ngoài định nghĩa hiện tại. Không có người thật nhận diện được.
- img_026.jpg: Hai người trên một xe: hai person và một motorcycle, không gộp. Xe bị cắt mép dưới. Giữ 102.
- img_028.jpg: Pickup thật là truck; tượng ngựa không gán; ô tô đỏ phía sau là car bị che. Giữ 102.
- img_029.jpg: Người đi xe đạp vẫn là person, không có lớp bicycle; người đi xe máy và mô tô tách riêng. Giữ 102.
- img_037.jpg: Cabin và thùng riêng dù có nắp cao: truck; giữ người bên trái; loại xe buýt lớn ngoài taxonomy. Giữ 102.
- img_040.jpg: Giữ người đội mũ, taxi thật, xe phía sau; không gán mặt quảng cáo hoặc phản chiếu trên mũ. Không tạo box motorcycle chỉ từ gương chưa thấy thân xe. Giữ 102.
