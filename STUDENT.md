# Thông tin học viên

- Họ và tên: Nguyễn Thanh Phong
- MSHV: 2A202603119
- Lớp (L2 / L3 / khác): L2
- Tài khoản GitHub: Tphong27
- Repo bài nộp (URL): https://github.com/Tphong27/K5-L23-DAY08-2A202603119-NguyenThanhPhong-AI-Assisted-Labeling
- Commit chốt bài (hash): 995d344f994a81af8e6807c749c6f790f1ef6d64
- Máy: Windows 11, không GPU (torch 2.15.0+cpu, chạy CPU)
- CVAT local: v2.74.1

## Kết quả tóm tắt (điền cuối bài; số lấy từ `submission/qc/*`, kèm mẫu số)

- Máy: accepted 195/313 (62,3%); edited 86/313 (27,5%); deleted 30/313 (9,6%); added 74/360 (20,6%).
- So với gold: precision của **máy** 37/42 = 88,1%; recall 37/47 = 78,7%.
- Bắt bẫy: lượt đầu 8/12 = 66,7%; sau khi xem đáp án và sửa 12/12 = 100%.
- Thời gian: gán tay 39/4 = 9,75 phút/ảnh; duyệt có pre-label 40/24 = 1,67 phút/ảnh.
- QC vòng 1: 1/5 ảnh lỗi (20,0%), 2/75 đối tượng lỗi (2,7%); đã sửa hai box `person` thừa trên `img_031`. QC vòng 2: 0/5 ảnh lỗi, 0/73 đối tượng lỗi, xác nhận đồng ý kết quả ngày 2026-11-18.
- **Quyết định cuối:** Release theo tự xác nhận và kết quả QC vòng 2; `qc_report.py` cũng gợi ý Release. Mẫu 5 ảnh có giới hạn thống kê, không bảo đảm tỉ lệ lỗi thực ≤ 2%. Bản COCO 392 annotation trước sửa không còn, nhưng hồ sơ QC vòng 1 đã lưu trong `submission/qc/reviews/round-01/`.
