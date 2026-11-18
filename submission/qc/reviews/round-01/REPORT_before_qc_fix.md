# Báo cáo Day 08 — 2A202603119 - Nguyễn Thanh Phong

> Một file duy nhất cho cả bài (đã gộp recipe, QC và ghi chú cá nhân). Mọi con số kèm **mẫu số** và nguồn (file nào). Viết bằng lời của bạn.

## 1. Model và recipe (Part 2–3)

| Lớp             | Model + cỡ                                             | Ngưỡng dùng khi gộp | Căn cứ                                                                                                                          |
| --------------- | ------------------------------------------------------ | ------------------: | ------------------------------------------------------------------------------------------------------------------------------- |
| `person`        | YOLO26s                                                |                0.30 | `r03–r04`; quét trên 19 vật gold: recall tối đa 89%, chưa đạt mục tiêu 90%, nên giữ ngưỡng mặc định.                            |
| `bicycle`       | YOLO26s                                                |                0.30 | Chỉ 1 vật gold; không đủ mẫu để chọn ngưỡng riêng.                                                                              |
| `motorbike`     | YOLO26s                                                |                0.35 | Quét trên 11 vật gold: recall 91%, precision 91%; `r03–r04` cho thấy ngưỡng 0.50 làm mất box.                                   |
| `car`           | YOLO26s                                                |                0.30 | Quét trên 8 vật gold: recall tối đa 88%, chưa đạt mục tiêu 90%; giữ mặc định.                                                   |
| `bus`           | YOLO26s                                                |                0.30 | Không có đủ bằng chứng từ 4 ảnh gold để chọn ngưỡng riêng; dùng mặc định.                                                       |
| `truck`         | YOLO26s                                                |                0.30 | Chỉ 1 vật gold; không đủ mẫu để chọn ngưỡng riêng.                                                                              |
| `three_wheeler` | YOLOE-26s; prompt `tuk-tuk`, `cyclo`, `cargo tricycle` |                0.30 | `r05`: tìm được hai xích lô ở `img_032` nhưng sót chiếc bên phải; quét trên 7 vật gold cho recall tối đa 86%, nên giữ mặc định. |

- Nguồn ngưỡng: `submission/qc/threshold_sweep.md` và `.csv` (4 ảnh gold). Chuỗi dùng ở Part 4: `--thr "motorbike=0.35" --conf 0.3`.
- Lệnh `merge_prelabels.py` đã dùng: `python scripts/merge_prelabels.py work/prelabel/yoloe_all/pre.coco.json work/prelabel/y26_all/pre.coco.json --thr "motorbike=0.35" --conf 0.3 --iou 0.6 --split "batch,gold" --out work/prelabel/merged_review/pre.coco.json`. Kết quả: 373 box trên batch + gold (nguồn: đầu ra lệnh gộp).
- SAM đã thử: **SAM 2.1 base** (`sam2.1_b.pt`) trên CPU, 10 mask/2 ảnh ở `r06` (4,38 giây/ảnh). Ở `img_013`, SAM tạo mask từ box người; xe ba bánh không có mask vì YOLO26 không cung cấp box lớp này.
- Lỗi không sửa được bằng ngưỡng: YOLO26 không có lớp `three_wheeler` trong bộ lớp COCO; cần nguồn YOLOE và vẫn phải duyệt bằng mắt.
- Ví dụ box điểm cao mà sai hoặc điểm thấp mà đúng: **chưa kết luận**, sẽ ghi sau khi duyệt ảnh ở Part 4.
- Giới hạn: chỉ có 4 ảnh gold; ngưỡng được chọn và số đo máy được tính trên cùng tập này nên precision/recall về sau có thể lạc quan. Các lớp chỉ có 1 vật gold không đủ mẫu để suy ra ngưỡng riêng.

## 2. AI error profile (từ `qc/review_stats.md`)

| Trạng thái |  Số |           Tỉ lệ | Mẫu số                  |
| ---------- | --: | --------------: | ----------------------- |
| accepted   | 197 | 197/313 = 62,9% | 313 box máy             |
| edited     |  86 |  86/313 = 27,5% | 313 box máy             |
| deleted    |  28 |   28/313 = 8,9% | 313 box máy             |
| added      |  74 |  74/362 = 20,4% | 362 đối tượng sau duyệt |

- Nguồn: `submission/qc/review_stats.md` sau lượt sửa cuối; script đã loại 29 đối tượng quanh bẫy khỏi bảng AI error profile.
- Có 5 trường hợp đổi lớp (được tính trong deleted + added); số vật máy sót thật ước tính 74 − 5 = 69.
- Theo số lượng tuyệt đối, `person` (29 added) và `motorbike` (26 added) là hai lớp phải vẽ bổ sung nhiều nhất; số này chưa chuẩn hóa theo số vật của từng lớp.
- Máy so với gold: TP=37, FP=5, FN=10; precision 37/42 = 88,1%, recall 37/47 = 78,7% (4 ảnh gold, 47 đối tượng được chấm; nguồn: `review_stats.md`).

