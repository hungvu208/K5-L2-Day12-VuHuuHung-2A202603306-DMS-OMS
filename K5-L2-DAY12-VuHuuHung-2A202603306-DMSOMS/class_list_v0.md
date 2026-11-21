# Danh sách lớp v0 — Vũ Hữu Hưng · G01

Viết tiếp từ slide 54 bài giảng. Mỗi lớp trả lời: **model cần nhận ra cái này để làm gì?**

## DMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng DMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `face.eye_left` / `face.eye_right` | `open` / `closed` / `unknown` | Phát hiện tài xế buồn ngủ (tính tỷ lệ nhắm mắt kéo dài PERCLOS, chớp mắt chậm) để cảnh báo kịp thời | Có (NIR thấy rõ cả ban đêm), trừ khi đốm phản xạ đèn LED trên kính hoặc kính râm dày che mất → `unknown` |
| 2 | `face.gaze_zone` | `road` / `mirror_l` / `mirror_r` / `mirror_c` / `cluster` / `center` / `lap` / `other` | Phát hiện mất tập trung (rời mắt khỏi đường kéo dài) và phân biệt thao tác an toàn (nhìn gương, đồng hồ) với xao nhãng (nhìn màn hình giữa, nhìn đùi) | Có (dựa trên hướng đầu và vùng mắt trên NIR), khó xác định hơn khi mặt nghiêng nhiều hoặc mắt bị che |
| 3 | `face13` (13 điểm mặt) | 13 tọa độ điểm mắt/mũi/miệng kèm cờ `occluded` / `outside` | Tính độ mở mắt (EAR), phát hiện ngáp qua độ mở miệng (MAR) và ước lượng góc xoay đầu 3D (yaw/pitch/roll) | Có, nhưng các điểm bị che bởi gọng/đốm phản xạ kính hoặc góc nghiêng phải gắn cờ `occluded`/`outside` thay vì đoán |
| 4 | `face.glasses` | `none` / `clear` / `sun` / `unknown` | Nhận biết tài xế đang đeo kính cận hay kính râm để kích hoạt nhánh xử lý mắt phù hợp, tránh nhầm đốm phản xạ/tròng tối thành nhắm mắt | Một phần — thấy rõ gọng kính trên NIR, nhưng kính râm có thể trông khá trong suốt trên NIR nên dễ khó phân biệt với `clear` |
| 5 | `hand` (`whose`, `side`, `on_wheel`) | `driver`/`passenger`; `left`/`right`; `yes`/`no`/`unknown` | Phát hiện tay rời vô lăng (hands-off-wheel), đánh giá mức sẵn sàng tiếp quản lái (TOR) trên xe ADAS/tự lái và phân biệt tay tài xế với tay hành khách | Có (nhận diện tốt qua hình dạng bàn tay và vị trí trên vô lăng), trừ vùng quá tối hoặc bị che khuất |
| 6 | `phone` (`state`) | `held` / `mounted` / `lap` | Phát hiện tài xế cầm hoặc thao tác điện thoại gây mất tập trung, phân biệt với điện thoại gắn trên giá đỡ hợp lệ | Một phần — nhận qua hình dáng cạnh, góc hoặc màn hình sáng; dễ nhầm với vật tối cầm tay nếu không thấy rõ đường nét máy |
| 7 | `person.seatbelt` | `worn_correct` / `worn_incorrect` / `not_worn` / `unknown` | Cảnh báo tài xế chưa thắt dây hoặc thắt dây sai cách (dưới nách, lệch vai) để đảm bảo an toàn khi va chạm | Một phần — thấy rõ khi dây tương phản độ sáng với áo, nhưng dễ chìm vào áo tối màu hoặc bị tay che → `unknown` |
| 8 | `frame_meta.camera_blocked` | `yes` / `no` | Phát hiện camera DMS bị che khuất, bẩn hoặc lóa bất thường để cảnh báo lỗi hệ thống và tránh đưa ra cảnh báo sai | Có (dễ nhận ra khi khung hình bị che tối, mất chi tiết toàn phần hoặc có vật chắn sát ống kính) |

## OMS (ít nhất 6 lớp)

