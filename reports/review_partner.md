# Peer review — Day 3

| Trường | Giá trị |
| --- | --- |
| Author | Nguyễn Vũ Dũng |
| Reviewer | N/A - không có peer reviewer được ghi nhận |
| Pair ID | N/A - làm cá nhân |
| CVAT version | Không ghi nhận |
| Thời điểm review | 15/09/2026; tự kiểm thay cho peer review |

## Danh sách finding

Không có biên bản peer review độc lập. Các mục dưới đây là các điểm tự kiểm cần theo dõi, không được xem là finding của reviewer.

| # | CVAT frame | MOT frame | ID | Loại lỗi | Quan sát + rule áp dụng | Cách sửa đề xuất | Closure: fixed / not-a-defect / needs-review |
| ---: | ---: | ---: | ---: | --- | --- | --- | --- |
| 1 | 51-53 | 51-53 | 4 | endpoint/ghost bbox | Diagnostics ghi bbox trước khi track tham chiếu xuất hiện; cần kiểm tra frame bắt đầu theo rule entry. | Xác minh frame đầu tiên nhận diện được xe; dùng `outside` nếu đó là track cũ còn treo. | needs-review |
| 2 | 59-78 | 59-78 | 5 | endpoint/ghost bbox | Diagnostics ghi bbox trước khi track tham chiếu xuất hiện; không suy đoán identity khi xe chưa xác định rõ. | Kiểm tra frame bắt đầu và chỉ tạo track khi nhận diện được xe bốn bánh. | needs-review |
| 3 | 149-171 | 149-171 | 4, 8 | endpoint/ghost bbox | Diagnostics ghi bbox còn sau khi track tham chiếu rời khung; áp dụng luật kết thúc ở frame cuối còn nhìn thấy. | Kiểm tra `outside` tại endpoint, không giữ bbox treo. | needs-review |

## Reviewer checklist

| Hạng mục | PASS / FINDING / N/A | Frame–ID–evidence |
| --- | --- | --- |
| Có tối thiểu 6 track hợp lệ; chỉ gồm xe bốn bánh | PASS | 8 track trong `clip_01`; validator 0 lỗi |
| Một xe giữ một ID; không reuse ID cho xe khác | PASS | `outputs/eval_vs_gold.json`: IDSW = 0 |
| Occlusion ngắn giữ ID; crossing không đổi ID | PASS | Diagnostics không ghi fragmentation cho nhãn cuối |
| Entry/exit đúng; không box treo sau khi xe rời khung | FINDING | Diagnostics ghi ghost track ở các đoạn 51-53, 59-78, 79-100 và 149-171 |
| Bbox ôm phần nhìn thấy, không đoán phần bị che/ngoài khung | PASS | Validator 0 lỗi; độ khít được phản ánh bằng LocA/MOTP |
| Frame giữa hai keyframe không bị interpolation drift | PASS | Không có lỗi định dạng; đã rà các đoạn đổi hướng/che khuất |
| Export đúng MOT 1.1; frame bắt đầu từ 1; cột 2 là track ID | PASS | Validator hai clip đạt 0 lỗi |
| Mọi finding có cách sửa và closure do tác giả điền | FINDING | Không có peer finding độc lập; các điểm tự kiểm giữ trạng thái needs-review |

## Self-QC attestation của reviewer

| Lượt | PASS / ĐÃ SỬA / NEEDS-REVIEW | Frame–ID–evidence |
| --- | --- | --- |
| 1 — identity/timeline | PASS | `clip_01`: 8 track, IDSW = 0 |
| 2 — endpoint/scope | ĐÃ SỬA | Đã bổ sung luật `outside` và entry/exit trong `GUIDELINE_MINI.md` |
| 3 — geometry/interpolation | PASS | Validator 0 lỗi; xem lại các bbox IoU thấp trong evaluation |

## Exit ticket

1. Finding quan trọng nhất và rule dùng để kết luận: endpoint/ghost bbox; phải kết thúc track ở frame cuối còn nhìn thấy và không kéo bbox sau khi xe rời khung.
2. Một finding tác giả đóng là `not-a-defect`, kèm lý do (nếu có): frame 87/113 là model switch, không phải lỗi annotation vì gold và nhãn cuối giữ identity.
3. Một rule cần Lab Coach làm rõ (nếu có): ngưỡng tối thiểu để xác định xe rất nhỏ/mờ ở frame bắt đầu.
