# 🌿 Plant Disease Detection AI

**Nghiên cứu phân loại 38 lớp bệnh/tình trạng lá cây với PyTorch và PlantVillage.** Repository tập trung vào kiểm toán dữ liệu, xử lý mất cân bằng lớp, so sánh CNN/transfer learning và cải tiến MobileNetV2 bằng nhánh residual + attention.

> **Trạng thái:** Dự án nghiên cứu dựa trên Jupyter notebooks; **chưa phải ứng dụng chẩn đoán được triển khai**. Các số liệu bên dưới là kết quả trong artifacts nghiên cứu, **không phải hiệu năng đã kiểm chứng trên ảnh thực địa**.

## Kết quả nổi bật

| Mô hình (theo tổng hợp Phase 10) | Accuracy | Macro F1 | Minority Recall | Worst-class Recall | Parameters |
|---|---:|---:|---:|---:|---:|
| Improved MobileNetV2 (Residual + SE) | 99.153% | **98.881%** | 99.412% | 94.118% | 2,761,218 |
| MobileNetV2 pretrained | 98.656% | 98.292% | 99.094% | 93.617% | 2,272,550 |
| EfficientNet-B0 pretrained | 98.416% | 97.956% | 97.914% | 89.796% | 4,056,226 |
| SimpleCNN | 94.237% | 92.944% | 97.495% | 71.000% | 63,974 |

Nguồn số liệu: [final_cross_phase_comparison.csv](results/10_final_research_synthesis/phase_10_final_research_synthesis/final_cross_phase_comparison.csv). Không suy ra kết quả thống kê đa seed hoặc độ chính xác ngoài thực tế từ bảng này.

## Bắt đầu

Yêu cầu: **Python 3.10+** (khuyến nghị; chưa có ma trận kiểm thử version), pip và Jupyter; GPU CUDA hoặc Apple Silicon MPS là tùy chọn.

```bash
git clone https://github.com/nguyenhuynhdt37/plant-disease-detection-ai.git
cd plant-disease-detection-ai
python3 -m venv .venv
source .venv/bin/activate                 # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook
```

**Chuẩn bị dữ liệu:** tải PlantVillage từ nguồn có quyền sử dụng phù hợp, giải nén ảnh theo thư mục lớp trong `data/raw/color/<class_name>/*.jpg`. Nếu nghiên cứu các variant, thêm `data/raw/grayscale/` và `data/raw/segmented/`. Ảnh thô và checkpoint **không được commit**; repo có sẵn metadata `metadata/{train,val,test,class_map}.csv`, nhưng CSV không thay thế được ảnh gốc. Đường dẫn `relative_path` trong CSV phải khớp file ảnh.

**Lưu ý về đường dẫn trước khi chạy:** `src/paths.py::find_project_dir()` hiện tìm thư mục có tên **`plan`**, hoặc thư mục con `plan/`; tên clone mặc định không đáp ứng điều kiện này. Cách chạy tạm thời không sửa code: clone với tên đích `plan` (`git clone <url> plan`) hoặc đặt repo vào thư mục `plan`. Xem [Getting started](docs/getting-started.md). Đây là hạn chế đã biết, không phải bước cài đặt lý tưởng.

Mở `notebooks/01_problem_definition.ipynb`, sau đó lần lượt nghiên cứu các phase theo [Notebook guide](docs/notebooks.md). Một số notebook **huấn luyện lại và ghi đè artifacts**; hãy sao lưu trước khi chạy, đặc biệt khi `FORCE_TRAIN=True`.

## Cấu trúc

```text
├── notebooks/       # 11 notebook nghiên cứu, từ định nghĩa bài toán đến inspection
├── src/             # data, audit, models, train, transforms, plots, paths
├── metadata/        # split CSV và ánh xạ 38 class
├── results/         # metric CSV, figures, báo cáo và kết quả các phase
├── reports/         # báo cáo audit và một số outputs
├── data/raw/        # ảnh PlantVillage tại máy (không được commit)
├── requirements.txt
└── docs/            # hướng dẫn, kiến trúc, thực nghiệm, hạn chế
```

## Các phần của nghiên cứu

1. Xác định bài toán và audit PlantVillage.
2. Baseline trên các phiên bản ảnh; tối ưu augmentation.
3. Phân tích imbalance, sampling, loss và hybrid strategy.
4. So sánh SimpleCNN, MobileNetV2, EfficientNet-B0.
5. Thử nhánh residual/SE cho MobileNetV2; Grad-CAM và tổng hợp kết quả.
6. Inspect kiến trúc và complexity.

Đọc thêm: [Tài liệu dự án](docs/README.md) · [Kiến trúc & code](docs/architecture.md) · [Dữ liệu & phương pháp](docs/data-and-methodology.md) · [Kết quả nghiên cứu](docs/experiments-and-results.md) · [Hạn chế & roadmap](docs/limitations-and-roadmap.md).

## Thận trọng khi diễn giải

PlantVillage có ảnh được thu thập trong điều kiện tương đối kiểm soát; kết quả không xác nhận khả năng chẩn đoán bệnh ngoài thực địa. Báo cáo có cảnh báo về single-run, thiếu cross-dataset validation, và các phase dùng subset khác nhau. Phân loại ảnh không tương đương khuyến nghị điều trị bệnh.

**Tác giả:** Nguyễn Xuân Huỳnh — [nguyenhuynhdt37](https://github.com/nguyenhuynhdt37). Chưa thấy file LICENSE ở snapshot được kiểm tra; không tự suy đoán quyền tái sử dụng code hoặc dữ liệu.
