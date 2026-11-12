# Báo cáo thực hành Day 3

- Mã học viên theo lớp: 2A202603228
- Ngày / CVAT local: 11–12/11/2026, CVAT local chạy bằng Docker trên WSL (localhost:8080)
- Công cụ đã dùng: CVAT, vẽ thủ công bằng Brush/Polygon; không dùng gợi ý tự động (SAM). Dùng `inspect_submissions.py` để kiểm cấu trúc ZIP và scorer sau khi coach phát ground truth.

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | cp5_occlusion.zip | 1 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

`inspect_submissions.py` báo 9/9 ZIP đúng cấu trúc (OK). Medium và Hard đã được sửa và export lại sau khi chạy scorer.

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: ảnh `000000181542`, người phụ nữ mặc áo dài trắng đi bộ ở giữa ảnh, vẽ tay bằng Polygon.
- Class và quy tắc tôi dùng để chọn biên: class `person`. Tôi vẽ sát phần nhìn thấy của người, bám theo đầu tóc, vai, vạt áo dài và hai bàn chân, không lấy mặt đường và vạch kẻ đường xung quanh. Chỗ tay và vạt áo chạm bánh xe máy phía sau, tôi dừng ở ranh giữa người và xe, không tô lấn sang xe máy (quy tắc: vẽ phần nhìn thấy, không đoán phần bị che).
- Nếu dùng gợi ý sau đó: không dùng gợi ý tự động, toàn bộ vẽ thủ công.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: medium_instance, cả 3 ảnh (`000000181542`, `000000373353`, `000000458325`); chủ yếu là người và xe nhỏ ở xa hoặc bị che.
- Lỗi thuộc loại: thiếu vật.
- Bằng chứng tôi nhìn thấy: sau khi coach phát ground truth, scorer cho thấy tôi nộp 45 vật trong khi đáp án có 71, R@0.5 = 0.52, điểm tự đánh giá 0.5/32. Tôi so số vật theo từng ảnh và từng lớp để tìm ảnh còn thiếu.
- Quy tắc và hành động sửa: mở lại CVAT, vẽ thêm người và xe còn sót ở từng ảnh, mỗi vật một mask riêng, vật bị che chỉ vẽ phần nhìn thấy.
- Sau sửa đã Save và export lại chưa? Đã Save, export lại COCO 1.0 và ghi đè `submissions/medium_instance.zip`.

Kết quả tự đánh giá liên quan: trước sửa nộp 45/71 vật, R@0.5 = 0.52, 0.5/32; sau sửa nộp 68/71 vật, R@0.5 = 0.73, 10.4/32. Đây là kết quả **sau khi xem reference**, không phải bản làm độc lập. Tôi không tự ghi PASS/top 3/bonus.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| `cp6_coverage`: xe buýt trắng lớn giữa ảnh | Gán `car` (gần nghĩa nhất) hoặc để trống | `classes.json` của trạm không có lớp bus/truck; phiếu quy tắc: không ép đoán cho đủ coverage | Để trống vùng xe buýt (và xe tải bên trái), chỉ gán các vùng thuộc schema: car, building, road, sidewalk, sky, person. Câu hỏi coach: để trống như vậy có đúng không? |
| `cp4_curb`: bãi bê tông bên phải, chỗ SUV trắng | `road` (nơi xe đỗ) hoặc `sidewalk` (mặt sân giống vỉa hè) | Ranh theo chức năng và bó vỉa, không theo màu | Gán `sidewalk` cho mặt bê tông liền vỉa hè phía trước; phần quanh SUV và dải đất có cây giữa đường và vỉa hè để trống vì không chắc. Câu hỏi coach: bãi đỗ xe này nên là road hay sidewalk? |
| `hard_panoptic` ảnh `000000460147`: ô tô trên xe chở ô tô | Mỗi ô tô là một `car` riêng, hoặc chỉ tính vào mask `truck` | Các xe nhìn rõ là xe con; cùng lớp sát nhau vẫn là hai instance | Vẽ từng ô tô thành `car` riêng và vẽ xe chở là `truck`. Câu hỏi coach: mask `truck` phủ lên các ô tô trên xe (chồng lấn) thì có cần khoét ra không? |
