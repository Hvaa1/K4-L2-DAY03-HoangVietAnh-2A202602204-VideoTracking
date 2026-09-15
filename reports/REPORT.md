# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: Hoàng Việt Anh
Ngày: 15/09/2026

---

## 1. Quá trình gán nhãn

| Mục | Giá trị      |
| --- |--------------|
| Công cụ | CVAT / khác: |
| Thời gian gán `clip_02` (warm-up) | 30 phút      |
| Thời gian gán `clip_01` | 60 phút      |
| Số track đã vẽ trong `clip_01` | `...`        |
| Số keyframe trung bình mỗi track | `...`        |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. Vẽ BB mất thời gian căn chỉnh giũa những frame nên em dùng tool trackml của cvat và thay đổi tham số để phù hợp
2. 0
3. 0

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):

- Lượt 1: Cùng 1 xe cùng 1 frame   
- Lượt 2: Lúc làm có thể thấy nó chưa hiện hoặc biến mất nhưng tua lại vẫn thấy rõ
- Lượt 3: Đôi lúc bb bị lệch

Kiểm chéo với: `...`. Chi tiết ở `reports/review_partner.md`.
Số lỗi bạn tìm được trong bản của bạn ấy: `...`. Số lỗi bạn ấy tìm được trong bản của bạn: `...`.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

`...`

## 3. Pre-gold lock và chấm trước/sau rework

| Evidence | Giá trị                                                          |
| --- |------------------------------------------------------------------|
| SHA-256 từ `evidence/pre-gold/clip_01/manifest.json` | 5598bc855114e23c5a1074b3dbd8435f858d9f341b509544a17eb335f3de1197 
 |
| Thời điểm khóa | 5:00                                                             |
| Số row / frame / track trước khi mở reference | 561/8                                                            |

| | HOTA |  DetA |  AssA |  LocA |  IDF1 |  MOTA | MOTP | FP | FN | IDSW |
| --- | ---: |------:|------:|------:|------:|------:| ---: |---:|---:|-----:|
| Bản pre-gold |0.796 | 0.783 | 0.8458 | 0.9735 | 0.9476 | 0.974 |0.8283 |  9 | 21 |    0 |
| Sau rework  |0.796 | 0.783 | 0.811 | 0.846 | 0.947 | 0.978 |0.828 |  9 | 21 |    0 |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có / chưa**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi  | Frame           | ID  | Đã sửa thế nào |
|-----------|-----------------|-----|----------------|
| BBOX trôi | 93,155,96,92,97 | 5,6 | Sửa BBox       |
|           |                 |     |                |
|           |                 |     |                |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị                          |
| --- |----------------------------------|
| Python / ultralytics / torch / lap | 3.13.5/8.4.145/2.11+cu128/0.5.13 |
| weights / hai tracker | yolo26n.pt/bytetrack ,reid       |
| conf / IoU / imgsz / classes | 0.25/0.7/960/[2 5 7]             |
| device | 0                                |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |---:|---:|-----:|
| bạn vs gold |0.796 | 0.783 | 0.811 | 0.846 | 0.947 | 0.978 |0.828 |  9 | 21 |    0 |
| ByteTrack control vs gold |0.709 |0.649 |0.776 |0.846 |0.875 |0.749 |0.823 | 88 | 54 |    2 |
| BoT-SORT + ReID vs gold |0.763 |0.711 |0.820 |0.872 |0.900 |0.792 |0.860 | 91 | 26 |    2 |
| ReID vs bạn |0.741 |0.683 |0.811 |0.840 |0.899 |0.784 |0.823 |  99 | 22 |    0 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

Như vậy **MOTA của tôi cao hơn IDF1**:

\[
0.974 > 0.9476
\]

Điều này cho thấy nếu xét theo MOTA thì kết quả của tôi rất tốt, nhưng khi xét tính nhất quán của identity trên toàn bộ trajectory thì vẫn còn một số sai khác so với Gold.



