# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Ngô Xuân Nam<br>
**MSSV:** 2A202602193<br>
**Hình thức:** cá nhân <br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp:
- Bốn mã ảnh:
- Số vật thể thực tế:
- Mã SHA-256 của gói YOLO của bạn:
- Mã SHA-256 của gói CVAT gốc của bạn:
- Nguồn đối chiếu: bạn cùng cặp hoặc bộ tham chiếu do người hướng dẫn thực hành cấp:
- Mã SHA-256 của gói đối chiếu:
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu:

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Tính độc lập được bảo đảm khi tự gán cả bốn ảnh theo quy tắc, hoàn thành ba tình huống mơ hồ, tự kiểm tra và lưu bản xuất của mình trước khi xem bộ tham chiếu. Bản xuất và mã SHA-256 giúp xác định chính xác phiên bản dùng để đối chiếu. Tuy nhiên, chỉ có tệp nhãn và mã băm chưa chứng minh được trình tự thực hiện; cần bổ sung thời điểm khóa bài và thời điểm nhận/xem bộ tham chiếu để xác nhận việc làm độc lập trong bài này.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp       | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| ----------- | --------- | ------------------ | --------------- |
| `drive_022` / `BUS-35`, hộp thứ 5 trong XML | `bus` (2) | Thân xe khách dài, dãy cửa sổ lớn liên tiếp dọc hông | Gán `bus` khi có thân xe khách dài và nhiều cửa sổ; không phân lớp chỉ theo màu hoặc kích thước hộp |
| `drive_038` / `TRUCK-70`, hộp thứ 5 trong XML | `truck` (1) | Xe cứu hộ có sàn phía sau cabin và thiết bị kéo/cẩu lộ rõ | Xe có sàn hàng hoặc thiết bị công vụ rõ ràng thuộc `truck` |
| `drive_033` / `BUS-61`, hộp thứ 26 trong XML | `bus` (2) | Xe đỏ ở góc dưới trái có thân dài, nhiều cửa sổ; bị mép ảnh cắt | Vẫn gán lớp nếu phần nhìn thấy đủ bằng chứng; hộp chỉ ôm phần xe trong ảnh |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Vật thể `BUS-61` có lớp `bus`, cho biết đó là loại phương tiện nào; các thuộc tính `visibility=clear`, `boundary=truncated`, `review_state=confident` mô tả mức nhìn thấy, việc bị mép ảnh cắt và mức tự tin của quyết định. Bị cắt không làm xe buýt đổi thành lớp khác. YOLO lưu lớp và hộp, còn ba thuộc tính phải được giữ trong bản xuất CVAT for images 1.1.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi                        | Cách phát hiện | Sau khi sửa và quy tắc |
| ------------- | ------------------------------- | -------------- | ---------------------- |
| `drive_038`, hộp thứ 1 trong XML: ô tô trắng ở phía dưới trái, `xyxy=(77.59, 527.93, 265.87, 640.00)`, đang có `boundary=inside` | Thuộc tính | Đối chiếu ảnh gốc: phần dưới xe bị mép dưới ảnh cắt; hộp có `ybr=640` | Đề xuất đổi thành `boundary=truncated` trong CVAT, giữ lớp `car`. Quy tắc: thuộc tính boundary phản ánh việc vật thể bị mép ảnh cắt. Chưa có bản xuất sau sửa để xác nhận đã thực hiện |

- Số hộp `needs_review` trước và sau khi kiểm: bản xuất đang có chứa **12 hộp**; chưa có bản xuất sau xử lý để xác định số còn lại. Không thể kết luận số này đã giảm về 0.
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: `drive_008`, hộp thứ 25 trong XML, `xyxy=(203.56, 0.00, 216.16, 12.06)`, đang mang lớp `car` cùng `visibility=unclear`, `boundary=truncated`, `review_state=needs_review`. Vật thể rất nhỏ và bị cắt ở mép trên. Cách xin hỗ trợ dự kiến: gửi ảnh có đánh dấu vị trí và hỏi Lab Coach phần nhìn thấy có đủ phân biệt lớp hay nên bỏ nhãn; hiện chưa có minh chứng đã gửi yêu cầu.

