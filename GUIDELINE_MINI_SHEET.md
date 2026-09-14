# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên:** Vũ Ngọc Huyền<br>
**MSSV:** 2A202602323<br>
**Hình thức:** cá nhân<br>
**Mã cặp:** SOLO

## 1. Phạm vi

- Chỉ gán phương tiện thuộc bốn lớp bên dưới.
- Mỗi phương tiện là một hộp; không gộp nhiều xe.
- Không gán người, xe máy, xe đạp, biển báo hoặc phần phản chiếu.
- Vật thể quá nhỏ hoặc mờ đến mức không thể phân lớp có căn cứ: không đoán; ghi lý do vào nhật ký quyết định.

## 2. Bốn lớp cố định

| Mã | Lớp | Gán khi nhìn thấy | Không gán vào lớp này |
| ---: | --- | --- | --- |
| 0 | `car` (ô tô con) | sedan, hatchback, SUV, taxi, xe bán tải dùng như xe con | xe có thùng/ben rõ; thân xe buýt; xe van thân hộp |
| 1 | `truck` (xe tải) | thùng, ben, sàn hàng hoặc thiết bị công vụ rõ ràng | ô tô con; thân xe buýt; xe van kín một khối |
| 2 | `bus` (xe buýt) | thân xe khách dài, nhiều cửa sổ hoặc hàng ghế | xe van nhỏ; xe tải; ô tô con |
| 3 | `van` (xe van) | thân hộp nhỏ, kín, dùng chở người hoặc hàng | thân xe buýt; khoang hàng tách biệt như xe tải |

Thứ tự lớp là cố định: `0 car, 1 truck, 2 bus, 3 van`.

## 3. Hộp giới hạn

- Vẽ sát phần vật thể nhìn thấy.
- Không ước lượng phần bị xe khác che.
- Vật thể chạm mép ảnh vẫn được gán nếu đủ bằng chứng phân lớp.
- Không để hộp chứa nhiều nền hoặc nhiều phương tiện.

## 4. Ba thuộc tính

| Thuộc tính | Giá trị | Ý nghĩa |
| --- | --- | --- |
| `visibility` (mức nhìn thấy) | `clear` (rõ), `occluded` (bị che), `unclear` (không rõ) | mức bằng chứng nhìn thấy |
| `boundary` (quan hệ mép ảnh) | `inside` (trong ảnh), `truncated` (bị cắt) | vật thể có bị mép ảnh cắt hay không |
| `review_state` (trạng thái xem lại) | `confident` (tự tin), `needs_review` (cần xem lại) | đánh dấu quyết định cần quay lại |

YOLO không lưu ba thuộc tính này. Vì vậy phải xuất thêm `CVAT for images 1.1` từ cùng công việc.

## 5. Ba tình huống mơ hồ

Hoàn thành trước khi xem bài của người khác hoặc bộ nhãn tham chiếu.

### Tình huống A — xe buýt hay xe van?

*Lưu ý: trường hợp thực tế gặp phải là lưỡng lự giữa `car` và `van`, không phải giữa `bus` và `van`; ghi lại đúng tình huống đã trải qua trong quá trình gán nhãn.*

- Ảnh và mã vật thể: `car 40`, ảnh `drive_008` (ảnh thứ 4)
- Dấu hiệu nhìn thấy: thân xe hơi vuông, góc chụp khó nhìn rõ hình dáng nên dễ gây hiểu nhầm giữa `car` và `van`
- Quy tắc áp dụng: chỉ gán `van` khi quan sát rõ thân xe dạng hộp liền khối, không có phần đầu xe nhô ra như xe con; chỉ gán `car` khi thân xe có đường nét cong, thon theo kiểu dáng ô tô con thông thường
- Quyết định: gán lớp `car`, vì dấu hiệu quan sát được (dù thân xe hơi vuông) vẫn nghiêng về đặc điểm ô tô con hơn là thân hộp liền khối điển hình của van
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Chuyển `review_state` sang `needs_review`, ghi lại lý do phân vân và xin xác nhận từ Lab Coach hoặc đối chiếu với nguồn tham khảo trước khi kết luận cuối cùng

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `car 15` / `van 16`, ảnh `drive_022`
- Dấu hiệu nhìn thấy: `van 16` có thân xe dạng hộp liền khối, không có phần đầu xe nhô ra rõ rệt; `car 15` có đường nét thân xe cong, thon hơn nhưng vẫn còn điểm gây phân vân giữa hai lớp
- Quy tắc áp dụng: Chỉ gán `van` khi quan sát rõ thân xe dạng hộp liền khối, không có phần đầu xe nhô ra như xe con; chỉ gán `car` khi thân xe có đường nét cong, thon theo kiểu dáng ô tô con thông thường
- Quyết định: `van 16` gán lớp `van`; `car 15` giữ lớp `car` nhưng chuyển `review_state` từ `confident` sang `needs_review` do chưa đủ chắc chắn
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Không tự ý đổi lớp khi chưa chắc chắn; chuyển sang `needs_review`, ghi lại lý do và xin xác nhận từ Lab Coach hoặc đối chiếu với nguồn tham khảo trước khi kết luận

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `car 44`, ảnh `drive_008` (ảnh thứ 4)
- Dấu hiệu nhìn thấy khi phóng 100%: phần đầu xe bị che khuất một phần, đồng thời xe nằm sát mép ảnh nên phần thân bị cắt bớt; phần còn lại nhìn thấy vẫn đủ để xác định là `car`
- Giá trị `visibility`: `occluded`
- Giá trị `boundary`: `truncated`
- Trạng thái `review_state`: `confident`
- Lý do: dù bị che phần đầu và bị mép ảnh cắt, phần thân xe còn nhìn thấy vẫn đủ rõ ràng để nhận diện chắc chắn là `car` mà không cần đoán thêm, nên không cần đánh dấu `needs_review`

## 6. Xác nhận tự kiểm tra

- [ ] Đã rà đủ bốn ảnh.
- [ ] Đã kiểm vật thể thiếu và trùng.
- [ ] Đã kiểm lớp và hình học từng hộp.
- [ ] Mỗi hộp có đủ ba thuộc tính.
- [ ] Đã xử lý mọi hộp `needs_review`.
- [ ] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi.
- [ ] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [ ] Số vật thể thực tế: `56` — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
