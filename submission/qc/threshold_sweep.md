# Quét ngưỡng theo lớp

Mục tiêu recall: 90%. Ảnh dùng để quét: 4.

| Lớp           | Ngưỡng gợi ý | Lý do                                                                                                   |
| ------------- | ------------ | ------------------------------------------------------------------------------------------------------- |
| person        | (mặc định)   | không ngưỡng nào <= 0.5 đạt recall 90% (tối đa 89%, 19 vật): dùng ngưỡng mặc định, nói rõ trong báo cáo |
| bicycle       | (mặc định)   | chỉ 1 vật trong ảnh gold (< 5): mẫu quá ít, KHÔNG chọn ngưỡng theo lớp này, dùng ngưỡng mặc định        |
| motorbike     | 0.35         | recall 91% (>= 90%), precision 91% (11 vật gold)                                                        |
| car           | (mặc định)   | không ngưỡng nào <= 0.5 đạt recall 90% (tối đa 88%, 8 vật): dùng ngưỡng mặc định, nói rõ trong báo cáo  |
| truck         | (mặc định)   | chỉ 1 vật trong ảnh gold (< 5): mẫu quá ít, KHÔNG chọn ngưỡng theo lớp này, dùng ngưỡng mặc định        |
| three_wheeler | (mặc định)   | không ngưỡng nào <= 0.5 đạt recall 90% (tối đa 86%, 7 vật): dùng ngưỡng mặc định, nói rõ trong báo cáo  |

Dán vào lệnh merge_prelabels (lớp không có trong chuỗi dùng `--conf` mặc định 0.3): `--thr "motorbike=0.35"`
