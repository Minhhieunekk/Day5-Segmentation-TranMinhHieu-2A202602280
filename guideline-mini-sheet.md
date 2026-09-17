# Phiếu quy tắc gán nhãn Day 5 (Segmentation Mini-Sheet)

Tài liệu bỏ túi tổng hợp toàn bộ quy tắc gán nhãn, lưu ý hình học, chuẩn format export CVAT, quy trình tự kiểm tra (QC) và hướng dẫn tính điểm theo Rubric Day 5.

---

## 1. Phân biệt 3 loại bài toán & Format Export trong CVAT

| Loại bài toán | Câu hỏi cốt lõi | Trong bài Lab Day 5 | Format Export CVAT | Định dạng file nộp |
| :--- | :--- | :--- | :--- | :--- |
| **Semantic** | Pixel này thuộc **loại vùng (class)** nào? | `easy_semantic`, `cp3_thin`, `cp4_curb`, `cp6_coverage` | **Segmentation mask 1.1** | File ZIP chứa `SegmentationClass/*.png` và `labelmap.txt` |
| **Instance** | Pixel này thuộc **vật thể (object/instance)** cụ thể nào? | `medium_instance`, `cp1_holes`, `cp2_slice`, `cp5_occlusion` | **COCO 1.0** | File ZIP chứa `annotations/*.json` (polygon/RLE) |
| **Panoptic** | Pixel này thuộc **vùng nền (stuff)** nào hay **vật đếm được (thing)** nào? | `hard_panoptic` | **COCO 1.0** | File ZIP chứa `annotations/*.json` tích hợp cả stuff & things |

> [!IMPORTANT]
> **Quy tắc tên lớp:** Tên lớp phải trùng khớp 100% từng ký tự với `classes.json` của từng task (chú ý phân biệt chữ hoa/thường, dấu gạch dưới hay khoảng trắng: ví dụ `traffic sign` khác `traffic_sign`). Không dùng một danh sách lớp chung cho mọi task.

---

## 2. Quy tắc vàng cho 6 trạm Checkpoint

Mỗi trạm kiểm tra một năng lực xử lý hình học cụ thể trong bài toán phân đoạn ảnh:

| Trạm | Mục tiêu chính | Bằng chứng đúng cần thể hiện | Lỗi phổ biến cần tránh |
| :--- | :--- | :--- | :--- |
| **`cp1_holes`** *(Instance)* | Xử lý lỗ rỗng (Holes) | Kính xe, cửa sổ, nan hoa xe gắn liền thân xe nằm trọn trong mask của vật thể theo quy ước task. | Khoét lỗ tùy tiện vào kính xe làm vật bị rỗng ruột. Chỉ khoét khi phần nhìn xuyên thấu rõ ràng thuộc background tách biệt. |
| **`cp2_slice`** *(Instance)* | Tách vật thể kề sát (Slice) | Hai người hoặc hai xe kề sát nhau vẫn phải là **hai instance/mask độc lập**. | Gộp hai xe hoặc hai người đứng cạnh nhau thành một mask duy nhất. |
| **`cp3_thin`** *(Semantic)* | Cấu trúc nét mảnh (Thin structures) | Cột đèn (`pole`), biển báo (`traffic sign`) có nhãn đúng, mask bám sát nét mảnh khi zoom lớn. | Bỏ qua các vật thể mảnh nhỏ hoặc dùng brush quá dày làm lem màu sang nền trời/nhà. |
| **`cp4_curb`** *(Semantic)* | Ranh giới bó vỉa (Curb/Boundary) | Ranh giới giữa `road` và `sidewalk` phân chia chính xác theo gờ bó vỉa nâng cao và chức năng sử dụng. | Chọn nhầm ranh giới chỉ dựa theo màu nhựa đường/bê tông mà không nhìn kết cấu vật lý của vỉa hè. |
| **`cp5_occlusion`** *(Instance)* | Vật thể bị che khuất (Occlusion) | Vật thể bị cột/vật khác che cắt rời vẫn được gán là **1 instance duy nhất** (multi-polygon). | Tách thành 2 object khác nhau hoặc tự ý vẽ xuyên qua phần bị che khuất (chỉ vẽ phần thực tế nhìn thấy). |
| **`cp6_coverage`** *(Semantic)* | Độ phủ toàn vẹn (Full coverage) | Tất cả các vùng nhìn thấy thuộc các lớp quy định đều được phủ kín, không để khe hở. | Để lại các khe hở trống lớn (unlabeled gaps) giữa các ranh giới lớp, hoặc tô bừa vào vùng không chắc chắn. |

---

## 3. Quy tắc hình học & Xử lý biên chung

1. **Chỉ vẽ phần nhìn thấy (Visible parts):**
   - Bám sát đường biên thực tế nhìn thấy trên ảnh.
   - Tuyệt đối không tự ý vẽ bù/phóng đoán phần bị vật khác che khuất hoặc phần ngoài khung hình.