## 3. Thiên kiến tự động hóa và ảnh bẫy

- Lượt duyệt đầu bắt được 8/12 bẫy = 66,7%, dưới ngưỡng 10/12. Bốn bẫy lọt ban đầu: `img_034` (loose), `img_004` (wrong_class), `img_022` (extra), `img_027` (wrong_class).
- Sau khi xem đáp án và sửa lại, kết quả cuối là 12/12 = 100% (loose 3/3, missing 3/3, extra 3/3, wrong_class 3/3; nguồn: `submission/qc/review_stats.md`). Kết quả này là **sau sửa**, không phải tỷ lệ bắt bẫy mù lần đầu.
- Lượt đầu giữ nguyên 205/313 box máy nhưng vẫn lọt 4/12 bẫy: tỷ lệ accepted tự nó không chứng minh đã phát hiện hết lỗi. Cần quét ảnh trước khi xem box và kiểm riêng các lớp, box thừa, box lỏng.

## 4. Máy có đáng dùng không?

- Thời gian do học viên báo trong `submission/time_log.csv`: gán tay 4 ảnh gold hết 39 phút = 9,75 phút/ảnh; duyệt 24 ảnh có pre-label hết 40 phút = 1,67 phút/ảnh. Chênh lệch thô là khoảng 82,9% thời gian/ảnh.
- So sánh thời gian chỉ mang tính mô tả: gold và batch là các ảnh khác nhau, số vật mỗi ảnh khác nhau, và sau 4 ảnh gold người gán đã quen hơn. Không thể quy toàn bộ chênh lệch cho model.
- Đánh giá theo số hiện có: pre-label giúp giảm thời gian thao tác báo cáo, nhưng cần duyệt kỹ vì 74/362 đối tượng sau duyệt được thêm và lượt đầu chỉ bắt 8/12 bẫy. Ưu tiên dùng YOLO26s cho 6 lớp COCO và YOLOE làm gợi ý `three_wheeler`, luôn có người kiểm.
- Ba quy tắc cho lượt duyệt sau (có thể kiểm tra được):
  1. Ẩn nhãn và đếm vật theo lớp trước khi hiện box trên 24/24 ảnh; ghi dấu hoàn tất từng ảnh.
  2. Kiểm mọi box theo thứ tự bỏ sót → thừa → sai lớp → lỏng; ghi lại số box thêm/xóa/sửa sau mỗi nhóm 10 ảnh.
  3. Nghỉ 3–5 phút sau khoảng 10 ảnh và kiểm lại các box điểm cao ở lớp dễ nhầm (`car`/`three_wheeler`, `motorbike`/`bicycle`).

## 5. Mask SAM cho 3 vật (Part 5)

| Ảnh           | SAM (phiên bản)                   | Số click                    | Mask gồm người lái / hàng hoá?                                                               | Lỗi quan sát                                                                           | File trong `masks/`         |
| ------------- | --------------------------------- | --------------------------- | -------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- | --------------------------- |
| `img_013.jpg` | SAM 2.1 base (`sam2.1_b.pt`), CPU | 2 (1 dương, 1 âm; thêm box) | Mask chủ yếu là người lái, không gồm cả xe.                                                  | Vùng chân/chỗ ngồi sát thân xe khó phân định bằng một cặp click; 32.796 px, 2 polygon. | `driver_img013.jpg`         |
| `img_032.jpg` | SAM 2.1 base, CPU                 | 2 (1 dương, 1 âm; thêm box) | Mask bám xích lô ở giữa ảnh, không lấy toàn bộ người đứng cạnh.                              | Các khoảng hở trong khung và mép xe cần kiểm bằng mắt; 55.362 px, 1 polygon.           | `cyclo_img032.jpg`          |
| `img_003.jpg` | SAM 2.1 base, CPU                 | 2 (1 dương, 1 âm; thêm box) | Mask gồm mái, thùng xe và phần hàng nằm trong thùng; người nhìn thấy trên xe cần nhãn riêng. | Ranh giới giữa xe, hàng hóa và người chưa rõ với một mask; 106.409 px, 2 polygon.      | `cargo_tricycle_img003.jpg` |

- Nguồn: ba ảnh overlay thực tế trong `submission/masks/` và đầu ra của `sam2_points.py`. SAM dùng click + box để tạo mask, không tự quyết định lớp.
- Đối chiếu guideline v1.0 mục 4.2: quy tắc bám đường biên phần nhìn thấy và tách hai vật chạm nhau giúp kiểm mask; ranh giới hàng hóa gắn xe, người ngồi trong xe và khoảng hở khung xe là các ca cần xem lại khi calibration.
- Interactor SAM trực tiếp trong CVAT: chưa thử; ba kết quả trên được tạo bằng script offline.

## 6. Calibration (Part 6)

