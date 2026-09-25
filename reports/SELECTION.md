# Vì sao chọn lô này?

Trong 50 dòng đứng đầu `outputs/selection_round1.csv`, chọn năm frame tôi ưu tiên nếu chỉ có ngân
sách rà năm ảnh:

| thứ tự | frame | thời điểm | score | lý do |
| --- | --- | ---: | ---: | --- |
| 1 | frame_0182.jpg | 72.8s | 0.9591 | rank 1, điểm cao nhất, nhiều box mơ hồ (A=1.0) nên model phân vân nhất |
| 2 | frame_0369.jpg | 147.6s | 0.9324 | rank 2, U và A đều cao, thời điểm cuối khác hẳn đầu video |
| 3 | frame_0326.jpg | 130.4s | 0.9155 | rank 4, U=0.931, đắt box khó |
| 4 | frame_0099.jpg | 39.6s | 0.9063 | rank 8, đầu video, trải thời gian để đa dạng |
| 5 | frame_0312.jpg | 124.8s | 0.9100 | rank 7, lấp khoảng trống giữa 74.8s và 130.4s |

Quyết định xét ảnh gần trùng: **frame_0372.jpg (t=148.8s, score 0.9101)** không được chọn dù trong
top 6 vì cách frame_0369.jpg chỉ 1.2s — đúng dưới MIN_GAP_S=2.0s, gần như trùng khung hình, gán
cả hai tốn công mà model học thêm rất ít.

Ba frame thuộc lô 12 ảnh model chọn, có bằng chứng cột `selected=True` trong CSV: **frame_0182.jpg**
(rank 1, 0.9591), **frame_0369.jpg** (rank 2, 0.9324), **frame_0392.jpg** (rank 15, 0.8874 nhưng có
U cao nhất pool = 0.9747 — model phân vân nhất trên từng box).

Một frame có điểm cao nhưng không chọn: **frame_0372.jpg** (rank 6) — gần trùng với frame_0369 đã chọn.
Một frame có điểm thấp vẫn nên xem: **frame_0195.jpg** (rank 268, t=78.0s, score 0.5721) — cuối vùng
pool, ít box mơ hồ nên bị đánh thấp, nhưng là mốc thời gian chưa có nhãn nào, nếu ảnh tối/giãn cách xe
thì vẫn đáng một lượt nhìn.

Điều phép chọn này chưa chứng minh về chất lượng mô hình: điểm bất định chỉ đo mức bài toán của model
trên từng frame, chưa đảm bảo gán nhãn những frame đó sẽ tăng AP50 trên tập test — cần chạy vòng sau
(fine-tune + chấm test) mới kiểm chứng.