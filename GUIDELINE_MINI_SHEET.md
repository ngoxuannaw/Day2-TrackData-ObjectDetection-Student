# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Ngô Xuân Nam<br>
**MSSV:** 2A202602193<br>
**Hình thức:** cá nhân <br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

|  Mã | Lớp              | Gán khi nhìn thấy                                       | Không gán vào lớp này                             |
| --: | ---------------- | ------------------------------------------------------- | ------------------------------------------------- |
|   0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
|   1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng      | ô tô con; thân xe buýt; xe van kín một khối       |
|   2 | `bus` (xe buýt)  | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế           | xe van nhỏ; xe tải; ô tô con                      |
|   3 | `van` (xe van)   | thân hộp nhỏ, kín, dùng chở người hoặc hàng             | thân xe buýt; khoang hàng tách biệt như xe tải    |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính                          | Giá trị                                                 | Ý nghĩa                             |
| ----------------------------------- | ------------------------------------------------------- | ----------------------------------- |
| `visibility` (mức nhìn thấy)        | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy            |
| `boundary` (quan hệ mép ảnh)        | `inside` (trong ảnh), `truncated` (bị cắt)              | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại)      | đánh dấu quyết định cần quay lại    |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

Nguồn kiểm tra: bốn ảnh gốc và `annotations.xml` trong bản xuất `job_1_dataset_2026_09_14_08_24_40_cvat for images 1.1 (1).zip` (Job 1). Các mã `A-01`, `B-01`, `C-01` là mã tham chiếu trong phiếu, không phải ID trên giao diện CVAT. Số thứ tự hộp dưới đây tính từ 1 trong từng ảnh của XML; tọa độ ghi theo `(xtl, ytl, xbr, ybr)`, đơn vị pixel.

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_022.jpg` — `BUS-35`, xe buýt ở tiền cảnh, phía dưới bên trái; hộp thứ 5 trong XML, tọa độ `(117.32, 350.64, 383.73, 575.70)`.
- Dấu hiệu nhìn thấy: Thân xe khách dài, dãy cửa sổ lớn liên tiếp dọc hông xe và kính chắn gió phía trước lớn. Các dấu hiệu cấu trúc này phù hợp với xe buýt; không dựa riêng vào màu xe hay kích thước hộp.
- Quy tắc áp dụng: Gán `bus` khi thấy thân xe khách dài, nhiều cửa sổ hoặc nhiều hàng ghế; không gán `van` nếu vật thể có thân xe buýt.
- Quyết định: Lớp `bus` (mã lớp `2`).
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng ảnh lên 100%, đặt `review_state=needs_review`, ghi rõ dấu hiệu còn thiếu vào nhật ký quyết định và nhờ Lab Coach kiểm tra; không đoán lớp.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038.jpg` — `TRUCK-70`, xe cứu hộ màu trắng ở giữa phần dưới ảnh; hộp thứ 5 trong XML, tọa độ `(285.79, 342.54, 477.30, 490.48)`.
- Dấu hiệu nhìn thấy: Sau cabin có sàn và thiết bị kéo/cẩu chuyên dụng lộ rõ; khoang chở hàng không phải thân hộp kín liền khối.
- Quy tắc áp dụng: Gán `truck` khi thấy sàn hàng hoặc thiết bị công vụ rõ ràng; không gán `van` cho xe có khoang/thiết bị phía sau tách biệt và không gán `car` cho xe công vụ dạng tải.
- Quyết định: Lớp `truck` (mã lớp `1`).
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng ảnh lên 100%, đặt `review_state=needs_review`, mô tả phần thùng/sàn hoặc thiết bị chưa nhìn rõ và xin Lab Coach hỗ trợ; không suy đoán theo màu hay kích thước hộp.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_033.jpg` — `BUS-61`, xe buýt màu đỏ ở góc dưới bên trái; hộp thứ 26 trong XML, tọa độ `(0.00, 431.35, 184.00, 640.00)`.
- Dấu hiệu nhìn thấy khi phóng 100%: Thân xe dài và các cửa sổ của xe buýt nhìn rõ, nhưng mép trái và mép dưới của ảnh cắt mất một phần thân xe; không có phương tiện khác che phần đang nhìn thấy.
- Giá trị `visibility`: `clear`.
- Giá trị `boundary`: `truncated`.
- Trạng thái `review_state`: `confident`.
- Lý do: Bằng chứng phân lớp vẫn rõ nên gán `bus`; phần thiếu là do mép ảnh cắt chứ không phải bị vật thể khác che. Hộp chỉ ôm sát phần xe nhìn thấy trong ảnh, không ước lượng phần nằm ngoài ảnh.

## 6. Xác nhận tự kiểm tra

- [X] Đã rà đủ bốn ảnh.
- [X] Đã kiểm vật thể thiếu và trùng.
- [X] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính — đã kiểm tra đủ trên 99/99 hộp trong bản xuất XML.
- [X] Đã xử lý mọi hộp `needs_review` — bản xuất hiện còn 12 hộp cần xem lại trong CVAT.
- [X] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [X] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [X] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: **99 hộp trong bản xuất CVAT hiện tại** (`drive_008`: 30; `drive_022`: 5; `drive_033`: 30; `drive_038`: 34) — 40–60 là mục tiêu khối lượng, không phải điểm cắt. Số này bao gồm 12 hộp `needs_review`; cần cập nhật nếu số hộp thay đổi sau khi tự kiểm tra và xuất lại.