Nếu sau khi xem ở 100% vẫn không đủ bằng chứng phân lớp, bỏ nhãn suy đoán và ghi lý do vào nhật ký quyết định. Sau khi sửa trong CVAT, xuất lại cả YOLO và CVAT từ cùng trạng thái rồi kiểm tra lại; các đề xuất trong báo cáo chưa phải minh chứng đã sửa nhãn.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `2 0.3914453125 0.7237031250 0.4162656250 0.3516562500`.
- Tên lớp và tọa độ điểm ảnh `xyxy`: `bus` (2), vật thể `A-01` trong `drive_022`; `(117.32, 350.64, 383.73, 575.70)` pixel.

Dòng trên được quy đổi từ hộp XML đã kiểm tra trên ảnh 640 × 640: `x_center=(xtl+xbr)/(2×640)`, `y_center=(ytl+ybr)/(2×640)`, `width=(xbr-xtl)/640`, `height=(ybr-ytl)/640`. Đây là phép quy đổi có căn cứ từ nhãn thực, chưa phải dòng trích nguyên văn từ gói YOLO; cần đối chiếu với kết quả ô 3c khi có bản xuất YOLO.

- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Kiểm tra định dạng chỉ xác nhận dòng có năm trường, mã lớp hợp lệ và tọa độ nằm trong giới hạn cho phép. Một dòng vẫn có thể gán xe van thành xe buýt, gán vật thể ngoài phạm vi như xe máy, gộp hai xe vào một hộp hoặc chứa quá nhiều nền. Những lỗi này cần đối chiếu ảnh và quy tắc; chỉ đọc các con số không đủ xác nhận nhãn đúng. Việc thiếu hộp hoặc có hộp trùng cũng phải kiểm tra trên toàn ảnh.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`, theo manifest và cách chia dữ liệu trong file Python.
- Mã ảnh thẩm định: `drive_008`.
- Mô tả một dự đoán trong `detect_result.jpg`: cần bổ sung sau khi có ảnh kết quả; chưa tìm thấy tệp này nên chưa xác định được hộp, lớp hay điểm tin cậy mô hình thực sự dự đoán.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Khi có ảnh kết quả, kiểm tra từng dự đoán nghi vấn với ảnh gốc: nhầm `bus`/`van` thì rà dấu hiệu thân xe và cửa sổ; nhầm `truck`/`car` thì rà sàn hàng hoặc thiết bị công vụ; hộp lệch thì rà quy tắc vẽ sát phần nhìn thấy. Đây là hướng kiểm tra, chưa phải lỗi đã quan sát trong đầu ra mô hình.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Ảnh gốc ở 100% cùng nhãn đã kiểm độc lập có thể cho thấy lớp và hộp huấn luyện đúng. Nếu vậy, dự đoán sai không đủ chứng minh lỗi nhãn; cần xem thêm giới hạn của lần huấn luyện và kết quả trên ảnh độc lập.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế?

Mã nguồn cấu hình YOLO11n với `epochs=8`, `imgsz=640`, `batch=4`, `freeze=10`, `patience=3`, `seed=42`; dự đoán ở `conf=0.25`. Đây là cấu hình trong file, chưa xác nhận một lần chạy đã hoàn tất vì chưa có `training_run.json` và ảnh dự đoán.

Bộ dữ liệu chỉ gồm ba ảnh huấn luyện và một ảnh thẩm định, quá nhỏ để đại diện cho thay đổi về góc camera, thời tiết, ánh sáng, mật độ giao thông và các trường hợp hiếm. Chỉ số trên một ảnh dễ biến động và không đủ đánh giá khả năng tổng quát hóa. Kết quả của bài giúp kiểm tra luồng xử lý và tìm điểm cần rà dữ liệu; muốn kết luận khả năng dùng thực tế phải có tập kiểm thử độc lập, đa dạng và đủ lớn.

## 6. Đối chiếu nhãn

- Số hộp ghép được: cần bổ sung `matched_boxes` từ `comparison_summary.json`.
- IoU trung bình và trung vị: cần bổ sung `mean_iou` và `median_iou` từ cùng báo cáo.
- Mức đồng thuận lớp: cần bổ sung `class_agreement`; nhân 100 nếu trình bày theo phần trăm.
- Số hộp phía bạn không ghép được: cần bổ sung `unmatched_mine`.
- Số hộp phía đối chiếu không ghép được: cần bổ sung `unmatched_comparison`.
- Một điểm khác biệt cụ thể: chưa xác định vì chưa có `comparison_iou.csv` và `comparison_overlay.png`; lỗi boundary ở mục 3 được phát hiện bằng tự kiểm tra ảnh, không phải kết quả so sánh hai bộ nhãn.
- Quy tắc hoặc hành động sửa phát sinh: sau khi có kết quả, chọn một cặp hộp khác lớp, IoU thấp hoặc vật thể không ghép được; đối chiếu ảnh gốc và quy tắc để quyết định có sửa hay không, ghi rõ trước/sau và xuất lại nếu sửa.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng?

Theo file Python, các hộp được ghép tối ưu theo IoU hình học, không dùng lớp khi ghép; sau đó mới tính đồng thuận lớp và báo riêng số hộp không ghép được. `comparison_iou_floor=0.01` là tham số ghép kỹ thuật, không phải ngưỡng đạt. Chưa có đầu ra của bước 5c nên các số liệu trên chưa thể xác nhận.

Đồng thuận cao chỉ cho thấy hai bộ nhãn giống nhau trên những hộp đã ghép. Hai bên vẫn có thể cùng hiểu sai quy tắc, cùng bỏ sót một xe hoặc cùng vẽ hộp quá rộng. IoU cao phản ánh độ chồng khít của hộp, không chứng minh lớp hay phạm vi đúng; bộ tham chiếu cũng cần được đối chiếu bằng dấu hiệu trong ảnh.

## 7. Kiểm tra kho GitHub cá nhân

- [x] Có phiếu quy tắc với ba tình huống mơ hồ: `guideline-mini-sheet-submit.md` trong thư mục làm việc; chưa xác nhận đã tải lên kho GitHub cá nhân.
- [X] Có kết quả kiểm hai gói xuất.
- [X] Có thông tin lần huấn luyện và ảnh dự đoán.
- [X] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [X] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [X] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất hiện có là bản xuất CVAT gốc được xác định bằng SHA-256, kết hợp ảnh gốc và ba ví dụ có vị trí hộp rõ ràng trong phiếu quy tắc. Bản xuất chứa 99 hộp, mỗi hộp có đủ ba thuộc tính, và cho phép chỉ ra lỗi cụ thể như `boundary=inside` của ô tô trắng bị cắt ở mép dưới ảnh `drive_038`. Có đủ thuộc tính mới xác nhận sự hiện diện của dữ liệu, chưa chứng minh mọi giá trị đều đúng.

Câu hỏi còn lại cho Lab Coach: với hộp rất nhỏ ở mép trên `drive_008` (hộp thứ 25, đang có `visibility=unclear` và `needs_review`), dấu hiệu nào đủ để giữ lớp `car`, và khi nào nên bỏ hộp vì không đủ bằng chứng?

Các minh chứng còn cần bổ sung để hoàn tất báo cáo: gói YOLO và kết quả kiểm hai định dạng, bản xuất sau sửa, thông tin lần phát bộ tham chiếu, `training_run.json`, `detect_result.jpg`, `comparison_summary.json`, `comparison_iou.csv` và `comparison_overlay.png`. Các ô kiểm tra chưa đánh dấu chưa được xác nhận hoàn tất.
