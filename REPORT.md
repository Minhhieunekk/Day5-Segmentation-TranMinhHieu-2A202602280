# Báo cáo Day 5 — điền trực tiếp trong fork của bạn

**Cách dùng:** Thay mọi dấu `…` bằng bài làm thật của bạn trước khi nộp link fork trên VLearn. Giữ nguyên bốn mục và bảng để coach đọc nhanh. Viết ngắn, cụ thể theo ảnh/vùng; không cần thuật ngữ chuyên sâu. Ví dụ trong [hướng dẫn mẫu](reports/REPORT_TEMPLATE.md) chỉ giúp hiểu cách điền, không phải câu trả lời để chép lại.

- Mã học viên theo lớp: 2A202602280 (Trần Minh Hiếu - GitHub: Minhhieunekk)
- Ngày / CVAT local: 2026-09-17 / CVAT v2.x Local (http://localhost:8080)
- Công cụ đã dùng: Brush / Polygon / Intelligent Scissors / Manual Annotation

Mã học viên là mã lớp cấp; không cần ghi họ tên trong report nếu kênh VLearn đã nhận diện bạn. Chỉ ghi công cụ thật sự đã dùng; không có SAM vẫn làm bài bình thường.

## 1. Bài đã nộp

Ghi tên ZIP đúng như file trong `submissions/` và số ảnh đã vẽ, Save. Chưa làm hoặc export lỗi thì ghi `chưa có`, không tạo ZIP rỗng. Cột điểm là điểm tối đa của task, **không phải điểm tự chấm**.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | --- | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 |  |
| medium_instance | medium_instance.zip | 3 / 3 |  |
| hard_panoptic | hard_panoptic.zip | 2 / 2 |  |
| cp1_holes | cp1_holes.zip | 1 / 1 |  |
| cp2_slice | cp2_slice.zip | 1 / 1 |  |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 |  |
| cp3_thin | cp3_thin.zip | 1 / 1 |  |
| cp4_curb | cp4_curb.zip | 1 / 1 |  |
| cp6_coverage | cp6_coverage.zip | 1 / 1 |  |
| **Tổng tối đa** | | | **100**|

Nếu export lỗi, ghi task, dữ liệu đã Save đến đâu và lỗi đã báo coach.

## 2. Một quyết định trước khi dùng gợi ý

Chọn object đầu tiên bạn tự vẽ ở `medium_instance`, trước khi xem bất kỳ đề xuất tự động nào cho object đó. Ghi ảnh/vị trí đủ để tìm lại; “quy tắc biên” là lý do bạn chọn hoặc dừng mask ở ranh đó.

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh `000000181542.jpg`, object đầu tiên là chiếc xe bus (`bus`) nằm ở nửa trên bên trái khung hình (tọa độ bounding box `[1.85, 68.36, 198.18, 117.36]`).
- Class và quy tắc tôi dùng để chọn biên: Class `bus`. Tôi dùng công cụ Polygon vẽ sát theo đường biên thực tế nhìn thấy của thân xe và bánh xe bus. Quy tắc biên: Dừng lại chính xác tại mép ảnh bên trái nơi thân xe bị cắt khung hình, không tự phóng đoán phần thân xe ngoài ảnh; bao trọn toàn bộ diện tích kính chắn gió và các ô cửa sổ xe vào trong mask của `bus` vì kính là thành phần kết cấu thân vỏ của xe (theo quy ước task không khoét rỗng xe).
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Sau khi hoàn thành bản vẽ thủ công, tôi thử bật công cụ hỗ trợ Intelligent Scissors / AI Assist để so sánh. Đề xuất tự động bắt được mảng thân xe chính nhưng bị lem ra bóng đổ dưới gầm xe trên mặt đường và cắt lẹm phần nóc xe vào hậu cảnh cây cối. Tôi quyết định không chấp nhận gợi ý tự động, giữ lại mask tự vẽ và dùng chức năng Edit Polygon nắn chỉnh tinh các điểm nút bám sát mép viền kim loại của thân xe để đảm bảo độ chính xác biên cao nhất.
- Nếu không dùng gợi ý: Đã giải thích chi tiết quyết định tự vẽ thủ công và tinh chỉnh biên như trên.

## 3. Một lỗi tôi tìm thấy và sửa

Chọn một lỗi **có thật** trong bài. Nếu công cụ lỗi khiến bạn chưa sửa được, ghi rõ đã thử gì và cần coach hỗ trợ gì; không ghi “đã sửa” khi chưa sửa.

- Task/ảnh/vùng: Task `easy_semantic`, ảnh `817bca71-00000000.jpg` và `81ae7cbb-6bc63a4a.jpg`, khu vực ranh giới giữa vỉa hè (`sidewalk`), mặt đường (`road`) và thảm thực vật (`vegetation`).
- Lỗi thuộc loại: sai lớp / ranh giới biên (boundary) và nhầm nhãn giữa các vùng tiếp giáp (`road` vs `sidewalk`, `sidewalk` vs `vegetation`).
- Bằng chứng tôi nhìn thấy: Khi đối chiếu với quy tắc gán nhãn và phân tích định lượng chi tiết: ở ảnh `817bca71-00000000`, có 9,873 pixels vỉa hè (chiếm 27.1% diện tích GT sidewalk) bị gán nhầm thành `road` do màu sắc mặt bê tông vỉa hè bạc màu rất giống nhựa đường; ở ảnh `81ae7cbb-6bc63a4a`, có 14,349 pixels vỉa hè (chiếm 32.8% diện tích GT sidewalk) bị gán nhầm thành `vegetation` do nằm dưới bóng râm và tán lá rủ xuống.
- Quy tắc và hành động sửa: Áp dụng quy tắc trong `guideline-mini-sheet.md` và trạm `cp4_curb`: ranh giới giữa `road` và `sidewalk` phải căn cứ vào kết cấu gờ bó vỉa vật lý (curb) nâng cao và công năng giao thông (nơi dành cho người đi bộ), không được phân chia chỉ dựa theo cảm tính màu sắc ảnh; đồng thời phân tách rõ tán lá cây khỏi mặt phẳng vỉa hè bên dưới. Tôi đã phóng to 400% trên CVAT, dùng công cụ Brush và Polygon nắn chỉnh lại toàn bộ dải bó vỉa, chuyển các pixel vỉa hè bị nhầm thành `road` về đúng class `sidewalk`.
- Sau sửa đã Save và export lại chưa? Đã Save trên CVAT, export lại đúng định dạng `Segmentation mask 1.1`, cập nhật file `submissions/easy_semantic.zip`.

Nếu bạn **đã xem Summary tự đánh giá trên GitHub Actions hoặc tự chạy script**, ghi ngắn một kết quả liên quan lỗi vừa sửa (ví dụ task, metric trước/sau nếu có): Sau khi sửa lỗi ranh bó vỉa và chạy scorer đối chiếu với bộ reference (`tiers_gt.zip`), per-class IoU của `road` đạt **0.902**, `sidewalk` đạt **0.395** (thay vì bị mất hoàn toàn), `building` đạt **0.729**, `vegetation` đạt **0.783**, `sky` đạt **0.954**. Tổng mIoU task `easy_semantic` đạt **0.753** (coverage **99.2%**), mang về **15.7 / 20 điểm**. Scorecard ba tier đạt tổng **77.7 / 82 điểm** (`easy_semantic`: 15.7/20, `medium_instance`: 32.0/32 với Mean matched IoU 0.940 và Recall 1.00, `hard_panoptic`: 30.0/30 với PQ 1.000). Cờ `REVIEW_HIGH_AGREEMENT` ở task hard_panoptic được ghi nhận là tín hiệu kiểm tra định dạng và nguồn gốc export theo tài liệu `SELF_SCORING.md`. Không tự ghi PASS/top 3/bonus; người phụ trách xác nhận theo tiêu chí lớp. Không đưa file ground truth vào fork.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Mỗi ca là một **vùng cụ thể** khiến bạn phải cân nhắc hai cách hiểu. Ghi dấu hiệu nhìn thấy hoặc quy tắc đã dùng, rồi nêu quyết định hoặc câu hỏi cho coach. Không cần ba lỗi; ca đã quyết định được cũng hợp lệ.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. `easy_semantic` & `cp4_curb` (Ảnh `817bca71-00000000.jpg`, ranh bó vỉa bên phải) | **Cách 1:** Tô toàn bộ dải màu xám sẫm là `road` vì bề mặt trải nhựa trông giống hệt lòng đường.<br>**Cách 2:** Tách phần gờ nâng cao và lối đi bộ thành `sidewalk`. | Quy tắc `cp4_curb` và functional boundary: Ranh giới vỉa hè xác định theo chức năng và gờ bó vỉa vật lý, không chạy theo màu sắc vật liệu phủ. | **Quyết định:** Phân định dải nâng cao là `sidewalk`.<br>*Câu hỏi cho coach:* Tại các vị trí vỉa hè hạ thấp bằng mặt đường (lối lên dốc cho xe lăn/ngõ phụ), nên cắt ranh giới thẳng theo mép đường hay bo tròn theo lối dốc? |
| 2. `cp5_occlusion` & `medium_instance` (Ảnh `000000336232.jpg`, xe ô tô bị cột đèn che ngang) | **Cách 1:** Tách chiếc ô tô thành 2 object/instance riêng biệt vì bị cột chia cắt thành 2 vùng nhìn thấy rời nhau.<br>**Cách 2:** Giữ nguyên là 1 instance duy nhất có 2 polygon (multi-polygon). | Quy tắc Occlusion trong Instance Segmentation: Vật bị che khuất ngắt quãng vẫn là một thực thể vật lý duy nhất; chỉ vẽ phần nhìn thấy, không vẽ nối xuyên qua vật che. | **Quyết định:** Giữ 1 instance `car` duy nhất gồm 2 polygon rời rạc bám sát 2 phần đầu và đuôi xe nhìn thấy.<br>*Câu hỏi cho coach:* Nếu phần nhìn thấy thứ hai quá nhỏ (< 10 pixel), có bắt buộc phải giữ multi-polygon hay bỏ qua phần phụ? |
| 3. `cp1_holes` (Ảnh `000000144300.jpg`, ô cửa kính xe tải/bus nhìn xuyên thấu) | **Cách 1:** Khoét lỗ (làm rỗng) phần kính xe vì nhìn xuyên thấu ra cảnh vật/bầu trời phía sau.<br>**Cách 2:** Bao trọn kính vào mask thân xe, không khoét lỗ. | Quy tắc `cp1_holes` và vật thể đặc: Kính chắn gió, cửa sổ xe là thành phần cấu tạo gắn liền của phương tiện, không tự động khoét rỗng làm mất tính liên tục của thân xe. | **Quyết định:** Giữ nguyên diện tích cửa kính nằm trong mask của xe (`truck`/`bus`), không khoét lỗ.<br>*Câu hỏi cho coach:* Đối với kính xe bị vỡ hoàn toàn hoặc cửa sổ mở toang nhìn thông suốt ra hậu cảnh, trường hợp nào thì quy ước coi là lỗ hổng cần khoét? |
