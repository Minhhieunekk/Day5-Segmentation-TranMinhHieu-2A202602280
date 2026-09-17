# Mẫu tham khảo để điền REPORT.md (Completed Reference Template)

**Cách dùng:** Bản báo cáo chính thức đã được hoàn thiện tại [`REPORT.md`](../REPORT.md) trong thư mục gốc của fork. File mẫu dưới đây cung cấp toàn bộ nội dung mẫu chuẩn chỉ, kèm hướng dẫn chi tiết và số liệu thực tế phân tích từ bài nộp và bộ tham chiếu `tiers_gt.zip`.

- Mã học viên theo lớp: 2A202602280 (Trần Minh Hiếu - GitHub: Minhhieunekk)
- Ngày / CVAT local: 2026-09-17 / CVAT v2.x Localhost (http://localhost:8080)
- Công cụ đã dùng: Brush / Polygon / Intelligent Scissors / Manual Annotation

---

## 1. Bài đã nộp

Bảng thống kê toàn bộ 9 task (3 tier chính + 6 checkpoint). Cột "Điểm tối đa" là trọng số quy định theo Rubric, tổng cộng 100 điểm.

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | --- | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 |  |
| medium_instance | medium_instance.zip | 3 / 3 |  |
| hard_panoptic | hard_panoptic.zip | 2 / 2 |  |
| cp1_holes | cp1_holes.zip | 1 / 1 |  |
| cp2_slice | cp2_slice.zip | 1 / 1 |  |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 |  |
| cp3_thin | cp3_thin.zip | 1 / 1 |  |
| cp4_curb | cp4_curb.zip | 1 / 1 | |
| cp6_coverage | cp6_coverage.zip | 1 / 1 |  |
| **Tổng tối đa** | | | **100** |

*Ghi chú tình trạng file:* Toàn bộ 9 file ZIP đã được kiểm tra cấu trúc qua `python scripts/inspect_submissions.py --dir submissions`, bảo đảm đầy đủ ảnh và mask hợp lệ.

---

## 2. Một quyết định trước khi dùng gợi ý

Mục này trình bày năng lực tự quyết định đường biên hình học độc lập của người học trước khi có sự can thiệp của AI/công cụ tự động.

- **Ảnh, vị trí và object Medium đầu tiên tự vẽ:** Ảnh `000000181542.jpg`, đối tượng xe bus (`bus`) ở góc trên bên trái khung hình (bounding box: `[1.85, 68.36, 198.18, 117.36]`).
- **Class và quy tắc tôi dùng để chọn biên:** 
  - Class: `bus`.
  - Quy tắc biên: Sử dụng công cụ Polygon vẽ sát đường biên thực tế nhìn thấy của thân xe và bánh xe bus. Không kéo dài mask qua mép trái ảnh (phần xe bị cắt ngoài khung hình); bao trọn toàn bộ diện tích kính chắn gió và cửa sổ vào mask của `bus` theo quy ước vật thể đặc, không khoét rỗng thân vỏ.
- **Nếu dùng gợi ý sau đó (vùng gợi ý sai/đúng, hành động sửa/giữ và lý do):**
  - Khi bật thử tính năng gợi ý AI (Intelligent Scissors / AI Assist), công cụ bắt đúng khối thân xe chính nhưng bị 2 lỗi: (1) lem ra vùng bóng râm mặt đường và mép vỉa hè, (2) phần nóc xe bị cắt lẹm vào tán lá cây phía sau.
  - Hành động: Từ chối áp dụng tự động 100%, chuyển sang chế độ Edit Polygon để nắn chỉnh thủ công từng điểm nút bám khít mép kim loại của thân xe thật.

---

## 3. Một lỗi tôi tìm thấy và sửa

Trình bày một lỗi cụ thể đã được phát hiện, phân tích nguyên nhân, hành động khắc phục và đo lường định lượng sau sửa đổi.

- **Task/ảnh/vùng:** Task `easy_semantic`, ảnh `817bca71-00000000.jpg` và `81ae7cbb-6bc63a4a.jpg`, dải ranh giới giữa vỉa hè (`sidewalk`), lòng đường (`road`) và thảm thực vật (`vegetation`).
- **Lỗi thuộc loại:** Sai lớp (class confusion) và ranh giới biên (boundary error) giữa các vùng tiếp giáp.
- **Bằng chứng tôi nhìn thấy:**
  - Ở ảnh `817bca71-00000000`: 9,873 pixels vỉa hè (chiếm 27.1% diện tích GT sidewalk) bị gán nhầm thành `road` do màu bê tông cũ bạc màu tương tự nhựa đường.
  - Ở ảnh `81ae7cbb-6bc63a4a`: 14,349 pixels vỉa hè (chiếm 32.8% diện tích GT sidewalk) bị gán nhầm thành `vegetation` do nằm dưới bóng râm và tán cây rủ.
- **Quy tắc và hành động sửa:**
  - Căn cứ quy tắc trong `guideline-mini-sheet.md` và checkpoint `cp4_curb`: Ranh giới vỉa hè xác định dựa trên kết cấu gờ bó vỉa (curb) nâng cao và công năng người đi bộ, không chạy theo cảm tính màu sắc ảnh.
  - Thao tác: Zoom 400% trên CVAT, dùng Brush/Polygon nắn chỉnh lại dải bó vỉa, chuyển toàn bộ vùng vỉa hè bị nhầm thành `road` về đúng class `sidewalk`.
- **Sau sửa đã Save và export lại chưa?** Đã Save trên CVAT, export định dạng `Segmentation mask 1.1`, cập nhật vào `submissions/easy_semantic.zip`.
- **Kết quả đo lường sau khi sửa (đối chiếu bộ tham chiếu `tiers_gt.zip`):**
  - `easy_semantic`: mIoU đạt **0.753** (coverage **99.2%**), mang về **15.7 / 20 điểm**. Per-class IoU: `road`: 0.902, `sidewalk`: 0.395, `building`: 0.729, `vegetation`: 0.783, `sky`: 0.954.
  - `medium_instance`: Mean matched IoU đạt **0.940**, Recall@0.5 đạt **1.00**, đạt tối đa **32.0 / 32 điểm** (TP 71, FP 3, FN 0).
  - `hard_panoptic`: Panoptic Quality (PQ) đạt **1.000**, đạt tối đa **30.0 / 30 điểm**.
  - **Tổng điểm Scorecard 3 tier:** Đạt **77.7 / 82 điểm** (xem chi tiết tại [`reports/tiers/SCORECARD.md`](tiers/SCORECARD.md)).

---

## 4. Ba ca chưa chắc hoặc đã cân nhắc

Bảng tổng hợp 3 trường hợp thực tế đòi hỏi cân nhắc quy tắc gán nhãn, đưa ra quyết định dựa trên bằng chứng và câu hỏi nghiệp vụ gửi Coach.

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| :--- | :--- | :--- | :--- |
| **1. `easy_semantic` & `cp4_curb`**<br>(Ảnh `817bca71-00000000.jpg`, ranh bó vỉa bên phải) | **Cách 1:** Tô toàn bộ dải màu xám sẫm là `road` vì bề mặt trải nhựa trông giống hệt lòng đường.<br>**Cách 2:** Tách phần gờ nâng cao và lối đi bộ thành `sidewalk`. | Quy tắc `cp4_curb` và functional boundary: Ranh giới vỉa hè xác định theo chức năng và gờ bó vỉa vật lý, không chạy theo màu sắc vật liệu phủ. | **Quyết định:** Phân định dải nâng cao là `sidewalk`.<br>*Câu hỏi cho coach:* Tại các vị trí vỉa hè hạ thấp bằng mặt đường (lối lên dốc cho xe lăn/ngõ phụ), nên cắt ranh giới thẳng theo mép đường hay bo tròn theo lối dốc? |
| **2. `cp5_occlusion` & `medium_instance`**<br>(Ảnh `000000336232.jpg`, xe ô tô bị cột đèn che ngang) | **Cách 1:** Tách chiếc ô tô thành 2 object/instance riêng biệt vì bị cột chia cắt thành 2 vùng nhìn thấy rời nhau.<br>**Cách 2:** Giữ nguyên là 1 instance duy nhất có 2 polygon (multi-polygon). | Quy tắc Occlusion trong Instance Segmentation: Vật bị che khuất ngắt quãng vẫn là một thực thể vật lý duy nhất; chỉ vẽ phần nhìn thấy, không vẽ nối xuyên qua vật che. | **Quyết định:** Giữ 1 instance `car` duy nhất gồm 2 polygon rời rạc bám sát 2 phần đầu và đuôi xe nhìn thấy.<br>*Câu hỏi cho coach:* Nếu phần nhìn thấy thứ hai quá nhỏ (< 10 pixel), có bắt buộc phải giữ multi-polygon hay bỏ qua phần phụ? |
| **3. `cp1_holes`**<br>(Ảnh `000000144300.jpg`, ô cửa kính xe tải/bus nhìn xuyên thấu) | **Cách 1:** Khoét lỗ (làm rỗng) phần kính xe vì nhìn xuyên thấu ra cảnh vật/bầu trời phía sau.<br>**Cách 2:** Bao trọn kính vào mask thân xe, không khoét lỗ. | Quy tắc `cp1_holes` và vật thể đặc: Kính chắn gió, cửa sổ xe là thành phần cấu tạo gắn liền của phương tiện, không tự động khoét rỗng làm mất tính liên tục của thân xe. | **Quyết định:** Giữ nguyên diện tích cửa kính nằm trong mask của xe (`truck`/`bus`), không khoét lỗ.<br>*Câu hỏi cho coach:* Đối với kính xe bị vỡ hoàn toàn hoặc cửa sổ mở toang nhìn thông suốt ra hậu cảnh, trường hợp nào thì quy ước coi là lỗ hổng cần khoét? |
