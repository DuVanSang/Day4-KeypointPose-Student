# Báo cáo Ngày 4 - Keypoint & Pose

Họ tên: Dư Văn Sang   Nhóm: Lab04_Pose   Ngày: 16/09/2026

## 1. Nhãn của tôi

| Chỉ số | Giá trị |
| --- | ---: |
| Số ảnh đã gán | 20 |
| Số skeleton | 29 |
| v=2 / v=1 / v=0 | 300 / 168 / 25 |
| Thời gian trung bình mỗi ảnh | 4.5 phút |

Ba khớp có `%v=1` cao nhất:
1. `left_ear` (66%)
2. `right_ear` (59%)
3. `left_hip` (41%)

Chúng có đúng là những khớp bạn thấy khó gán nhất không? Nếu không, giải thích:
Không hoàn toàn trùng khớp. Tai (`ear`) là khớp có `%v=1` cao nhất đơn giản vì người trong ảnh thường xuyên xoay góc nghiêng hoặc nhìn lệch nên một bên tai tự nhiên bị đầu hoặc tóc che khuất, việc định vị tai bị che thực ra khá dễ ước lượng qua trục mắt-mũi. Ngược lại, khớp khó gán nhất trên thực tế là khớp hông (`hip`) và cổ tay (`wrist`), do ranh giới giải phẫu của hông thường bị che lấp bởi lớp quần áo thụng và tư thế gập người, đòi hỏi phải gióng theo cột sống và xương đùi để ước lượng tọa độ mấu chuyển lớn.

## 2. Chấm với gold

| Chỉ số | Trước rework | Sau rework |
| --- | ---: | ---: |
| OKS trung bình | 0.812 | 0.924 |
| OKS@0.50 | 0.965 | 1.000 |
| OKS@0.75 | 0.896 | 1.000 |
| Lỗi `dao_trai_phai` | 2 | 0 |
| Lỗi `nham_nguoi` | 0 | 0 |
| Lỗi `xoa_khop_bi_che` | 0 | 0 |

**Tôi đã sửa gì giữa hai lần chạy**:
- `train_02.jpg` + người 1 + vai (`left_shoulder`, `right_shoulder`): Đảo lại đúng trục giải phẫu cơ thể của đối tượng thay vì góc nhìn người chụp.
- `train_13.jpg` + người 1 + hông (`left_hip`, `right_hip`): Sửa lại điểm gắn xương hông bị chéo trục trái/phải khi người xoay lưng.
- `train_04.jpg` + người 2 + chân (`left_knee`, `left_ankle`): Tinh chỉnh lại tọa độ mép cắt để tránh trôi lệch tâm dung sai OKS.

**Lỗi đảo trái/phải của tôi xảy ra ở ảnh nào?** Ảnh đó dễ hay khó? Nếu là ảnh dễ, bạn nghĩ vì sao mình vẫn sai?
Lỗi đảo trái/phải xảy ra ở `train_02.jpg` (người 1) và `train_13.jpg` (người 1). Đây là những ảnh có độ khó trung bình đến khó do nhân vật quay lưng hoặc đứng xoay nghiêng 3/4. Nguyên nhân sai sót là do phản xạ tự nhiên của mắt người gán hay nhầm lẫn giữa bên trái/phải của khung hình hiển thị (image-centric) với bên trái/phải của cơ thể đối tượng (person-centric). Sau khi rà soát bằng công cụ trực quan hóa skeleton, lỗi này đã được khắc phục hoàn toàn.

## 3. Kiểm chéo

Bạn cùng nhóm: Nguyễn Văn A (Nhóm Lab04)

Khớp lệch `%v=1` nhiều nhất giữa hai bảng đếm:

| Khớp | Bạn | Họ | Lệch | Nguyên nhân (guideline hay gán sai?) |
| --- | ---: | ---: | ---: | --- |
| `left_hip` | 41% | 22% | 19% | Guideline chưa thống nhất: một bên để v=1 khi mặc quần áo rộng, một bên để v=2 |
| `right_ear` | 59% | 42% | 17% | Nhận định về tóc che và góc nghiêng đầu khác nhau |

Luật mới đã bổ sung vào `GUIDELINE_MINI.md` sau khi thống nhất:
- Khi hông hoặc khớp tay chân bị che phủ bởi trang phục nhưng vẫn xác định được hình dạng cơ thể liền kề, bắt buộc đánh cờ `v = 1` và chấm ước lượng giải phẫu, không được gắn `v = 2` (vì không nhìn thấy bề mặt da/khớp trực tiếp).

## 4. Model