MOTA chỉ phạt lỗi identity thông qua `IDSW`, tức là tại thời điểm hệ thống phát hiện một đối tượng bị chuyển từ ID này sang ID khác. Trong kết quả của tôi, `IDSW = 0`, nên MOTA gần như không bị giảm bởi lỗi ID mà chủ yếu bị ảnh hưởng bởi `FP = 9` và `FN = 21`.

Trong khi đó, **IDF1 đánh giá việc một identity có được duy trì đúng trên toàn bộ track hay không**. Vì vậy IDF1 có thể giảm khi track của tôi không khớp hoàn toàn với trajectory của Gold, ngay cả khi metric IDSW không ghi nhận một lần chuyển ID trực tiếp.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

BoT-SORT + ReID có IDF1 và AssA cao hơn, tức là khả năng liên kết các detection thành cùng một trajectory tốt hơn ByteTrack.

Tuy nhiên IDSW vẫn là 2 ở cả hai tracker, nên không thể nói treatment đã loại bỏ hoàn toàn lỗi đổi ID.

Ví dụ một sequence minh họa:

| Frame | 100 | 101 | 102 | 103 | 104 | 105 | 106 |
|---|---:|---:|---:|---:|---:|---:|---:|
| GT ID | 7 | 7 | 7 | che | che | 7 | 7 |
| ByteTrack | 7 | 7 | 7 | - | - | 12 | 12 |
| BoT-SORT + ReID | 7 | 7 | 7 | - | - | 7 | 7 |

Trong trường hợp này ByteTrack tạo ID mới sau khi xe bị che khuất, còn BoT-SORT + ReID nối lại được track cũ nên AssA và IDF1 tốt hơn.

Tuy nhiên không thể quy toàn bộ chênh lệch cho ReID, vì ByteTrack và BoT-SORT là hai implementation khác nhau. Chúng khác cả motion model, matching strategy, threshold, cách quản lý lost track và cách association. Vì vậy kết quả tốt hơn là hiệu quả của toàn bộ BoT-SORT + ReID treatment, không phải chỉ riêng ReID.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

| Metric | ByteTrack | BoT-SORT + ReID | Thay đổi |
|---|---:|---:|---:|
| DetA | 0.649 | 0.711 | +0.062 |
| FP | 88 | 91 | +3 |
| FN | 54 | 26 | -28 |
| AssA | 0.776 | 0.820 | +0.044 |
| IDSW | 2 | 2 | 0 |

DetA tăng từ **0.649 lên 0.711**.

FN giảm từ **54 xuống 26**, tức giảm **28 trường hợp bỏ sót**. FP tăng nhẹ từ **88 lên 91**, tức tăng **3 false positive**.

Như vậy BoT-SORT + ReID giảm đáng kể số đối tượng bị bỏ sót, dù FP tăng nhẹ.

Lỗi còn lại không hoàn toàn là lỗi detector. BoT-SORT + ReID vẫn có:

- `FP = 91`
- `FN = 26`
- `IDSW = 2`
- `AssA = 0.820`

Do đó vẫn tồn tại cả:

- **Detection error:** thể hiện qua FP và FN.
- **Association error:** thể hiện qua AssA chưa đạt 1 và vẫn còn 2 IDSW.

Ngoài ra, FP/FN ở đầu ra tracker không nhất thiết hoàn toàn do detector, vì mỗi tracker có cách giữ, loại và nối lại track khác nhau.

**Kết luận:** lỗi còn lại gồm cả detection và association, nhưng FP và FN đang chiếm phần lớn hơn lỗi ID switch.


**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Không thể xác định chính xác frame và ID chỉ từ bảng metric tổng hợp. Cần xem lại video hoặc file tracking theo từng frame.

### Trường hợp tôi đúng, ReID sai

| Frame | Trước | Frame cần kiểm tra | Sau |
|---|---:|---:|---:|
| Gold ID | 7 | 7 | 7 |
| ID của tôi | 7 | 7 | 7 |
| ReID | 7 | 12 | 12 |

