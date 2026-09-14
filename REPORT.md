# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên:** Vũ Ngọc Huyền<br>
**MSSV:** 2A202602323<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`
- Bốn mã ảnh: `drive_008`, `drive_022`, `drive_033`, `drive_038`
- Số vật thể thực tế: `56`
- Mã SHA-256 của gói YOLO của bạn: `a5d6b0a7723d660a0a20db82dfb97dd13ede4887b9d5e728df7fe592ee9ecf66`
- Mã SHA-256 của gói CVAT gốc của bạn: `3dcd6c368b5df1811a592f200eb7c7f59ab0d99accf7ffee71628072594748a5`
- Nguồn đối chiếu: bộ tham chiếu do Lab Coach cấp (`day2-teaching-reference.zip`)
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Tôi đã hoàn thành việc gán nhãn cho cả bốn ảnh, tiến hành tự kiểm tra và xuất riêng hai gói dữ liệu YOLO và CVAT trước khi nhận bộ nhãn tham chiếu từ Lab Coach. Gói YOLO của tôi cũng đã được khóa và ghi nhận bằng mã SHA-256 trước khi bắt đầu quá trình đối chiếu. Trong suốt quá trình gán nhãn ban đầu, tôi không xem, sao chép hay sử dụng bất kỳ thông tin nào từ bộ nhãn tham chiếu. Bộ nhãn tham chiếu chỉ được sử dụng ở bước sau để phân tích và xác định các khác biệt về phạm vi, phân loại lớp và hình học của bounding box. Do đó, kết quả gán nhãn ban đầu hoàn toàn phản ánh các quyết định độc lập của tôi.

## 2. Quyết định phân lớp

| Ảnh/vật thể | Lớp | Dấu hiệu nhìn thấy | Quy tắc áp dụng |
| --- | --- | --- | --- |
| Bus 13, xe khách lớn ở phía trước (ảnh `drive_033`) | `bus` | Xe có thân dài, kích thước lớn, nhiều ô cửa sổ và thiết kế đặc trưng để vận chuyển nhiều hành khách | Gán `bus` khi quan sát thấy đặc điểm của xe khách như thân xe kéo dài, nhiều cửa sổ hoặc nhiều khu vực ghế; không chuyển sang `van` chỉ vì xe có dạng hình hộp |
| Van 16, xe thân hộp (ảnh `drive_033`) | `van` | Thân xe hình hộp liền khối, không có phần đầu xe nhô ra rõ rệt như xe con (đối chiếu với car 15 cùng ảnh) | Gán `van` khi thân xe liền khối dạng hộp; phân biệt với `car` dựa vào việc car có kiểu dáng thon/đường nét cong hơn thay vì hộp liền khối |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Một phương tiện có thể được xác định là lớp `car` dựa trên kiểu dáng và cấu tạo của nó. Tuy nhiên, nếu một phần chiếc xe bị phương tiện khác chắn mất thì thuộc tính có thể được ghi nhận là `visibility=occluded`. Như vậy, **lớp** dùng để xác định vật thể thuộc loại nào, trong khi **thuộc tính** phản ánh trạng thái quan sát của vật thể, chẳng hạn bị che khuất, nằm sát mép ảnh hoặc cần được kiểm tra thêm. Việc thay đổi thuộc tính không đồng nghĩa với việc thay đổi lớp của vật thể.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa | Loại lỗi | Cách phát hiện | Sau khi sửa và quy tắc |
| --- | --- | --- | --- |
| Hộp `car` (car 15) trong ảnh `drive_022` được gán `confident` | lớp/thuộc tính | Rà lại ảnh ở mức phóng to, nhận thấy hình dáng thân xe có thể là dạng hộp liền khối thay vì đường nét cong của xe con, không đủ chắc chắn để giữ nguyên phân loại ban đầu | Giữ nguyên lớp `car`, đổi `review_state` từ `confident` sang `needs_review`; áp dụng quy tắc: chỉ gán `van` khi quan sát rõ thân xe dạng hộp liền khối, không có phần đầu xe nhô ra như xe con; chỉ gán `car` khi thân xe có đường nét cong, thon theo kiểu dáng ô tô con thông thường. Khi không đủ căn cứ phân biệt rõ giữa hai lớp này (ảnh nhỏ/mờ/góc chụp khó xác định), chuyển sang `needs_review` thay vì tự ý đổi lớp mà không chắc chắn — để dành xác nhận ở bước đối chiếu hoặc xin ý kiến Lab Coach, tránh gán sai lớp dựa trên suy đoán |

- Số hộp `needs_review` trước và sau khi kiểm: *[CHƯA ĐIỀN — cần đếm lại trong CVAT bằng bộ lọc review_state=needs_review, hoặc ước lượng nếu nhớ được]*
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: hộp `car` (car 15) trong ảnh `drive_022`, nghi ngờ có thể là `van` do đặc điểm thân xe dạng hộp liền khối; tôi giữ nguyên lớp hiện tại và chuyển `review_state=needs_review` thay vì tự ý đổi lớp, để xin xác nhận từ Lab Coach hoặc đối chiếu với nguồn tham khảo trước khi kết luận.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `2 0.383555 0.723492 0.444641 0.364953`
- Tên lớp và tọa độ điểm ảnh `xyxy`: lớp `2` là `bus`; tâm chuẩn hóa `(0.3836, 0.7235)`, kích thước chuẩn hóa `(0.4446, 0.3650)`; quy đổi sang pixel `xyxy = [103.2, 346.2, 387.8, 579.8]` trên ảnh kích thước 640 × 640.
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Một dòng nhãn có đủ năm giá trị và tọa độ nằm trong khoảng hợp lệ (0-1) chỉ chứng minh rằng dòng đó đọc được đúng cú pháp, không chứng minh nội dung gán nhãn là chính xác. Người gán nhãn vẫn có thể mắc các loại lỗi khác nhau dù định dạng hoàn toàn đúng: chọn nhầm mã lớp (ví dụ gán `2` cho một xe thực chất là `van` thay vì `bus`), gán nhãn cho một vật thể nằm ngoài phạm vi cho phép của bài (người đi bộ, xe máy), bỏ sót vật thể cần gán nhãn, hoặc vẽ hộp sai về mặt hình học — như hộp bao trùm quá nhiều nền xung quanh xe, hoặc bị cắt mất phần thân xe thực sự nhìn thấy được. Vì vậy, việc kiểm tra định dạng chỉ là điều kiện cần, không phải điều kiện đủ để đảm bảo chất lượng nhãn.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện: `drive_022`, `drive_033`, `drive_038`
- Mã ảnh thẩm định: `drive_008`
- Thông số lần chạy: Ultralytics `8.4.145`, seed `42`, cấu hình `8` epoch (dừng sớm ở epoch 4 do `patience=3` không cải thiện), thiết bị `CPU`.
- Mô tả một dự đoán trong `detect_result.jpg`: Trên ảnh thẩm định `drive_008` (1 ảnh, 12 vật thể thật), kết quả huấn luyện cho `Precision=0.00251`, `Recall=0.167`, `mAP50=0.00859`, `mAP50-95=0.00504` — các chỉ số này gần như bằng 0 và không thay đổi qua các epoch, cho thấy mô hình gần như chưa học được đặc trưng phân biệt giữa các lớp và hầu như không phát hiện đúng vật thể nào có ý nghĩa trên ảnh thẩm định.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Kết quả này chủ yếu phản ánh hạn chế về quy mô huấn luyện (chỉ 3 ảnh, 8 epoch, dừng sớm ở epoch 4) chứ chưa hẳn phản ánh lỗi trong nhãn. Tuy nhiên, đây cũng là dịp để rà lại nhất quán giữa các lớp ít mẫu (`truck`, `van`) giữa 3 ảnh huấn luyện, vì với dữ liệu quá nhỏ, một vài nhãn không nhất quán có thể ảnh hưởng lớn đến khả năng học của mô hình.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Việc chạy lại với nhiều epoch hơn, hoặc trên tập dữ liệu lớn hơn với cùng bộ nhãn, có thể cho thấy mô hình vẫn học được nếu có đủ dữ liệu — khi đó nguyên nhân chính là do kích thước tập huấn luyện quá nhỏ (chỉ 3 ảnh) chứ không phải lỗi nhãn.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Với chỉ 3 ảnh huấn luyện và 1 ảnh thẩm định thuộc cùng một bộ dữ liệu nhỏ, dừng huấn luyện chỉ sau 4 epoch, các chỉ số mAP/Precision/Recall gần như bằng 0 không phản ánh khả năng phát hiện vật thể của kiến trúc YOLO11n nói chung — chúng chỉ cho thấy dữ liệu quá ít để mô hình học hội tụ trong phạm vi thí nghiệm này. Đây là tín hiệu chẩn đoán đường ống kỹ thuật (pipeline hoạt động đúng, dữ liệu được đọc và train được), không phải phép đánh giá năng lực mô hình trong điều kiện thực tế.

## 6. Đối chiếu nhãn

- Số hộp ghép được: `41`
- IoU trung bình và trung vị: trung bình `0.805974`; trung vị `0.812003`
- Mức đồng thuận lớp: `0.682927`, tương đương `68.3%` trong các cặp đã ghép
- Số hộp phía bạn không ghép được: `15`
- Số hộp phía đối chiếu không ghép được: `9`
- Một điểm khác biệt cụ thể: *[CHƯA ĐIỀN — cần xác nhận lại 1 case cụ thể từ comparison_overlay.png hoặc comparison_iou.csv, ví dụ 1 hộp màu đỏ đơn lẻ không ghép được ở ảnh drive_033]*
- Quy tắc hoặc hành động sửa phát sinh: *[CHƯA ĐIỀN — mô tả bạn sẽ làm gì với case trên: giữ nguyên và ghi chú xin Lab Coach xác nhận, hay sửa lại nhãn]*
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? Mức đồng thuận đo lường mức độ hai nguồn (tôi và bộ tham chiếu) đồng ý với nhau, nhưng cả hai nguồn đều có thể cùng mắc chung một sai lầm — ví dụ cùng bỏ sót cùng một vật thể, hoặc cùng áp dụng sai một quy tắc phân loại nào đó. Đồng thuận cao chỉ chứng minh tính nhất quán giữa hai người gán nhãn, không chứng minh tính đúng đắn tuyệt đối so với thực tế. Ngoài ra, chỉ số này chỉ được tính trên 41 hộp đã ghép được — nó không giải thích được vì sao có tới 15 hộp phía tôi và 9 hộp phía đối chiếu không ghép được, đây mới chính là phần cần xem xét kỹ hơn.

## 7. Kiểm tra kho GitHub cá nhân

- [ ] Có phiếu quy tắc với ba tình huống mơ hồ.
- [ ] Có kết quả kiểm hai gói xuất.
- [ ] Có thông tin lần huấn luyện và ảnh dự đoán.
- [ ] Có tóm tắt, bảng và ảnh phủ của bước đối chiếu.
- [ ] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [ ] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất trong bài là hai gói xuất YOLO và CVAT gốc khớp nhau gần như tuyệt đối, với chỉ số `minimum_cross_format_iou = 0.9999630259815129` trong kết quả kiểm tra nhất quán chéo — điều này chứng minh cả hai định dạng đều được xuất từ cùng một trạng thái dữ liệu trong CVAT, không có chỉnh sửa xen giữa hai lần xuất. Hai câu hỏi còn lại tôi muốn xin ý kiến Lab Coach: thứ nhất, với trường hợp hộp car 15 trong ảnh `drive_022` mà tôi nghi ngờ có thể là `van` thay vì `car` do đặc điểm thân xe dạng hộp liền khối, đâu là dấu hiệu quyết định để phân biệt dứt khoát giữa hai lớp này trong trường hợp mơ hồ tương tự? Thứ hai, kết quả huấn luyện thử cho mAP50 và mAP50-95 rất thấp (gần bằng 0) và không cải thiện qua các epoch — đây có phải là dấu hiệu bất thường cần lưu ý về chất lượng nhãn hay chỉ đơn thuần phản ánh hạn chế của việc huấn luyện trên tập dữ liệu quá nhỏ (3 ảnh, 8 epoch)?