2. **Quan hệ giữa các đối tượng (Instance relationship):**
   - **Hai vật cùng class kề sát nhau:** Bắt buộc vẽ thành **2 instance riêng biệt**.
   - **Một vật bị ngắt quãng bởi vật cản:** Gán chung vào **1 instance duy nhất** (sử dụng tính năng multi-polygon trong CVAT).
3. **Chi tiết cấu tạo xe & lỗ rỗng:**
   - Kính chắn gió, cửa sổ xe ô tô/xe bus/xe tải là thành phần kết cấu của thân xe, **không khoét rỗng** trừ khi có chỉ định đặc biệt.
4. **Ranh giới Road vs Sidewalk:**
   - Ưu tiên cấu trúc hình học gờ bó vỉa và công năng (lòng đường xe chạy vs vỉa hè người đi bộ). Khi mặt đường và vỉa hè cùng màu, căn cứ vào vết nứt ranh giới hoặc bóng đổ của mép đá vỉa hè.
5. **Kỹ thuật thao tác trong CVAT:**
   - Phóng to 300% – 500% khi xử lý đường biên phức tạp, nét mảnh hoặc khe hẹp.
   - Thường xuyên nhấn **Save** (`Ctrl + S`) và kiểm tra lại danh sách **Objects** ở cột bên phải để loại bỏ mask rác/vùng thừa.

---

## 4. Quy trình tự kiểm tra QC 5 bước trước khi nộp

- [x] **Bước 1: Kiểm tra ảnh & định dạng export**
  - Semantic tasks: Export định dạng **Segmentation mask 1.1** (chứa folder `SegmentationClass/` và file `labelmap.txt`).
  - Instance & Panoptic tasks: Export định dạng **COCO 1.0** (chứa folder `annotations/` với file `instances_default.json`).
- [x] **Bước 2: Chuẩn hóa tên file ZIP**
  - Đặt đúng tên file: `easy_semantic.zip`, `medium_instance.zip`, `hard_panoptic.zip`, `cp1_holes.zip`, `cp2_slice.zip`, `cp3_thin.zip`, `cp4_curb.zip`, `cp5_occlusion.zip`, `cp6_coverage.zip`.
  - Di chuyển/lưu tất cả vào thư mục `submissions/` trong repo fork.
- [x] **Bước 3: Chạy script kiểm tra cấu trúc (Offline Inspection)**
  ```bash
  python scripts/inspect_submissions.py --dir submissions
  ```
  - Kiểm tra xem có task nào bị thiếu ảnh hoặc sai format không.
- [x] **Bước 4: Chạy tự đánh giá với bộ tham chiếu (khi có reference ground truth)**
  ```bash
  # Cài đặt reference (nếu dùng gói tiers_gt.zip)
  python scripts/install_reference.py tiers_gt.zip

  # Chạy scorecard 3 tier (Easy, Medium, Hard - tối đa 82 điểm)
  python scoring/scorecard.py --group tiers --dir submissions --out reports/tiers
  ```
- [x] **Bước 5: Hoàn thiện báo cáo `REPORT.md`**
  - Điền đầy đủ 4 phần trong `REPORT.md`: Thông tin học viên, Bảng bài đã nộp, Một quyết định trước gợi ý, Một lỗi đã sửa kèm số liệu, Ba ca cân nhắc thực tế.

---

## 5. Bảng tham chiếu công thức tính điểm Rubric

| Nhóm Task | Loại | Trọng số | Metric tính điểm | Floor | Cap (đủ điểm) | Công thức quy đổi điểm |
| :--- | :--- | :---: | :--- | :---: | :---: | :--- |
| `easy_semantic` | Semantic | **20** | **mIoU** theo các lớp | 0.40 | 0.85 | $\text{clamp}\left(\frac{\text{mIoU} - 0.40}{0.85 - 0.40}, 0, 1\right) \times 20$ |
| `medium_instance` | Instance | **32** | **Mean matched IoU $\times$ Recall@0.5** | 0.40 | 0.85 | $\text{clamp}\left(\frac{\text{metric} - 0.40}{0.85 - 0.40}, 0, 1\right) \times 32$ |
| `hard_panoptic` | Panoptic | **30** | **Panoptic Quality (PQ)** | 0.20 | 0.65 | $\text{clamp}\left(\frac{\text{PQ} - 0.20}{0.65 - 0.20}, 0, 1\right) \times 30$ |
| **6 Checkpoints** | Từng trạm | **18** | Đánh giá theo rubric từng trạm | — | — | 6 trạm $\times$ 3 điểm = 18 điểm |
| **TỔNG CỘNG** | | **100** | | | | **Tối đa 100 điểm cốt lõi** |

> [!NOTE]
> - Điểm tự đánh giá 3 tier tối đa là **82 điểm** (20 + 32 + 30).
> - Chính sách Bonus (theo thông báo lớp): Thưởng **10 điểm** khi hoàn thiện report đạt yêu cầu hoặc **20 điểm** cho top 3 độ chính xác cao nhất (không cộng dồn 10 + 20, trần tối đa không vượt quá 100 điểm: $\min(100, \text{core} + \text{bonus})$). Học viên không tự cộng điểm bonus vào `REPORT.md` khi chưa có xác nhận từ Coach.
