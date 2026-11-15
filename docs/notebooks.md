# Hướng dẫn 11 notebooks

Các file sau nằm trong `notebooks/`. Thứ tự theo số là tiến trình nghiên cứu, **không phải 11 bước bắt buộc của một pipeline production**.

| # | Notebook | Trọng tâm |
|---:|---|---|
| 01 | `01_problem_definition.ipynb` | Bài toán 38 lớp, mục tiêu và metrics |
| 02 | `02_data_audit.ipynb` | Kiểm toán biến thể dữ liệu, imbalance, dHash |
| 03 | `03_dataset_baseline_evaluation.ipynb` | Baseline đánh giá bộ dữ liệu và CNN |
| 04 | `04_preprocessing_optimization.ipynb` | Augmentation và so sánh preprocessing |
| 05 | `05_imbalance_analysis.ipynb` | Imbalance, sampler, recall class hiếm |
| 06 | `06_loss_function_study.ipynb` | So sánh loss functions |
| 07 | `07_simplecnn_hybrid_strategy_study.ipynb` | Hybrid strategy cho SimpleCNN |
| 08 | `08_modern_pretrained_model_study.ipynb` | SimpleCNN vs MobileNetV2 vs EfficientNet-B0 |
| 09 | `09_improved_mobilenetv2_architecture_study.ipynb` | Ablation residual/attention của MobileNetV2 |
| 10 | `10_final_research_synthesis.ipynb` | Đọc artifact các phase và tổng hợp kết quả |
| 11 | `11_model_architecture_inspection.ipynb` | Bóc tách components và parameter counts |

**Lưu ý về lịch sử:** notebook 01 hiện liệt kê 10 phase đầu và README cũ nói 9 notebooks; tree thực tế chứa **11 notebook**. Một số thư mục result có tiền tố `07_pretrained_transition_study` ngoài phase 07 hiện hành; đó là artifact lịch sử, không nên xóa hoặc gộp thiếu kiểm chứng.

## Cách làm việc an toàn

1. Đọc cell cấu hình (`SEED`, `IMAGE_SIZE`, `BATCH_SIZE`, `FORCE_TRAIN`, output dirs) trước khi chạy.
2. Kiểm tra ảnh tồn tại tại `data/raw/` và metadata đang dùng.
3. Không chạy toàn bộ Phase 08/09 chỉ để xem kết quả: dùng các CSV/ảnh trong `results/`.
4. Chỉ chạy Phase 10 khi muốn tái tổng hợp artifacts, và sao lưu outputs trước.
5. Khi chạy lại, ghi version Python/torch/torchvision, hardware, seed và elapsed time để so sánh có căn cứ.

Notebook 08 được kiểm tra trực tiếp có `IMAGE_SIZE=128`, `MAX_TRAINING_EPOCHS=15`, `FEATURE_EXTRACTION_EPOCHS=5`, `FINE_TUNING_EPOCHS=10`, `BATCH_SIZE=64`, `FORCE_TRAIN=True`. Những cài đặt này **chỉ nên áp dụng cho notebook 08**, không mặc định cho mọi notebook.
