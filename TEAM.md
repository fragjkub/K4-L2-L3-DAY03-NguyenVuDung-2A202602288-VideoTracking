# Khai báo nhóm — chỉ điền khi làm nhóm

Nếu làm cá nhân, không cần đưa file này vào repo nộp. Nếu làm nhóm, mỗi thành
viên nộp repo cá nhân và sao chép/điền cùng bảng thành viên dưới đây trong repo
của mình.

## Nhóm

- Tên nhóm: Không áp dụng - làm cá nhân
- Kênh liên lạc dùng để phối hợp: Không áp dụng
- Cách phân chia review và kiểm chứng evidence: Tự kiểm ba lượt; đối chiếu validator và các file evaluation trong `outputs/`

| Họ và tên | MSSV | Vai trò / phần việc | Artifact tự sở hữu |
| --- | --- | --- | --- |
| Nguyễn Vũ Dũng | Không cung cấp MSSV | Tự gán nhãn, tự kiểm, chạy evaluation và viết báo cáo | `annotations/`, `outputs/`, `GUIDELINE_MINI.md`, `reports/REPORT.md` |

## Phần đóng góp và học được của người nộp repo này

- Họ và tên / MSSV: Nguyễn Vũ Dũng / Không cung cấp MSSV
- Tôi trực tiếp tạo hoặc chỉnh sửa những artifact nào: annotation của hai clip, guideline, output evaluation và report.
- Finding hoặc quyết định annotation tôi chịu trách nhiệm: giữ ID qua occlusion/crossing; dùng track mới sau khi xe rời khung; kiểm tra các frame 87 và 113 nơi model đổi ID.
- Tôi học được gì về identity, occlusion, MOT hoặc ReID: IDF1/AssA phản ánh identity; ReID có thể giảm switch nhưng không đồng nghĩa detector tốt hơn.
- Điều tôi đã kiểm lại độc lập trước khi nộp: validator hai clip đạt 0 lỗi; report không còn placeholder; metric model và gate đã đối chiếu từ JSON.
