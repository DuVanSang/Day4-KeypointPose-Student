# Visibility report

- Thư mục nhãn: `dataset\labels\train`
- 20 ảnh, 29 skeleton, trung bình 16.14 khớp có v > 0 mỗi người
- Tổng: v=2 300 | v=1 168 | v=0 25

| # | Khớp | v=2 | v=1 | v=0 | %v=1 |
| ---: | --- | ---: | ---: | ---: | ---: |
| 0 | nose | 20 | 9 | 0 | 31% |
| 1 | left_eye | 18 | 11 | 0 | 38% |
| 2 | right_eye | 18 | 11 | 0 | 38% |
| 3 | left_ear | 10 | 19 | 0 | 66% |
| 4 | right_ear | 12 | 17 | 0 | 59% |
| 5 | left_shoulder | 25 | 4 | 0 | 14% |
| 6 | right_shoulder | 26 | 3 | 0 | 10% |
| 7 | left_elbow | 22 | 7 | 0 | 24% |
| 8 | right_elbow | 22 | 7 | 0 | 24% |
| 9 | left_wrist | 18 | 11 | 0 | 38% |
| 10 | right_wrist | 18 | 10 | 1 | 34% |
| 11 | left_hip | 16 | 13 | 0 | 45% |
| 12 | right_hip | 17 | 12 | 0 | 41% |
| 13 | left_knee | 15 | 11 | 3 | 38% |
| 14 | right_knee | 16 | 10 | 3 | 34% |
| 15 | left_ankle | 15 | 5 | 9 | 17% |
| 16 | right_ankle | 12 | 8 | 9 | 28% |

## Đọc bảng này thế nào

1. Khớp nào có **%v=1 cao**: khớp hay bị che. Cổ tay và hông thường là hai vị trí cần xem lại guideline trước khi kết luận.
2. Khớp nào có **v=0 cao bất thường**: mọi người đang dùng Outside ở chỗ đáng lẽ là Occluded. Đó là lỗi số 3 của slide 46, và nó xoá thẳng khớp đó khỏi bảng điểm OKS.
3. Khi so hai người: **lệch lớn = bất đồng về guideline**, không phải về bức ảnh. Sửa guideline trước, sửa nhãn sau.
