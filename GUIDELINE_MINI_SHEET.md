# Phiếu quy tắc gán nhãn — Ngày 2

**Họ và tên: Nguyễn Thế Anh** <br>
**MSSV: 2A202602138** <br>
**Hình thức: Cá nhân** <br>
**Mã cặp: SOLO**

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

### Tình huống A — xe buýt hay xe van?

- Ảnh và mã vật thể: `drive_022`, vật thể xe lớn ở tiền cảnh.
- Dấu hiệu nhìn thấy: thân xe dài, nhiều cửa sổ và hình dáng xe khách.
- Quy tắc áp dụng: gán `bus` cho thân xe khách dài, nhiều cửa sổ hoặc hàng ghế; không gán xe van nhỏ.
- Quyết định: `bus`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Đánh dấu `review_state=needs_review`, ghi lại mã ảnh/vật thể và xin Lab Coach hỗ trợ; không đoán theo màu xe.

### Tình huống B — xe tải hay xe van/ô tô con?

- Ảnh và mã vật thể: `drive_038`, xe thùng ở khu vực trung tâm.
- Dấu hiệu nhìn thấy: có cabin và thùng hàng/ben tách biệt rõ.
- Quy tắc áp dụng: gán `truck` khi thấy thùng, ben, sàn hàng hoặc thiết bị công vụ rõ; không gán xe van kín một khối.
- Quyết định: `truck`.
- Nếu vẫn thiếu bằng chứng, bạn sẽ làm gì? Phóng to ảnh, giữ `needs_review` nếu chưa phân biệt được khoang hàng, rồi xin hỗ trợ thay vì gán sang `van` hoặc `car`.

### Tình huống C — bị che, bị mép ảnh cắt hay không đủ bằng chứng?

- Ảnh và mã vật thể: `drive_033` hoặc `drive_038`, chọn một xe nhỏ/bị che khi rà ảnh ở độ phóng 100%.
- Dấu hiệu nhìn thấy khi phóng 100%: chỉ thấy một phần thân xe hoặc xe bị phương tiện khác che; cần phân biệt phần bị che với phần bị mép ảnh cắt.
- Giá trị `visibility`: `occluded` nếu còn nhận ra phương tiện nhưng bị vật thể khác che; `unclear` nếu không đủ bằng chứng để phân lớp.
- Giá trị `boundary`: `truncated` chỉ khi hộp chạm mép ảnh và vật thể bị mép ảnh cắt; nếu không thì `inside`.
- Trạng thái `review_state`: `needs_review` khi còn nghi ngờ, `confident` chỉ khi dấu hiệu và hộp đã rõ.
- Lý do: không suy đoán phần bị che hoặc phần nằm ngoài ảnh; vẽ theo phần nhìn thấy và ghi lại quyết định cần xem lại.

## 6. Xác nhận tự kiểm tra

- [x] Đã rà đủ bốn ảnh.
- [x] Đã kiểm vật thể thiếu và trùng.
- [x] Đã kiểm lớp và hình học từng hộp.
- [x] Mỗi hộp có đủ ba thuộc tính.
- [ ] Đã xử lý mọi hộp `needs_review` — còn `18/144` hộp cần xem lại.
- [x] Đã hoàn thành ba tình huống trước khi xem nguồn đối chiếu.
- [ ] Nếu làm theo cặp, hai người đã xuất bài độc lập trước khi trao đổi. Không áp dụng vì làm cá nhân.
- [x] Nếu làm cá nhân, bài riêng đã được kiểm trước khi nhận bộ tham chiếu.
- [x] Số vật thể thực tế: `144` — 40–60 là mục tiêu khối lượng, không phải điểm cắt.
