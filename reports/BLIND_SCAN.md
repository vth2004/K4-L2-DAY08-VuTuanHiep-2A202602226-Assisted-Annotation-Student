# Quét độc lập trước khi xem pre-label

Frame: frame_0182.jpg

Số xe nhìn thấy bằng mắt: 290

Hai vị trí dễ bị AI bỏ sót hoặc vẽ sai, kèm mô tả xe: vị trí 1 — dải xe ở xa sát chân trời, chỉ còn
hai chấm đèn nhỏ hoặc vệt đèn hậu mờ, dễ bị model bỏ sót; vị trí 2 — xe phía dưới bị đèn pha chói lóa,
thân xe tối và có vệt sáng phản chiếu xuống mặt đường, dễ bị vẽ box ôm cả vệt sáng.

Chạy `python3 tools/lock_blind.py` ngay sau khi điền. Sau đó giữ file này nguyên vẹn.
