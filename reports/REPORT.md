# Báo cáo Lab Ngày 08: Học chủ động cho bộ phát hiện xe

Họ và tên: Vũ Tuấn Hiệp  
Công cụ gán nhãn: CVAT, định dạng Ultralytics YOLO Detection 1.0

## 1. Dữ liệu và cách chia tập

Video quay đường cao tốc ban đêm, các frame liên tiếp khá giống nhau. Vì vậy tôi giữ cách chia theo thời gian và có khoảng đệm giữa pool với test. Nếu chia ngẫu nhiên, frame train và test có thể gần như trùng cảnh, làm kết quả đánh giá cao hơn thực tế.

## 2. Kết quả cold start

Model ban đầu là `yolov8n` đã học từ COCO (car, bus, truck), chưa train thêm trên dữ liệu này.

| AP50 | Precision | Recall | F1 | Recall small | Recall medium | Recall large |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 0.771 | 0.925 | 0.489 | 0.640 | 0.182 | 0.547 | 0.561 |

Model bắt xe lớn khá tốt nhưng bỏ sót nhiều xe nhỏ và xe ở xa. Kết quả chi tiết nằm trong `outputs/metrics_round0.json`.

## 3. Cách chọn ảnh

Tôi chọn ảnh dựa trên độ không chắc chắn của model, số box có confidence thấp và khoảng cách thời gian với các ảnh đã chọn. Điểm dùng công thức `0.5U + 0.3A + 0.2D`; các ảnh trong cùng khoảng 2 giây được hạn chế chọn cùng lúc.

Trong nhóm đầu của `outputs/selection_round1.csv`, các ảnh đáng chú ý là:

- `frame_0182.jpg`: rank 1, score 0.9591.
- `frame_0369.jpg`: rank 2, score 0.9324.
- `frame_0326.jpg`: rank 4, score 0.9155.
- `frame_0372.jpg`: rank 6 nhưng không chọn vì chỉ cách `frame_0369.jpg` 1.2 giây.

Cuối cùng tôi sửa nhãn cho 12 ảnh. Điểm chọn mẫu chỉ cho biết model đang khó ở đâu, chưa đảm bảo chắc chắn ảnh đó sẽ giúp model tốt hơn.

## 4. Rà nhãn và fine-tune

Từ 169 box model đề xuất, tôi giữ 149 box, chỉnh 7, xóa 13 và thêm 142 box. Sau khi sửa, tổng cộng có 298 box. Chi tiết nằm trong `outputs/round1_diff.md` và `reports/REVIEW_LOG.csv`.

Kết quả sau fine-tune 50 epoch:

| AP50 | Precision | Recall | F1 | Recall small | Recall medium | Recall large |
| ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| 0.522 | 1.000 | 0.174 | 0.296 | 0.000 | 0.149 | 0.634 |

AP50 giảm từ 0.771 xuống 0.522. Model sau train gần như chỉ giữ lại những xe chắc chắn, nên precision tăng nhưng recall giảm mạnh. Xe lớn có recall tăng nhẹ, còn xe nhỏ bị bỏ sót hoàn toàn. Có vẻ 12 ảnh train chưa đủ đa dạng, đồng thời model bị lệch về các xe lớn ở gần camera.

## 5. Kết luận và hướng tiếp theo

Vòng 1 chưa hiệu quả. Nếu làm tiếp, tôi sẽ ưu tiên thêm ảnh có xe nhỏ ở gần chân trời và xe bị đèn pha che sáng. Trước khi train tiếp, tôi sẽ kiểm tra lại nhãn ở các ảnh mới, tránh chọn frame quá gần nhau và thử giảm số epoch hoặc thay đổi augmentation.

Kết quả cần được nhìn với một chút thận trọng: tập test chỉ có 20 ảnh, các box rất nhỏ bị bỏ qua khi chấm, và nhãn test do model tạo chứ chưa được người kiểm tra toàn bộ. Vì vậy AP50 hiện tại chủ yếu cho biết model khớp với bộ tham chiếu này đến đâu, chưa phải chất lượng thực tế tuyệt đối.
