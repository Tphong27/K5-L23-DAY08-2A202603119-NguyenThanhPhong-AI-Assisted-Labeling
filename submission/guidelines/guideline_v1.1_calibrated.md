# Label contract — Phố châu Á — BỔ SUNG v1.1 — 2A202603119 - Nguyễn Thanh Phong

Đây là phần bổ sung cho guideline v1.0, áp dụng cho các ca đã đối chiếu trong `submission/qc/calibration_decisions.csv`. Các lớp và ngưỡng QC trong taxonomy không đổi.

## 1. Câu hỏi mở đã chốt

| Câu hỏi ở v1.0 | Quyết định v1.1 | Ảnh ví dụ | Ca |
|---|---|---|---|
| Mục 6.3: hàng rời chở trên xe có thuộc box/mask xe không? | Box ôm kết cấu nhìn thấy của xe, gồm mái và thùng gắn cố định. Mask chỉ lấy kết cấu gắn xe; rau quả/hàng rời và người không thuộc mask xe. Hàng rời nhô xa không làm box xe nở theo hàng. | `img_003.jpg` | `CAL-02` |
| Mục 6.5: kính chắn gió/cửa sổ xe thuộc mask không? | Tấm kính hiện hữu là bộ phận của xe, kể cả khi nhìn xuyên qua; lỗ hở không có kính không thuộc mask xe. Người nhìn thấy qua kính hoặc trong khoang có mask `person` riêng, không gộp vào xe. | `img_013.jpg` | `CAL-03` |

Các câu hỏi 6.1, 6.2, 6.4, 6.6 và 6.7 của v1.0 chưa có đủ bằng chứng từ ba ca này để chốt; không suy diễn thành quy tắc mới.

## 2. Ca khó mới và cách áp dụng

| Ảnh | Tình huống | Bằng chứng | Quyết định và quy tắc bổ sung | Ca |
|---|---|---|---|---|
| `img_032.jpg` | Xích lô ở mép phải đang đỗ, không có box YOLOE. | Ảnh pilot `work/prelabel/t_yoloe/viz/img_032.jpg` chỉ hiển thị hai box `three_wheeler` ở trái/giữa. | Xích lô đang đỗ vẫn gán `three_wheeler` nếu nhìn rõ và đủ lớn. Khi duyệt pre-label, quét cả mép ảnh và vẽ bổ sung vật bị máy bỏ sót. | `CAL-01` |
| `img_003.jpg` | Xe ba gác có mái, thùng, hàng rau quả và người trong xe. | `submission/masks/cargo_tricycle_img003.jpg`. | Mái/thùng gắn cố định thuộc xe; hàng rời và người tách khỏi mask xe. Không tạo box hàng vì taxonomy không có lớp hàng hóa. | `CAL-02` |
| `img_013.jpg` | Người lái cạnh kính/khung xe ba bánh. | `submission/masks/driver_img013.jpg`. | Tách mask người lái khỏi xe. Kính thật thuộc xe; khoảng hở không có kính không thuộc xe. | `CAL-03` |

## 3. Lịch sử thay đổi

| Phiên bản | Thay đổi | Bằng chứng |
|---|---|---|
| v1.0 | Bản label contract của giảng viên. | `guideline/guideline_v1.0_label_contract.md` |
| v1.1 | Làm rõ xích lô đang đỗ và bước tìm vật máy bỏ sót; chốt ranh giới hàng rời, kính chắn gió và người lái trong mask xe. Không đổi lớp hay ngưỡng QC. | `CAL-01` đến `CAL-03`; `img_032.jpg`, `img_003.jpg`, `img_013.jpg` |