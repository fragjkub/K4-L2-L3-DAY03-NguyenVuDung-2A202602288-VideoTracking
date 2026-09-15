# Báo cáo Ngày 3 — Tracking Annotation

Chép file này thành `reports/REPORT.md` rồi điền. Giữ nguyên các tiêu đề.

Họ tên / nhóm: `Nguyễn Vũ Dũng`
Ngày: `15/9/2026`

---

## 1. Quá trình gán nhãn

| Mục | Giá trị |
| --- | --- |
| Công cụ | CVAT Track Mode, export MOT 1.1 |
| Thời gian gán `clip_02` (warm-up) | `20` phút |
| Thời gian gán `clip_01` | `33` phút |
| Số track đã vẽ trong `clip_01` | 8 |
| Số keyframe trung bình mỗi track | Chưa có log CVAT; file MOT có 634 bbox / 8 track = 79.25 quan sát mỗi track, không đồng nhất với keyframe |

Ba tình huống khó nhất khi gán clip này, và bạn xử lý thế nào:

1. Xe bị che một phần: giữ nguyên ID trong thời gian che ngắn, đặt bbox theo phần nhìn thấy và kiểm tra lại ở frame giữa.
2. Xe ra/vào gần rìa ảnh: dùng `outside` đúng frame rời khung, không kéo bbox theo phần không còn nhìn thấy.
3. Hai xe đi gần hoặc cắt nhau: tua chậm, đối chiếu đặc điểm hình dạng/vị trí trước và sau giao nhau để không đổi ID.

## 2. Tự kiểm và kiểm chéo

Ba lượt tua bắt được gì (lượt 1 nhìn ID, lượt 2 frame đầu/cuối, lượt 3 frame giữa):
| Thời gian gán `clip_02` (warm-up) | 20 phút |
| Thời gian gán `clip_01` | 33 phút |

- Lượt 1: rà tính liên tục của ID và các điểm xe bị che/cắt nhau; không phát hiện ID switch trong nhãn cuối.
- Lượt 2: rà frame bắt đầu/kết thúc, bbox treo và `outside`; validator đạt 0 lỗi, còn cảnh báo bbox đứng yên ở track 2 của `clip_01`.
- Lượt 3: rà hình học bbox và interpolation ở các đoạn xe đổi hướng/che khuất; ghi nhận cần kiểm tra kỹ các bbox có IoU thấp trong kết quả đánh giá.

Kiểm chéo với: chưa có reviewer được ghi nhận. Chi tiết `reports/review_partner.md` chưa tồn tại trong repo.
Số lỗi bạn tìm được trong bản của bạn ấy: chưa có dữ liệu. Số lỗi bạn ấy tìm được trong bản của bạn: chưa có dữ liệu.

Ca nào hai người quyết khác nhau, và luật nào còn thiếu trong `GUIDELINE_MINI.md`?

Chưa có biên bản kiểm chéo nên chưa xác định được ca bất đồng. Luật cần làm rõ thêm là tiêu chí kết thúc track khi xe chỉ còn một phần rất nhỏ ở rìa ảnh và cách phân biệt che khuất ngắn với xe đã rời khung.

## 3. Pre-gold lock và chấm trước/sau rework
| SHA-256 file nhãn hiện tại (không phải pre-gold) | `054d08ab8100b19bb0613beb1a300e0553e5384b7dc3c8275141125f2665b870` |
| Thời điểm khóa | Chưa có log khóa pre-gold |
| Số row / frame / track trước khi mở reference | Chưa có snapshot để xác minh; nhãn hiện tại có 634 row / 190 frame / 8 track |

| | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| Bản pre-gold | Chưa có artifact | - | - | - | - | - | - | - | - | - |
| Sau rework | 0.7854 | 0.7675 | 0.8051 | 0.8762 | 0.9445 | 0.8831 | 0.8628 | 64 | 3 | 0 |

Qua cổng (`IDF1 >= 0.80`, `MOTA >= 0.75`, `MOTP >= 0.70`): **có**

Sau khi đọc danh sách lỗi, bạn đã sửa cụ thể những gì? Ghi theo frame và ID:

| Loại lỗi | Frame | ID | Đã sửa thế nào |
| --- | --- | --- | --- |
| Chưa có change log | - | - | Không có bản ghi sửa theo frame/ID để xác minh |
| Chưa có change log | - | - | Không có bản ghi sửa theo frame/ID để xác minh |
| Chưa có change log | - | - | Không có bản ghi sửa theo frame/ID để xác minh |

## 4. Kết quả model: ByteTrack control vs ReID treatment

Cấu hình từ `outputs/model_run_config.json`:

| Mục | Giá trị |
| --- | --- |
| Python / ultralytics / torch / lap | Python 3.13.15 / ultralytics 8.4.145 / torch 2.11.0+cpu / lap 0.5.13 |
| weights / hai tracker | yolo26n.pt / bytetrack.yaml / configs/trackers/botsort-reid.yaml |
| conf / IoU / imgsz / classes | 0.25 / 0.7 / 960 / 2, 5, 7 |
| device | cpu |

