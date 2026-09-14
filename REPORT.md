# Báo cáo — Ngày 2: phát hiện vật thể

**Họ và tên: Nguyễn Thế Anh** <br>
**MSSV: 2A202602138** <br>
**Hình thức: Cá nhân** <br>
**Mã cặp: SOLO**

## 1. Bài độc lập và nguồn dữ liệu

- Mã SHA-256 của ZIP ảnh được cấp: `f7d99888f21440fb0374d84962b93213bd8c14e665d093cc8d37f4c61b71ed33`.
- Bốn mã ảnh: `drive_022` (`cb8297af4cc5bb660f9d56f0e31ed77f8fa1f95b31f47bb8b8a1cd08b2f94886`), `drive_033` (`8dc05a7a8f06ed137d08b643ef676629a2c4cf3b25051852de22e7400311e465`), `drive_038` (`35294a107b157646e0619ad985226ef424968d7798f90f7ded5b32f32cebbb86`), `drive_008` (`fde088a7a955343bb3366008d31e3c2a6ecf6ddbfd08e58ecd91f5e6d67a05d1`).
- Số vật thể thực tế: `144` trong gói của bạn (drive_022: 5; drive_033: 35; drive_038: 72; drive_008: 32), vượt khoảng mục tiêu 40–60. Bộ tham chiếu có `50` hộp.
- Mã SHA-256 của gói YOLO của bạn: `0f58a6db0665aff3e6a58999ac55b422017b9fa6db48853c139e7987c8673d4b`.
- Mã SHA-256 của gói CVAT gốc của bạn: `c68be558b291142054b3d8dea6af7b4eaff6b7f6523ad12c7cff9300d84529d3`.
- Nguồn đối chiếu: bộ nhãn đối chiếu do Lab Coach cấp, `release_id=day2-reference-4img-v1`.
- Mã SHA-256 của gói đối chiếu: `c8bbc767d8bb9a29f4ca5abf0c3516e5c2af94c58143a980b0148cfe0b500d2b`.
- Nếu làm cá nhân, ghi mã lần phát và thời điểm nhận bộ tham chiếu: mã lần phát `day2-reference-4img-v1`; nhận ngày `14/09/2026`.

Giải thích vì sao bài của bạn vẫn độc lập trước khi đối chiếu:

Bài làm được thực hiện và kiểm tra trước khi nhận bộ tham chiếu, theo quy trình độc lập của notebook.

## 2. Quyết định phân lớp

| Ảnh/vật thể                               | Lớp     | Dấu hiệu nhìn thấy                                             | Quy tắc áp dụng                                                                                                          |
| ----------------------------------------- | ------- | -------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `drive_022` — xe buýt lớn ở tiền cảnh     | `bus`   | Thân xe dài, nhiều cửa sổ và hình dáng xe khách rõ             | Gán `bus` cho thân xe khách dài, nhiều cửa sổ hoặc hàng ghế; không gán xe van nhỏ, xe tải hoặc ô tô con                  |
| `drive_038` — xe tải thùng đỏ ở trung tâm | `truck` | Có cabin và thùng hàng/ben tách biệt rõ                        | Gán `truck` khi thấy thùng, ben, sàn hàng hoặc thiết bị công vụ rõ; không gán ô tô con, xe buýt hoặc xe van kín một khối |
| `drive_008` — xe con màu bạc ở tiền cảnh  | `car`   | Thân xe con, không có thùng hàng tách biệt                     | Gán `car` cho sedan, hatchback, SUV, taxi hoặc xe bán tải dùng như xe con                                                |
| Vật thể dạng thân hộp nhỏ, kín            | `van`   | Dùng chở người hoặc hàng, không có đặc điểm xe buýt hay xe tải | Gán `van` cho thân hộp nhỏ, kín; không gán thân xe buýt hoặc khoang hàng tách biệt như xe tải                            |

Nêu một ví dụ cho thấy lớp và thuộc tính là hai loại thông tin khác nhau:

Một xe bị che khuất vẫn có thể có lớp `car` nếu hình dáng còn đủ bằng chứng, nhưng thuộc tính `visibility=occluded` ghi mức nhìn thấy. Lớp mô tả loại phương tiện; thuộc tính mô tả trạng thái quan sát. Các lớp cố định trong notebook là `car`, `truck`, `bus`, `van`, còn thuộc tính là `visibility`, `boundary` và `review_state`.

## 3. Tự kiểm tra và sửa nhãn

| Trước khi sửa                                                                | Loại lỗi         | Cách phát hiện                   | Sau khi sửa và quy tắc                                                                                  |
| ---------------------------------------------------------------------------- | ---------------- | -------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Trước kiểm: có 144 hộp, vượt mục tiêu 40–60 và khác 50 hộp của bộ tham chiếu | phạm vi/hình học | So sánh số hộp theo ảnh và audit | Chưa xác định hộp cụ thể đã sửa; cần rà hộp nhỏ/xa và vùng mép ảnh theo quy tắc mỗi phương tiện một hộp |

- Số hộp `needs_review` trước và sau khi kiểm: trước kiểm **chưa xác định**; sau kiểm `18/144` (`confident=126`, `needs_review=18`).
- Một quyết định chưa đủ bằng chứng và cách bạn xin hỗ trợ: các xe nhỏ, bị che hoặc mờ trong `drive_033`/`drive_038`; giữ `review_state=needs_review`, ghi image ID và số hộp, gửi ảnh phủ cùng câu hỏi cho Lab Coach thay vì đoán lớp.

