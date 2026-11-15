# Kiến trúc code và luồng xử lý

## 1. Phạm vi

Đây là **notebook-first research pipeline**. Không tìm thấy source cho REST API, web frontend hay inference service ở repository snapshot. `src/` chứa helper dùng chung, không phải app entrypoint.

```text
PlantVillage image folders + metadata CSV
                 |
                 v
src.data.PlantDataset -> torchvision transforms -> DataLoader
                 |                            |
                 |               optional WeightedRandomSampler
                 v
src.models (TinyCNN / SimpleCNN / pretrained / ImprovedMobileNetV2)
                 |
                 v
src.train.train_model + validation Macro F1 / EarlyStopping
                 |
         best state_dict (*.pth; local only)
                 |
                 v
src.train.evaluate_model -> per-class metrics / confusion matrix
                 |
                 v
notebooks -> results/<phase>/tables|figures|reports
                 |
                 v
Phase 10 cross-phase research synthesis
```

## 2. Modules

| Module | Trách nhiệm |
|---|---|
| `src/data.py` | `ProjectPaths`, stratified CSV split, `PlantDataset`, deterministic subsets, hybrid resampling, inverse-frequency weights và weighted loader |
| `src/audit.py` | Quét dataset, kích thước/mode ảnh, dHash phát hiện ảnh có hash giống nhau |
| `src/models.py` | `TinyCNN`, `SimpleCNN`, MobileNetV2, EfficientNet-B0, `ImprovedMobileNetV2`; các residual/SE/CBAM helper |
| `src/train.py` | `TrainConfig`, FocalLoss, class-balanced weights, vòng train, EarlyStopping, evaluator |
| `src/utils.py` | Random seed, device CUDA/MPS/CPU, transforms, report utilities |
| `src/visualization.py` | Hàm vẽ bảng/biểu đồ/heatmap phục vụ nghiên cứu |
| `src/paths.py` | Tìm project root; hiện hard-code tên `plan` |
| `copy_artifacts.py` | Copy ảnh báo cáo, nhưng các source path hiện cần được sửa |

## 3. Model families

- **TinyCNN:** baseline nhỏ cho phase đầu, conv + pooling + global average pooling + linear.
- **SimpleCNN:** 4 conv blocks với batch norm, ReLU, pooling, classifier + dropout.
- **MobileNetV2:** weights pretrained torchvision, thay classifier cho 38 lớp; tùy chọn freeze backbone và dropout.
- **EfficientNet-B0:** pretrained backbone, thay classification head và tùy chọn freeze.
- **ImprovedMobileNetV2:** backbone MobileNetV2 và nhánh ảnh gốc `ResidualEnhancementBranch` chạy song song. Nhánh dùng convolution, residual blocks, tùy chọn SE hoặc lightweight CBAM; đầu ra của hai nhánh được nối rồi qua MLP classifier. Biến thể best trong báo cáo: `mobilenetv2_residual_se`.

Không đồng nhất kiến trúc trong `models.py` với việc có sẵn file checkpoint. Model factory gọi torchvision pretrained weights và cần giải quyết việc tải/caching khi chạy.

## 4. Training & evaluation contract

`train_model` nhận loaders, optimizer, criterion, số epochs, save path, patience. Mỗi epoch log train loss, val loss/accuracy/Macro F1; **checkpoint được lưu khi val Macro F1 tăng**, early-stop sau `patience` epoch không cải thiện. `evaluate_model` xuất Accuracy, macro precision/recall/F1, per-class precision/recall/F1, confusion matrix, predictions.

`TrainConfig` là dataclass default, **không có nghĩa tất cả notebooks dùng đúng các giá trị này**. Notebook có thể định nghĩa cấu hình riêng và chạy hai giai đoạn: frozen feature extraction → unfreeze fine-tune. So sánh phải đọc cấu hình từng experiment.

## 5. Risks ở tầng code

`PlantDataset._resolve_image_path` có fallback về variant `color`, có thể che giấu ảnh thiếu trong thí nghiệm grayscale/segmented. `src.paths.find_project_dir` ràng buộc tên thư mục. Chưa có CLI chuẩn hóa, manifest checkpoint, tests tự động hay inference entrypoint trong snapshot. Không nên quảng bá đây là ready-to-deploy classifier.