| So sánh | HOTA | DetA | AssA | LocA | IDF1 | MOTA | MOTP | FP | FN | IDSW |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| bạn vs gold | 0.7854 | 0.7675 | 0.8051 | 0.8762 | 0.9445 | 0.8831 | 0.8628 | 64 | 3 | 0 |
| ByteTrack control vs gold | 0.7085 | 0.6487 | 0.7761 | 0.8463 | 0.8746 | 0.7487 | 0.8226 | 88 | 54 | 2 |
| BoT-SORT + ReID vs gold | 0.7635 | 0.7110 | 0.8204 | 0.8721 | 0.9001 | 0.7923 | 0.8595 | 91 | 26 | 2 |
| ReID vs bạn | 0.7366 | 0.6778 | 0.8020 | 0.8863 | 0.8664 | 0.7334 | 0.8770 | 86 | 82 | 1 |

## 5. Phân tích — năm câu hỏi

**1. MOTA của bạn cao hơn hay thấp hơn IDF1? Nếu MOTA cao mà IDF1 thấp thì điều đó nói gì, và vì sao MOTA không phạt nặng lỗi ID?**

MOTA của bạn thấp hơn IDF1 (0.8831 so với 0.9445). MOTA tính chủ yếu từ FP, FN và IDSW; vì vậy lỗi duy trì identity có thể làm IDF1 giảm nhưng không làm MOTA giảm tương ứng nếu số IDSW nhỏ so với tổng số frame/object. Trên kết quả này, bạn có IDSW = 0, nên MOTA vẫn cao.

**2. ByteTrack control và BoT-SORT + ReID treatment khác nhau thế nào ở IDF1, AssA và IDSW? Dẫn một frame sequence để giải thích treatment tốt hơn, tệ hơn hoặc không đổi đáng kể. Nhắc rõ đây không cô lập causal effect của ReID vì hai tracker implementation khác.**

So với ByteTrack, BoT-SORT + ReID có IDF1 cao hơn (0.9001 so với 0.8746), AssA cao hơn (0.8204 so với 0.7761), còn IDSW không đổi (2 và 2). ByteTrack có IDSW ở frame 59 (GT ID 4) và frame 94 (GT ID 5); ReID có IDSW ở frame 87 (GT ID 5) và frame 113 (GT ID 6). Trên clip này treatment tốt hơn ở các metric identity tổng thể, nhưng đây không cô lập causal effect của ReID vì hai tracker implementation khác nhau.

**3. DetA, FP và FN đổi thế nào? Lỗi còn lại là detector hay association?**

So với ByteTrack, DetA tăng từ 0.6487 lên 0.7110; FN giảm từ 54 xuống 26, nhưng FP tăng nhẹ từ 88 lên 91. Vì coverage/detection thay đổi rõ cùng với association, lỗi còn lại không thể quy hoàn toàn cho detector hoặc association chỉ từ các metric này; cần xem frame evidence để tách hai loại lỗi.

**4. Một chỗ bạn đúng và ReID sai (frame, ID, vì sao):**

Frame 113, GT ID 6: nhãn của tôi giữ ID 6 liên tục và đạt `IDSW = 0` khi so với gold, trong khi ReID đổi từ track 24 sang 31. Vì vậy ở điểm này ReID bị lỗi association; không nên dùng output model để sửa annotation.

**5. Một chỗ ReID làm bạn xem lại annotation (frame, ID, vì sao), hoặc lý do evidence cho thấy model sai:**

Frame 94, GT ID 5: ByteTrack đổi từ track 23 sang 32, còn ReID không ghi nhận switch ở frame này. Đây là tín hiệu để xem lại continuity của ID 5, nhưng gold và nhãn của tôi đều không có ID switch; evidence cho thấy ByteTrack sai, không phải annotation cần đổi.

## 6. Nếu phải gán thêm 10 clip nữa

Bạn sẽ sửa gì trong `GUIDELINE_MINI.md`, và đổi gì trong quy trình làm việc của mình?

Tôi sẽ bổ sung ngưỡng kết thúc track ở rìa ảnh, ví dụ phải dùng `outside` tại frame cuối còn nhìn thấy vật thể và không giữ bbox thêm sau đó; đồng thời ghi riêng trường hợp xe bị che ngắn và xe rời khung rồi quay lại. Tôi cũng sẽ ghi frame/ID ngay khi gặp ca mơ hồ, đặt keyframe dày hơn ở đoạn crossing hoặc đổi hướng, chạy validator sau mỗi lần export, khóa pre-gold và lưu manifest trước khi mở reference/model. Peer review sẽ dùng bảng bắt buộc `frame + ID + lỗi + cách sửa + closure`.

## 7. Tệp đã nộp

- [x] `annotations/clip_01/gt.txt`
- [x] `annotations/clip_02/gt.txt`
- [x] `evidence/pre-gold/clip_01/gt.txt` và `manifest.json`
- [x] `GUIDELINE_MINI.md` đã điền
- [x] `outputs/eval_vs_gold.json`
- [x] `outputs/model_bytetrack_clip_01.txt`
- [x] `outputs/model_reid_clip_01.txt`
- [x] `outputs/model_run_config.json`
- [x] `outputs/eval_bytetrack_vs_gold.json`, `outputs/eval_reid_vs_gold.json`, `outputs/eval_reid_vs_me.json`
- [ ] `reports/review_partner.md` 
- [x] `reports/REPORT.md` 