- Đã ghi 3 quyết định trong `submission/qc/calibration_decisions.csv`: `CAL-01` (lỗi máy ở `img_032.jpg`), `CAL-02` (hàng rời trên xe ba gác ở `img_003.jpg`) và `CAL-03` (kính/người lái trong mask ở `img_013.jpg`).
- Quyết định ảnh hưởng rõ nhất đến cách vẽ mask là `CAL-02`: mái và thùng gắn cố định thuộc xe, hàng rời và người tách khỏi mask xe. `CAL-03` phân biệt kính thật với lỗ hở và tách người lái khỏi xe.
- Các quy tắc bổ sung có ảnh dẫn và mã ca trong `submission/guidelines/guideline_v1.1_calibrated.md`. Taxonomy tăng `version` lên `v1.1`; lớp, thuộc tính và mục `rules` giữ nguyên.
- Nguyên nhân: `CAL-01` là giới hạn YOLOE (bỏ sót vật); `CAL-02` và `CAL-03` là hai câu hỏi mở của guideline v1.0 cần làm rõ. Năm câu hỏi mở khác chưa đủ bằng chứng trong ba ca này để chốt.

## 7. QC và quyết định (Part 6)

- Release candidate từ `build_release.py`: 24 ảnh batch, 392 annotation (gồm 18 `ignore_region`), taxonomy v1.1; nguồn `submission/release/release_manifest.json`.
- Mẫu QC từ `qc_sample.py --n 5 --seed 42`: 5/24 ảnh, 0 ca `can_review` + 5 ảnh ngẫu nhiên (`img_003`, `006`, `014`, `031`, `035`), 75 đối tượng. Ảnh gốc và overlay đã được ChatGPT hỗ trợ xem; học viên cần xác nhận lại phiếu `qc_review.csv` trước khi dùng làm kết luận cuối.
- Kết quả trong `submission/qc/qc_report_auto.md`: lỗi theo **đối tượng** 2/75 = 2,7%; lỗi theo **ảnh** 1/5 = 20,0%. Hai lỗi đều là box `person` thừa trên quần áo treo ở `img_031.jpg`, object ID 339 và 340. Bốn ảnh mẫu còn lại chưa thấy lỗi rõ trong lượt kiểm này.
- Mẫu chỉ có 5 ảnh; với 1/5 ảnh lỗi, cận trên Wilson 95% của tỉ lệ ảnh lỗi thật khoảng 62%. Mẫu nhỏ không chứng minh lô đạt ngưỡng 2%.
- Script kiểm được định dạng, lớp, kích thước box và số đo từ phiếu; không tự biết box có ôm đúng vật thật, vật bị cả người và máy bỏ sót, hoặc người duyệt có tuân thủ checklist. Lượt QC hiện có AI hỗ trợ nên cần học viên xác nhận bằng mắt.

**Quyết định: Rework**, ngày 2026-11-18. Lý do: 1/5 ảnh lỗi = 20,0% > ngưỡng 2%; hai box `person` thừa trên cùng ảnh. Đây là quyết định cho release candidate hiện tại, chưa phải xác nhận phát hành.

Việc tiếp theo: học viên kiểm lại `img_031.jpg` trong CVAT, xóa box 339 và 340 nếu xác nhận là quần áo treo, export nhãn sửa thành `submission/labels/qc_fixed.json`, chạy lại `build_release.py`, lấy mẫu và kiểm lại vòng QC. Giữ phiếu hiện tại làm bằng chứng lỗi trước sửa.

## 8. Ghi chú cá nhân

- Công cụ AI đã dùng: YOLO26s tạo box cho các lớp COCO, YOLOE-26s gợi ý `three_wheeler`, SAM 2.1 base tạo mask từ click/box; ChatGPT hỗ trợ giải thích lệnh, đối chiếu ảnh overlay và soạn phần mô tả số đo. Nhãn batch được người học duyệt trong CVAT; các nhận xét cần được người học kiểm lại trên ảnh gốc.
- Gold có bị nhiễm không: theo xác nhận của học viên, 4 ảnh gold được tự gán tay và **chưa xem hay nhập** pre-label model trước khi hoàn tất gold. Trong `work/prelabel/gold_model_*` có các file chạy model riêng; chúng không được dùng để tạo `gold_manual.json` theo lời học viên.
- Môi trường: Windows 11, CPU (không GPU), Python trong `.venv`, CVAT local bằng Docker. YOLO26s, YOLOE-26s và SAM 2.1 base đã chạy được.
- Cache `data/cache/`: không dùng cho các lần chạy đã ghi; các file pre-label được tạo bằng script trên máy.
- Vướng mắc thao tác đã xử lý: Task CVAT ban đầu thiếu bộ 8 nhãn nên import lỗi; file COCO export cần đưa về đúng đường dẫn/tên; một lần export có tên `batch_reviewed..json` nên `validate_labels.py --all` không nhận.
- Điều cần kiểm ở lần làm tiếp: xác nhận nhãn Project/Task trước khi import và giữ bằng chứng lượt review đầu riêng với kết quả sau khi xem đáp án bẫy.