## 4. Một dòng nhãn YOLO

- Dòng `class x_center y_center width height`: `0 0.659297 0.132719 0.037969 0.054250` (dòng đầu của `drive_008.txt`).
- Tên lớp và tọa độ điểm ảnh `xyxy`: class `0` là `car`; với ảnh 640x640, tọa độ là `[409.8, 67.6, 434.1, 102.3]` pixel.
- Vì sao dòng đúng định dạng vẫn có thể sai lớp, phạm vi hoặc hình học?

Dòng có thể đúng cú pháp và đủ 5 trường nhưng vẫn sai `class_id`, chọn nhầm loại phương tiện, bao phủ nhiều nền/nhiều xe hoặc không biểu diễn đúng phần vật thể nhìn thấy. Bộ kiểm tra chỉ xác nhận định dạng số, biên và kích thước hộp; không chứng minh quyết định gán nhãn bằng mắt là đúng.

## 5. Huấn luyện và dự đoán thử

- Ba mã ảnh huấn luyện theo manifest trong notebook: `drive_022`, `drive_033`, `drive_038`.
- Mã ảnh thẩm định theo manifest trong notebook: `drive_008`.
- Mô tả một dự đoán trong `detect_result.jpg`: dự đoán được thực hiện trên `drive_008` với confidence `0.25`; chưa có đủ chi tiết để mô tả từng hộp.
- Dự đoán đó gợi ý cần kiểm lại quy tắc hoặc dữ liệu nào? Rà phạm vi hộp, vật thể nhỏ/xa và sự phân bố lớp; kết quả đạt `mAP50=0.00687`, `mAP50-95=0.00196`.
- Minh chứng nào có thể bác bỏ nhận định của bạn? Đối chiếu ảnh dự đoán và confidence với nhãn `drive_008`.
- Vì sao kết quả trên bốn ảnh không phải phép đánh giá mô hình dùng thực tế? Chỉ có 3 ảnh train và 1 ảnh validation, mẫu không đại diện; thử nghiệm chỉ nhằm chẩn đoán dữ liệu. Notebook cũng nêu rõ mAP/IoU trong bài này không phải phép đánh giá triển khai thực tế.

Thiết lập: Ultralytics `8.4.145`, `yolo11n.pt`, `epochs=8`, `seed=42`, `imgsz=640`, `batch=4`, `freeze=10`, `patience=3`, GPU `0`. Huấn luyện dừng sớm sau 4 epoch; kết quả cuối là `mAP50=0.00687`, `mAP50-95=0.00196`.

## 6. Đối chiếu nhãn

- Số hộp ghép được: `48`.
- IoU trung bình và trung vị: `0.872844` và `0.891609`.
- Mức đồng thuận lớp: `0.729167` (`72.9167%`).
- Số hộp phía bạn không ghép được: `96`.
- Số hộp phía đối chiếu không ghép được: `2`.
- Một điểm khác biệt cụ thể: gói của bạn có `144` hộp, còn bộ tham chiếu có `50`; chênh lệch lớn nhất theo ảnh nằm ở `drive_038` (`72` hộp của bạn so với `17` hộp tham chiếu).
- Quy tắc hoặc hành động sửa phát sinh: ghép tối ưu theo IoU hình học với floor kỹ thuật `0.01`, không dùng lớp khi ghép; cần rà phạm vi và hộp nhỏ/xa theo quy tắc mỗi phương tiện một hộp. Chưa có log xác nhận hộp cụ thể đã được sửa.
- Vì sao mức đồng thuận cao không chứng minh mọi nhãn đều đúng? Hai bên có thể cùng áp dụng sai một quy tắc hoặc cùng bỏ sót vật thể; mức đồng thuận chỉ đo sự nhất quán với nguồn đối chiếu, không phải chân lý nhãn hay chất lượng sản xuất.

Bộ đối chiếu chỉ đo mức nhất quán giữa hai bộ nhãn, không chứng minh mọi nhãn đều đúng. Nguồn đối chiếu cũng có thể có lỗi, và việc ghép theo IoU chỉ đánh giá các hộp được ghép theo tiêu chí kỹ thuật đã chọn.

## 7. Kiểm tra kho GitHub cá nhân

- [ ] Có phiếu quy tắc với ba tình huống mơ hồ.
- [x] Có kết quả kiểm hai gói xuất: hai ZIP xuất thực tế và annotations XML hiện có.
- [x] Có thông tin lần huấn luyện và ảnh dự đoán.
- [x] Có tóm tắt và ảnh phủ của bước đối chiếu.
- [ ] Không có gói xuất thô, bộ nhãn tham chiếu hoặc trọng số mô hình.
- [ ] Không có dữ liệu VinFast/khách hàng/ảnh cá nhân/mật khẩu/mã truy cập.

Minh chứng mạnh nhất trong bài và câu hỏi còn lại cho Lab Coach:

Minh chứng mạnh nhất là hai gói xuất có checksum xác định, gói CVAT có 144 hộp với đủ ba thuộc tính, và bộ tham chiếu có 50 hộp. Câu hỏi còn lại cho Lab Coach: cần giữ hay loại các hộp dư trong `drive_038` theo quy tắc phạm vi nào?