| # | Lớp / thuộc tính | Giá trị | Để làm gì (chức năng OMS) | Nhìn thấy được trên IR? |
|---|---|---|---|---|
| 1 | `person` + `seat` | `driver` / `front_passenger` / `rear_left` / `rear_middle` / `rear_right` | Đếm số hành khách và xác định đúng vị trí ghế theo xe để điều khiển cảnh báo dây an toàn, kích hoạt túi khí theo ghế và điều hòa vùng | Có (nhận diện rõ hình dáng cơ thể và vị trí ghế kể cả trong cabin tối sau khi cân chỉnh hiển thị) |
| 2 | `person.seatbelt` | `worn_correct` / `worn_incorrect` / `not_worn` / `unknown` | Giám sát tình trạng thắt dây an toàn của hành khách các hàng ghế để nhắc nhở an toàn trước và trong khi xe chạy | Khó hơn RGB khi trang phục tối màu và mất thông tin màu sắc; nhiều trường hợp khuất/tối phải ghi `unknown` |
| 3 | `face` (box mặt hành khách) | bounding box vùng mặt | Xác nhận đối tượng là người thật (tránh nhầm vật lớn), hỗ trợ đánh giá tư thế ngồi lệch/cúi sâu ảnh hưởng đến vùng bung túi khí (out-of-position) | Có nếu hành khách hướng mặt về camera; không vẽ nếu quay đi hoặc bị che gần hết |
| 4 | `child_seat` (`seat`, `occupied`) | `seat` theo xe; `occupied`: `no` / `unknown` | Nhận diện ghế trẻ em lắp trên từng vị trí ghế để kiểm soát túi khí và hỗ trợ nhắc kiểm tra ghế trẻ em | Có (thấy rõ khung và tựa đầu ghế trẻ em), nhưng lòng ghế tối/bị che khó xác định bên trong → `occupied = unknown` |
| 5 | `infant_seat` (`seat`, `facing`) | `seat` theo xe; `facing`: `rear` / `front` | Phát hiện nôi xách em bé và hướng lắp đặt (quay sau/quay trước) để tự động tắt túi khí phù hợp và hỗ trợ cảnh báo bỏ quên trẻ trong xe (CPD) | Có (hình dạng nôi và quai xách đặc trưng), nhưng ở góc khuất/tối có thể dễ nhầm với `child_seat` |
| 6 | `object` (`seat`, `type`) | `seat` theo xe; `type`: `bag` / `box` / `cup` / `other` | Phân biệt đồ vật đặt trên ghế với hành khách để tránh báo động giả nhắc thắt dây an toàn và nhắc lấy đồ bỏ quên khi rời xe | Có (nhận diện qua hình dáng, kích thước), nhưng thiếu màu sắc nên các vật ít đặc trưng dễ phải gán `other` |
| 7 | `frame_meta` (`modality`, `light`, `camera_blocked`) | `rgb`/`ir`; `day`/`night`/`unknown`; `yes`/`no` | Gắn siêu dữ liệu cảm biến để tách tập RGB/IR khi huấn luyện/đánh giá model OMS và phát hiện kịp thời camera bị che khuất | Có (`modality` theo nguồn camera; `camera_blocked` quan sát trực tiếp trên ảnh) |

## 2 câu hỏi leo thang

Câu hỏi bạn muốn hỏi người viết guideline (hoặc khách hàng) vì guideline v1.0 chưa trả lời. Mỗi câu kèm **tên khung** làm ví dụ.

1. Câu hỏi: Guideline v1.0 mô tả `infant_seat` là "ghế em bé (nôi xách)" và `child_seat` là "ghế trẻ em", nhưng thuộc tính `facing` của `infant_seat` lại cho phép cả `front` (quay trước). Vậy khi gặp ghế trẻ em cỡ nhỏ đặt quay về phía trước mà không rõ quai xách, tiêu chí hình học cụ thể nào dùng để phân biệt `infant_seat` với `child_seat`?
   Khung ví dụ: `G01 S07_rgb.png` (cùng `S07_ir.png`) và `G01 S08_rgb.png` (cùng `S08_ir.png`).
   Bạn tạm gán thế nào và vì sao: Mình tạm gán là `infant_seat` (`facing = front`) dựa theo phom ghế nhỏ gọn, đồng thời ghi nhận bất đồng trong `disagreement_log.csv` (bạn review gán `child_seat`) để hỏi coach chốt tiêu chí phân biệt chính thức trước khi cập nhật bản `v2`.

2. Câu hỏi: Trên ảnh DMS NIR góc nghiêng có kính cận bị phản xạ đèn LED, khi một điểm trong `face13` vẫn nằm bên trong khung hình nhưng bị che hoàn toàn (do sống mũi, góc mặt quay nghiêng hoặc lóa kính dày) đến mức không thể ước lượng chắc vị trí, người gán chỉ nên bật `outside`, chỉ bật `occluded`, hay được bật đồng thời cả hai cờ?
   Khung ví dụ: `G01 D01.png` (điểm `mouth_right` và vùng mắt dưới kính) và `G01 D03.png` (4 điểm mắt trái khuất khi tài xế quay mặt).
   Bạn tạm gán thế nào và vì sao: Ở `D03.png` (4 điểm mắt trái khuất hẳn không đoán được) mình bật `outside` và ghi `eye_left = unknown`; ở `D01.png` (vùng mắt ước lượng được vị trí sau tròng kính) mình bật `occluded` và ghi `eye_left/right = unknown` theo tinh thần mục 4.6, nhưng với `mouth_right` ở `D01.png` mình đã lỡ bật cả hai cờ và cần hỏi rõ quy ước ưu tiên giữa `occluded` và `outside`.
