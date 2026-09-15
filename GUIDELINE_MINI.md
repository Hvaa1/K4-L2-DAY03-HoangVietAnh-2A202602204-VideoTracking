# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: Hoàng Việt Anh
Clip: `clip_01`, `clip_02`

---

## 1. Phạm vi: gán cái gì, không gán cái gì

Một lớp duy nhất: **`vehicle`** — xe bốn bánh (xe con, van, xe buýt, xe tải).

| Gán | Không gán |
| --- | --- |
| xe con, SUV, taxi, xe bán tải | người đi bộ |
| van, minivan | xe đạp |
| xe buýt, minibus | **xe máy / mô tô** |
| xe tải, xe đầu kéo | xe trong ảnh quảng cáo, trong gương, dưới bóng nước |

Bổ sung của nhóm (nếu có): `...`

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm                                                                               | Vì sao                         |
| --- |---------------------------------------------------------------------------------------------|--------------------------------|
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 15 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | Do thời gian xe bị che quá ít  |
| Xe bị che lâu hơn ngưỡng trên | Có thể thay đổi nhãn hoặc không tùy tình huống                                              | có thể xe khác đã xuất hiện    |
| Xe rời khung hình rồi quay lại | mặc định: **track mới**                                                                     | Vì có thể xe mới đã xuất hiện  |
| Hai xe cắt nhau / chồng lên nhau | Vẫn giữ nguyên id                                                                           | do vẫn là 1 xe từ đầu đến cuối |

## 3. Luật bbox

| Tình huống | Luật của nhóm                                                                                                                          |
| --- |----------------------------------------------------------------------------------------------------------------------------------------|
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh                                                                                          |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được**                                                                                                        |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: thời điểm mắt người có thể nhận diện được là xe 4 bánh |
| Xe đang đỗ, không di chuyển | vẫn gắn bbox                                                                                                                           |
| Keyframe đặt dày ở đâu | thời điểm xe bị cắt, lúc đầu và kết thúc                                                                                               |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: 0
- Tình huống: 0
- Quyết định: 0
- Lý do: 0

### Ca 2
- Clip / frame / ID: 0
- Tình huống: 0
- Quyết định: 0
- Lý do: 0

### Ca 3
- Clip / frame / ID: 0
- Tình huống: 0
- Quyết định: 0
- Lý do: 0

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- `...`
- `...`
