# Mini annotation guideline — Ngày 3 (tracking)

> Điền file này **trong lúc gán nhãn**, không phải sau khi xong. Mỗi lần bạn dừng
> lại nghĩ "cái này tính sao nhỉ?" thì đó là một dòng phải ghi vào đây.
>
> Đây là tài liệu mà người gán nhãn tiếp theo sẽ đọc để làm giống bạn. Nếu hai
> người trong nhóm gán khác nhau, gần như luôn là vì file này chưa nói rõ — chứ
> không phải vì ai kém.

Nhóm / tên: `Nguyễn Vũ Dũng`
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

Bổ sung của nhóm (nếu có): chỉ ghi xe bốn bánh khi nhận diện được rõ; không suy đoán từ bóng hoặc vật thể phản chiếu.

## 2. Luật ID — phần quan trọng nhất

| Tình huống | Luật của nhóm | Vì sao |
| --- | --- | --- |
| Xe bị che một phần rồi hiện lại | giữ nguyên ID nếu bị che **dưới 25 frame** (mặc định của lab: 25 frame = 2 giây @ 12.5 fps) | Occlusion ngắn vẫn là cùng một vật thể; giữ ID giúp chuỗi track không bị phân mảnh. |
| Xe bị che lâu hơn ngưỡng trên | giữ ID nếu còn xác định chắc chắn cùng xe; nếu không đủ bằng chứng thì kết thúc track và tạo track mới khi xe xuất hiện rõ | Ưu tiên identity có bằng chứng, không nối ID chỉ vì vị trí gần nhau. |
| Xe rời khung hình rồi quay lại | mặc định: **track mới** | Khi đã ra khỏi khung, không có bằng chứng liên tục để khẳng định đó vẫn là cùng lần xuất hiện. |
| Hai xe cắt nhau / chồng lên nhau | giữ ID theo hướng chuyển động, hình dạng và vị trí trước/sau crossing; không đổi ID chỉ vì bbox chồng nhau | Tua chậm và đối chiếu frame trước và sau điểm giao nhau để tránh hoán đổi identity. |

## 3. Luật bbox

| Tình huống | Luật của nhóm |
| --- | --- |
| Xe bị cắt bởi rìa ảnh | bbox chạm đúng rìa, không đoán phần ngoài ảnh |
| Xe bị xe khác che một phần | bbox ôm phần **nhìn thấy được** |
| Xe vừa xuất hiện, còn rất nhỏ / rất mờ | bắt đầu track từ frame đầu tiên xác định được là xe bốn bánh; ngưỡng nhóm chọn: nhìn thấy ít nhất phần thân xe và có thể phân biệt với người/xe máy |
| Xe đang đỗ, không di chuyển | vẫn giữ track nếu xe còn trong khung; đặt bbox theo phần nhìn thấy và không bấm `outside` chỉ vì xe đứng yên |
| Keyframe đặt dày ở đâu | đặt dày tại frame bắt đầu/kết thúc, trước/sau occlusion hoặc crossing, khi đổi hướng nhanh và khi bbox thay đổi hình học rõ |

## 4. Ít nhất ba ca mơ hồ đã gặp thật

Ghi **frame cụ thể** và **ID cụ thể**, không ghi chung chung.

### Ca 1
- Clip / frame / ID: `clip_01` / 55 / ID 4
- Tình huống: xe ở vùng giao cắt, bbox có hình học khó và IoU đánh giá thấp.
- Quyết định: giữ ID 4, đặt bbox theo phần xe nhìn thấy.
- Lý do: chuỗi chuyển động trước và sau frame 55 liên tục; không có bằng chứng xe biến mất rồi quay lại.

### Ca 2
- Clip / frame / ID: `clip_01` / 87 / ID 5
- Tình huống: đoạn xe bị che/có association khó; model ReID đổi ID tại frame này.
- Quyết định: giữ một ID 5 xuyên suốt.
- Lý do: gold và nhãn cuối không có ID switch; model là evidence chẩn đoán, không phải nguồn sửa nhãn.

### Ca 3
- Clip / frame / ID: `clip_01` / 113 / ID 6
- Tình huống: xe gần vùng che khuất và model ReID chuyển từ track 24 sang 31.
- Quyết định: giữ ID 6 và kiểm tra lại continuity ở các frame lân cận.
- Lý do: vị trí/hướng chuyển động liên tục; gold cũng giữ cùng identity, nên đây là lỗi association của model.

## 5. Sửa gì sau khi chấm với gold và sau khi kiểm chéo

Luật nào trong file này hoá ra còn thiếu hoặc còn mơ hồ? Viết lại cho rõ:

- Khi xe rời khung, phải kết thúc track ở frame cuối còn nhìn thấy và không giữ bbox treo sau đó; nếu xuất hiện lại thì tạo track mới theo luật mặc định.
- Khi bbox/identity ở crossing hoặc occlusion không chắc chắn, phải ghi frame + ID vào log review và không sửa theo model nếu chưa có bằng chứng hình ảnh.