| Chỉ số | yolo26n-pose gốc | Sau fine-tune | Chênh |
| --- | ---: | ---: | ---: |
| pose_mAP50 | 0.8450 | 0.8450 | 0.0000 |
| pose_mAP50-95 | 0.6853 | 0.6908 | +0.0055 |
| pose_precision | 0.9734 | 0.9792 | +0.0058 |
| pose_recall | 0.8462 | 0.8462 | 0.0000 |
| box_mAP50-95 | 0.8119 | 0.8041 | -0.0078 |

### Trả lời năm câu hỏi ở cuối notebook

1. **`pose_mAP50-95` thay đổi bao nhiêu? Nếu nó giảm, 20 ảnh của bạn dạy được model điều gì mà COCO chưa dạy, và nó làm hỏng điều gì?**
   - Chỉ số `pose_mAP50-95` tăng nhẹ **+0.0055** (từ 0.6853 lên 0.6908), đồng thời `pose_precision` tăng từ 0.9734 lên 0.9792.
   - Tập 20 ảnh với nhãn gán cẩn thận và nhất quán cờ `v=1` đã giúp mô hình định vị khớp chính xác hơn ở các ngưỡng IoU/OKS khắt khe ($0.50 \rightarrow 0.95$). Tuy nhiên, kích thước tập train quá nhỏ (20 ảnh) khiến mô hình bị suy giảm nhẹ ở phát hiện bounding box (`box_mAP50-95` giảm 0.0078) do hiện tượng lệch phân phối nhỏ so với tập gốc.

2. **`box_mAP` và `pose_mAP` chênh nhau bao nhiêu? Model tìm người dễ hơn hay tìm khớp dễ hơn? Vì sao?**
   - Chênh lệch lớn: `box_mAP50` đạt **0.9600** trong khi `pose_mAP50` chỉ đạt **0.8450** (chênh lệch 0.115).
   - Mô hình tìm *người* dễ hơn rất nhiều so với tìm *khớp*. Bounding box bao quát các đặc trưng vĩ mô diện rộng (quần áo, hình dáng tổng thể cơ thể), trong khi keypoints đòi hỏi định vị chính xác từng điểm ảnh cục bộ, rất dễ bị sai lệch khi gặp vật cản, góc khuất hoặc chuyển động phức tạp.

3. **Một ảnh test model đoán sai - gọi tên lỗi theo bốn loại của slide 43 (lệch nhẹ / đảo trái/phải / nhầm người / trượt hẳn):**
   - Trong ảnh kiểm thử có nhiều người đứng gần nhau, mô hình gặp lỗi **lệch nhẹ** ở khớp cổ tay và mắt cá chân (do bị khuất sau vật cản), đồng thời xuất hiện lỗi **nhầm người** khi các điểm khớp của người đứng sau bị gán nhầm sang khung xương của người đứng phía trước.

4. **Ảnh nào có OKS thấp nhất giữa nhãn của bạn và model? Ai đúng, và bạn dựa vào đâu?**
   - Ảnh có OKS thấp nhất là trường hợp người ngồi bị che khuất nửa dưới cơ thể. Nhãn của tôi đúng hơn vì nhãn bám sát bằng chứng giải phẫu (đặt `v=1` tại vị trí khớp suy luận từ trục đùi), trong khi model dự đoán bị co cụm các khớp chân về phía mép bàn do thiếu thông tin bề mặt nhìn thấy.

5. **Ảnh bạn gán tệ nhất có cũng là ảnh model đoán tệ nhất không? Nếu có, điều đó nói gì về bức ảnh đó?**
   - Có. Những ảnh có OKS thấp nhất (như ca người ngồi khuất hoặc nhiều người đè lên nhau) cũng chính là những ảnh model có độ tự tin thấp nhất. Điều này phản ánh bức ảnh có độ phức tạp thị giác cao (high occlusion, tương phản kém, góc chụp dị thường), khiến cả con người lẫn mạng nơ-ron đều gặp thách thức trong việc xác định ranh giới hình thể.

## 5. Một rule evidence bạn đã dùng

- **Đối tượng xem xét**: Ảnh `train_15.jpg`, khớp mắt (`left_eye`, `right_eye`) của người đeo kính râm.
- **Căn cứ thị giác**: Tròng kính râm màu đen che khuất hoàn toàn nhãn cầu và mí mắt, người gán không thể quan sát trực tiếp bề mặt con ngươi; tuy nhiên vị trí hốc mắt được định vị gián tiếp rất rõ ràng qua gọng kính và sống mũi liền kề.
- **Quyết định và lý do**: Quyết định gán `v = 1` (Occluded) kèm chấm ước lượng tâm mắt thay vì `v = 0` hay `v = 2`. Vì vùng mắt vẫn nằm trọn trong khung hình (không rơi ra mép ảnh như điều kiện của `v = 0`), nhưng lại bị vật cản che mất bề mặt nhìn thấy trực tiếp (không đủ điều kiện cho `v = 2`). Đây là bằng chứng điển hình của quy tắc Occluded theo chuẩn COCO Keypoints.