- Frame: `[điền frame thực tế]`
- Gold ID: `[ID]`
- ID của tôi: `[ID]`
- ReID ID: `[ID khác]`

Nếu xem các frame trước và sau cho thấy đây vẫn là cùng một phương tiện, annotation của tôi giữ đúng ID giống Gold trong khi ReID đổi sang ID khác, thì đây là trường hợp **tôi đúng và ReID sai**.

### Trường hợp ReID đúng, annotation của tôi cần xem lại

| Frame | Trước | Frame cần kiểm tra | Sau |
|---|---:|---:|---:|
| Gold ID | 15 | 15 | 15 |
| ID của tôi | 15 | 21 | 21 |
| ReID | 15 | 15 | 15 |

- Frame: `[điền frame thực tế]`
- Gold ID: `[ID]`
- ID của tôi: `[ID khác]`
- ReID ID: `[ID]`

Nếu xem sequence cho thấy đây thực sự vẫn là cùng một xe nhưng tôi đã tạo ID mới, thì annotation của tôi cần xem lại.

Tôi chỉ sửa annotation nếu video cho thấy rõ đây là cùng một phương tiện, **không sửa annotation chỉ vì model dự đoán khác**.

Với kết quả:

- `IDF1 = 0.974`
- `IDSW = 0`

nên lỗi về ID trong annotation của tôi tương đối ít.


**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

`...`

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

Tôi sẽ bổ sung các quy tắc sau:

### Quy tắc duy trì Track ID

1. Không tạo ID mới chỉ vì đối tượng bị che khuất hoặc mất khỏi hình trong một số frame.

2. Khi đối tượng xuất hiện lại sau che khuất, phải xem các frame trước và sau để xác định có phải cùng một đối tượng hay không.

3. Khi xác định lại ID, cần dựa vào:
   - hướng di chuyển;
   - vị trí trước và sau che khuất;
   - tốc độ tương đối;
   - loại phương tiện;
   - màu sắc;
   - hình dạng và đặc điểm dễ nhận biết.

4. Khi hai phương tiện giao nhau hoặc che khuất nhau, không đổi ID chỉ dựa vào vị trí ở một frame. Phải xem continuity của cả sequence.

5. Trước khi hoàn thành một clip, cần kiểm tra lại:
   - một đối tượng bị tách thành hai ID;
   - hai đối tượng khác nhau bị gộp thành một ID;
   - ID bị swap khi hai xe đi gần hoặc giao nhau;
   - đối tượng xuất hiện lại sau occlusion nhưng bị tạo ID mới;
   - bounding box bị thiếu khi đối tượng vẫn còn nhìn thấy rõ.

6. Prediction của ByteTrack, BoT-SORT hoặc ReID chỉ dùng để gợi ý vị trí cần kiểm tra lại, không được xem là Ground Truth.

7. Không sửa annotation chỉ vì model cho kết quả khác. Chỉ sửa khi kiểm tra trực tiếp video và có đủ bằng chứng.

8. Nếu không đủ bằng chứng để xác định hai đoạn track có phải cùng một đối tượng hay không, cần ghi lại trong decision log thay vì tự suy đoán.

**Điểm quan trọng nhất:** không gán tracking theo từng frame độc lập mà phải kiểm tra cả sequence, đặc biệt ở các đoạn có che khuất hoặc nhiều xe đi gần nhau.

## 7. Tệp đã nộp

- [X] `annotations/clip_01/gt.txt`
- [X] `annotations/clip_02/gt.txt`
- [X] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [X] `GUIDELINE_MINI.md` đã điền
- [X] `outputs/eval_vs_gold.json`
- [X] `outputs/model_bytetrack_clip_01.txt`
- [X] `outputs/model_reid_clip_01.txt`
- [X] `outputs/model_run_config.json`
- [X] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [ ] `reports/review_partner.md`
- [X] `reports/REPORT.md` (file này)
