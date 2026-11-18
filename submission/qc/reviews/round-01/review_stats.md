# Số đo review (det)

- Ảnh tính: 24 (batch,gold); box máy: 313; đối tượng sau duyệt: 362; ngưỡng accepted: IoU >= 0.9
- Đã **loại 29 đối tượng nằm quanh các bẫy** khỏi bảng này: lỗi nhân tạo không phản ánh chất lượng máy.

## 1. Bốn trạng thái (AI error profile)
| Trạng thái | Số | Tỉ lệ | Mẫu số |
|---|---|---|---|
| accepted (giữ nguyên) | 197 | 197/313 = 62.9% | 313 box máy |
| edited (sửa) | 86 | 86/313 = 27.5% | 313 box máy |
| deleted (xoá) | 28 | 28/313 = 8.9% | 313 box máy |
| added (vẽ thêm) | 74 | 74/362 = 20.4% | 362 đối tượng sau duyệt |

Trong số deleted + added, **5 trường hợp là đổi lớp** (máy gán sai lớp, box đúng chỗ): đó không phải máy bỏ sót. Số vật máy **sót thật** ≈ added − đổi lớp = 69.

### Theo lớp
| Lớp | accepted | edited | deleted | added |
|---|---|---|---|---|
| person | 101 | 43 | 4 | 29 |
| bicycle | 1 | 3 | 1 | 2 |
| motorbike | 47 | 33 | 10 | 26 |
| car | 31 | 3 | 6 | 13 |
| bus | 1 | 1 | 1 | 2 |
| truck | 4 | 0 | 1 | 2 |
| three_wheeler | 12 | 3 | 5 | 0 |

## 2. So với gold (nhãn gán tay từ đầu)
Ảnh gold: 4; đối tượng gold: 47 (mẫu số của recall)
| | TP | FP | FN | Precision | Recall |
|---|---|---|---|---|---|
| Máy (pre-label) | 37 | 5 | 10 | 88.1% (KTC 95%: 75–95%) | 78.7% (KTC 95%: 65–88%) |

*Lạc quan: ngưỡng được chọn trên chính các ảnh gold này (in-sample), và chỉ có 4 ảnh. Đọc khoảng tin cậy, đừng đọc con số đơn.*


(Ảnh gold không nằm trong nhãn sau duyệt: không tính residual / new error. Đó là chủ ý: duyệt lại ảnh bạn vừa tự gán tay sẽ đo trí nhớ, không đo thiên kiến.)

## 3. Ảnh bẫy
- Bắt được 12/12 = 100.0% (KTC 95%: 76–100%) (ngưỡng đạt: 80%). Chỉ 12 bẫy nên khoảng tin cậy rất rộng: đọc cùng danh sách bẫy lọt, đừng kết luận chỉ từ %.
  - loose: 3/3
  - missing: 3/3
  - extra: 3/3
  - wrong_class: 3/3

## Gợi ý đọc số
- `deleted` cao → ngưỡng thấp hoặc ánh xạ nhãn sai; `added` cao → máy bỏ sót (xem lớp nào);
- `accepted` thấp (< 50%) → sửa mỗi box lâu hơn vẽ mới: đổi model/ngưỡng hoặc vẽ tay;
- bắt bẫy < 80% → duyệt lại lô, siết checklist (quét ảnh TRƯỚC khi nhìn nhãn máy).
