# Tự đánh giá — Vũ Hữu Hưng · G01

## 1. Tự rà soát 4 cảnh

Nghỉ tay 1 phút, mở lại bài của mình, chọn 4 ảnh (2 S##, 2 D##), đọc như người lạ.

Loại lỗi: `seat_flip`, `wrong_class`, `guess`, `box`, `missing`, `extra`, `lr_flip` (trái/phải mặt), `flag` (thiếu cờ điểm), `ok`.

| Ảnh | Đối tượng | Loại lỗi | Ghi chú | Đã sửa? |
|---|---|---|---|---|
| `S04_rgb.png` | `person` / `face` (`rear_middle`) | `missing` | Đã vẽ box `person` ở `rear_middle` nhưng thiếu box `face` kèm theo (mục 4.2 OMS yêu cầu người lớn có `person` kèm `face` nếu nhìn thấy mặt); lỗi này cũng lặp sang `S04_ir.png` khi copy shape | Chưa (cần mở CVAT kiểm lại mặt trên `S04` và bổ sung vào `v2`) |
| `S07_rgb.png` | `infant_seat` (`rear_middle`) | `wrong_class` | Gán `infant_seat` (`facing = front`) nhưng dễ nhầm với `child_seat` do ghế quay trước (bạn review gán `child_seat`); lặp lại trên `S07_ir.png` | Chưa (cần hỏi coach chốt phân biệt `infant_seat` vs `child_seat` cho `v2`) |
| `D01.png` | `face13` (`mouth_right`) | `flag` | Điểm `mouth_right` đang bật đồng thời cả `occluded=1` và `outside=1`; theo mục 4.6 nên chọn rõ một cờ (`occluded` nếu đoán chắc vị trí, `outside` nếu không đoán được) | Chưa (cần chỉnh lại cờ trên CVAT cho `v2`) |
| `D05.png` | `face`, `face13`, `phone` (`driver`) | `ok` | Giữ đúng nguyên tắc `unknown` cho `eye_left` bị khuất (4 điểm mắt trái bật `outside`), chỉ vẽ `phone` (`state = held`) khi thấy rõ hình dạng máy, `on_wheel = no` đúng bối cảnh xe tự lái | Không cần sửa |

## 2. Lỗi mình lặp lại nhiều nhất hôm nay

Lỗi: Copy shape từ ảnh `_rgb` sang ảnh `_ir` trước khi rà soát đủ checklist đối tượng và lớp ở ảnh `_rgb`, làm cùng một lỗi/bất đồng lặp lại trên cả cặp ảnh (ví dụ: thiếu box `face` ở cả `S04_rgb.png` & `S04_ir.png`, và dễ nhầm lớp `infant_seat` / `child_seat` ở cả cặp `S07_rgb/ir` và `S08_rgb/ir`).

Vì sao mình mắc: Muốn tiết kiệm thời gian bằng thao tác `Ctrl+C` / `Ctrl+V` từ `_rgb` sang `_ir`, nhưng chưa dừng lại kiểm tra đủ mục 4.1–4.2 trên ảnh gốc trước khi nhân bản sang ảnh IR.

Lần sau tránh bằng cách: Hoàn tất và tự soát ảnh `_rgb` theo đủ 3 bước trước khi copy: (1) mọi `person` thấy mặt đều có box `face`, (2) kiểm tra lại `seat` theo xe (`trái ảnh = rear_right`), (3) kiểm tra kỹ ranh giới `infant_seat` (nôi xách) vs `child_seat`. Sau khi dán sang `_ir`, rà lại riêng từng thuộc tính dễ mất thông tin (`occupied`, `seatbelt`) để chuyển về `unknown` nếu không thấy rõ.

## 3. Ghi chú về disagreement_log

Một dòng bạn của mình đúng và mình sai. Mình học được gì từ dòng đó: Ở `S04_rgb.png` và `S04_ir.png` (dòng `face, box — chỉ có ở peer`), mình đã vẽ `person` nhưng quên vẽ box `face` đi kèm theo đúng quy tắc mục 4.2 OMS. Mình học được rằng `cabin_check check` đạt `0 lỗi` chỉ xác nhận hợp lệ về mặt cấu trúc/schema chứ không tự phát hiện được đối tượng bị bỏ sót; vì vậy cần kết hợp tự rà soát theo checklist và đối chiếu chéo (`cabin_check diff`) để tìm ra các lỗi thiếu nhãn.
