# Mini guideline - nhóm: Lab04_Pose  |  người gán: Dư Văn Sang  |  ngày: 16/09/2026

> Điền file này trong lúc gán nhãn, không phải sau khi xong. Mỗi lần bạn dừng lại
> hơn 10 giây để phân vân, đó là một dòng phải ghi vào đây.

## 1. Luật bắt buộc (đã thống nhất cả lớp - không sửa)

- Bộ 17 điểm COCO, đúng tên, đúng thứ tự. Lấy từ file .SVG chung.
- Mọi người trong ảnh đều có đủ 17 điểm. Điểm không dùng được thì gắn cờ, không xoá.
- Trái/phải tính theo cơ thể người, không theo bức ảnh.
- Bị che, còn trong khung -> v = 1, vẫn đặt chấm ở vị trí ước lượng.
- Ra ngoài mép ảnh -> v = 0, không đặt chấm.
- Không dùng Hidden (h) - nó không được lưu vào file.

## 2. Luật của nhóm bạn (phải điền)

| Tình huống | Luật nhóm bạn chọn | Vì sao |
| --- | --- | --- |
| Hông của người mặc quần áo dài | Chấm tại mấu chuyển lớn xương đùi (độ cao đáy xương chậu/đũng quần), gán v=1 | Vải áo dài/quần thụng che khuất khớp nhưng khung xương vẫn nằm trong ảnh; chấm theo cấu trúc giải phẫu để giữ nguyên liên kết pose |
| Tai bị tóc hoặc mũ bảo hiểm che một phần | Chấm tại vị trí ống tai ngoài; gán v=1 nếu bị che một phần hoặc che khuất; gán v=2 nếu thấy rõ vành tai | Đảm bảo tính nhất quán giữa vùng đầu và mặt; không bỏ qua tai khi đầu vẫn quay về hướng chụp |
| Người bị cắt ở mép ảnh (chỉ thấy từ hông trở lên) | Các khớp nằm ngoài viền cắt (đầu gối, cổ chân) bắt buộc gán v=0 tại tọa độ (0, 0) | Đúng chuẩn COCO: khớp không tồn tại trong không gian ảnh không được phép đặt chấm ảo |
| Cổ tay nằm sau tay lái / sau thân mình | Ước lượng điểm nối cẳng tay và bàn tay, gán v=1 | Vật thể hoặc chính cơ thể che khuất (occluded) nhưng tọa độ vẫn nằm bên trong khung hình |
| Hai người chồng lên nhau | Tách từng skeleton độc lập; khớp bị cơ thể người kia che vẫn đặt chấm ước lượng và gán v=1 | Tránh nhầm người (ID switch) và tránh bỏ sót điểm quan trọng khi tính OKS |
| Người nhỏ đến mức nào thì không gán nữa | Bounding box có chiều cao hoặc rộng dưới 20 px hoặc mờ nhòe hoàn toàn | Tránh sinh nhiễu vị trí do độ phân giải không đủ để xác định giải phẫu người |

## 3. Ba ca mơ hồ đã gặp (bắt buộc, ghi ít nhất 3)

### Ca 1 - ảnh `train_15.jpg`, người thứ `1` (hoặc `2`), khớp `left_eye` / `right_eye`
- Mơ hồ ở chỗ nào: Nhân vật đeo kính râm tối màu che khuất hoàn toàn tròng mắt và mí mắt, không nhìn thấy bề mặt con ngươi trực tiếp.
- Bạn quyết thế nào: Ước lượng tâm hốc mắt phía sau tròng kính râm và gán cờ `v = 1` (Occluded).
- Vì sao: Mắt vẫn nằm trong khung hình và xác định được vị trí giải phẫu nhờ gọng kính và sống mũi; theo quy tắc khi bị che khuất bề mặt nhưng còn trong ảnh thì dùng `v = 1` và vẫn đặt chấm.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu để `v = 2`, model học sai khái niệm "nhìn thấy rõ" (visual evidence). Nếu để `v = 0`, model sẽ bỏ qua mắt mỗi khi gặp người đeo kính, làm hỏng khả năng dự đoán pose khuôn mặt ngoài đời thực.

### Ca 2 - ảnh `train_03.jpg`, người thứ `1`, khớp `right_shoulder` / `right_elbow` / `right_wrist`
- Mơ hồ ở chỗ nào: Người đứng phía sau bị cơ thể của người đứng phía trước che khuất gần như toàn bộ cánh tay (vai, khuỷu tay, cổ tay).
- Bạn quyết thế nào: Dựa vào hướng thân người và tư thế để tự mường tượng vị trí giải phẫu cánh tay khuất sau lưng, đặt chấm ước lượng và gán cờ `v = 1` (Occluded).
- Vì sao: Cơ thể người phía sau vẫn liên tục, các khớp cánh tay vẫn nằm trong ranh giới ảnh chứ không rơi ra ngoài mép ảnh. Gán `v = 1` giúp giữ toàn vẹn khung xương 17 điểm.
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu gán `v = 0` (xóa khớp), model sẽ bị "đứt gãy" khung xương khi gặp cảnh đông người chen lấn, mất khả năng học quan hệ không gian giữa các khớp bị che khuất bởi người khác.

### Ca 3 - ảnh `train_13.jpg`, người thứ `3`, khớp: toàn bộ thân trên / chi dưới
- Mơ hồ ở chỗ nào: Người ở hậu cảnh đứng quá xa và bị mờ nhòe (out of focus/motion blur), ranh giới chi tiết từng khớp rất khó phân định chính xác.
- Bạn quyết thế nào: Định vị cấu trúc cơ thể theo khối bóng mờ và gán hầu hết các bộ phận là `v = 1` (Occluded) thay vì `v = 2`.
- Vì sao: Để đảm bảo an toàn về mặt chất lượng nhãn: bề mặt khớp không đạt độ nét tin cậy để coi là nhìn rõ (`v = 2`), nhưng nhân vật vẫn tồn tại rõ trong ảnh nên không thể bỏ qua hay xóa khớp (`v = 0`).
- Nếu người khác quyết ngược lại thì model học sai cái gì: Nếu tự tin gắn `v = 2` cho các điểm mờ nhòe, model sẽ học đặc trưng sai lệch (nhiễu điểm ảnh) và tự tin dự đoán sai vị trí khi gặp ảnh độ phân giải thấp.

## 4. Sau khi so visibility report với bạn cùng nhóm

- Khớp lệch `%v=1` nhiều nhất: `left_hip` (bạn `41%` / họ `20%`)
- Nguyên nhân là **guideline chưa rõ** hay **một trong hai bên gán sai**: Guideline ban đầu chưa làm rõ người mặc quần áo tối màu hoặc góc chụp nghiêng thì hông tính là thấy rõ (`v=2`) hay bị che (`v=1`). Bên bạn tuân thủ nguyên tắc khi vải quần che mấu xương thì để `v=1`, trong khi bên bạn cùng nhóm để `v=2`.
- Luật mới bổ sung vào mục 2 sau khi thống nhất: Với khớp hông, nếu không nhìn thấy rõ khớp nối chuyển động (bị quần áo rộng che phủ) thì thống nhất gán `v=1` theo ước lượng giải phẫu.