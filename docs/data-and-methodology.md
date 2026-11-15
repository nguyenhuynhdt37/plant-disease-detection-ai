# Dataset, phương pháp và định nghĩa metric

## Dataset

Nghiên cứu sử dụng PlantVillage với **38 nhãn** (xem `metadata/class_map.csv`), kết hợp tên cây và bệnh/tình trạng healthy. Audit trong `reports/02_data_audit/final_eda_insights.txt` ghi:

| Variant | Ảnh | Lớp |
|---|---:|---:|
| Color | 54,305 | 38 |
| Grayscale | 54,305 | 38 |
| Segmented | 54,306 | 38 |

Các variant không nhất thiết có file tương ứng 1:1; báo cáo nhấn mạnh segmented hơn 1 ảnh. Với **color**, lớp nhiều nhất là `Orange___Haunglongbing_(Citrus_greening)` (5,507), ít nhất `Potato___healthy` (152), chênh khoảng **36.23 lần**. Audit dHash báo 30 cặp trùng nội lớp và 0 cặp trùng liên lớp; **dHash bằng nhau không đủ chứng minh đã loại trừ mọi near-duplicate hoặc leakage qua train/test**.

## CSV contract

Các file `metadata/train.csv`, `val.csv`, `test.csv` chứa `relative_path,class_name,class_id`; `class_map.csv` chứa `class_name,class_id`. IDs dựa vào mapping trong CSV, **không tự sắp xếp lại nhãn lúc infer**. Ảnh thô không được commit. Hàm tạo metadata split trong `src.data` dùng stratified 80% train / 10% val / 10% test theo seed 42 mặc định; đó là **hành vi của hàm**, không phải bằng chứng mọi experiment trước đều chạy lại bằng hàm này.

## Preprocessing

`get_basic_transform(image_size)`: resize, ToTensor, Normalize với ImageNet mean `[0.485,0.456,0.406]` và std `[0.229,0.224,0.225]`. `get_light_augmentation_transform`: horizontal flip (p=0.5), rotation ±15°, ColorJitter nhẹ; test/val không augment ngẫu nhiên. Phase 04 khảo sát từng kỹ thuật và RandomErasing.

## Imbalance strategies

- **WeightedRandomSampler:** trọng số mẫu từ inverse frequency theo class, replacement mặc định True.
- **Class-weighted loss / FocalLoss / effective-number weights:** các thử nghiệm ở phase loss; không suy ra mọi experiment dùng cùng một loss.
- **Hybrid resampling:** undersample majorities và oversample minorities về `target_size` khi cần.
- **EarlyStopping:** chọn best checkpoint trên **validation macro F1**, không theo test.

## Metrics

- **Accuracy:** tỷ lệ dự đoán đúng toàn tập; có thể che khuất class hiếm.
- **Macro F1:** trung bình F1 không trọng số theo lớp, ưu tiên đánh giá đồng đều giữa lớp.
- **Minority Recall:** trung bình recall của các lớp được định nghĩa là minority **trong experiment**. Phase 08 xây danh sách minority theo 25th percentile class counts train; tuyệt đối không so sánh khi định nghĩa minority thay đổi mà không ghi rõ.
- **Worst-class Recall:** recall thấp nhất trong tất cả class.
- **Confusion matrix:** giúp xác định các cặp bệnh dễ nhầm lẫn.

Điểm cần chú ý: có 30 cặp duplicate nội lớp theo báo cáo audit, nhưng chưa có kiểm chứng ở đây rằng các cặp đã được loại khỏi các split. Muốn kết luận không leakage cần so hash/near-duplicate **giữa train/val/test**.
