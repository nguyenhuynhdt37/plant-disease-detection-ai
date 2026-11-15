# Cài đặt và chạy nghiên cứu

## 1. Yêu cầu

Python 3.10+ là lựa chọn khuyến nghị để bắt đầu; `requirements.txt` hiện **không pin phiên bản** và chưa cung cấp lockfile. Thư viện: Jupyter, IPython, NumPy, pandas, Pillow, scikit-learn, SciPy, Matplotlib, seaborn, torch, torchvision. PyTorch có thể yêu cầu hướng dẫn cài riêng theo OS/CUDA.

```bash
git clone https://github.com/nguyenhuynhdt37/plant-disease-detection-ai.git plan
cd plan
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
jupyter notebook
```

Windows PowerShell: `.venv\Scripts\Activate.ps1`. Chọn kernel của môi trường cài dependency nếu Jupyter có nhiều kernel.

**Vì sao clone thành `plan`?** `src/paths.py::find_project_dir()` có điều kiện tên thư mục `plan`; notebook 08/10 bootstrap qua hàm này. Clone bằng tên repo mặc định có thể báo `FileNotFoundError` dù đã có `src/`. Cần sửa helper để nhận diện root bằng marker như `src/` + `metadata/`, không ràng buộc tên thư mục (roadmap).

## 2. Dataset và metadata

Cấu trúc mong đợi:

```text
plan/
  data/raw/
    color/
      Apple___Apple_scab/
        <filename>.jpg
      ...
    grayscale/       # tuỳ chọn
    segmented/       # tuỳ chọn
  metadata/
    train.csv
    val.csv
    test.csv
    class_map.csv
```

Repo commit sẵn 4 CSV metadata, nhưng không commit ảnh. `PlantDataset` nối `data/raw/<variant>/` và cột `relative_path` trong CSV. Với `segmented`, code có thử tên `*_final_masked` rồi fallback sang `color` khi thiếu file; điều này có thể gây lẫn variant **mà không báo lỗi**, cần audit trước so sánh.

Nếu tạo split mới, `src.data.create_metadata_splits(raw_dir, output_dir, dataset_name="color", test_size=0.20, val_size_from_temp=0.50, seed=42)` dùng stratification và tỷ lệ 80/10/10. **Không ghi đè metadata hiện hữu nếu muốn tái lập các results đã commit.** Kiểm tra nhất quán tên class và file trước khi chạy.

## 3. Khởi chạy notebook

```bash
jupyter notebook notebooks/
```

Bắt đầu ở `01_problem_definition.ipynb` và `02_data_audit.ipynb`. Tiếp tục theo [notebooks.md](notebooks.md). Phase 08/09 có thể cần tải pretrained weights của torchvision trong lần chạy đầu; cần internet hoặc cache có sẵn. Notebook 08 đặt `FORCE_TRAIN=True` trong snapshot được kiểm tra: chạy lại sẽ train và ghi outputs mới. Kiểm tra các cell ghi file, kích thước subset, epochs và thiết bị trước khi chọn Run All.

## 4. Device & transforms

`src.utils.get_device()` ưu tiên CUDA → MPS → CPU. `get_num_workers(..., notebook_safe=True)` trả về 0 để tránh lỗi multiprocessing notebook. Preprocessing dùng resize, ToTensor và ImageNet normalization; chỉ áp dụng random augmentation cho train; validation/test dùng transform cố định.

## 5. Troubleshooting

- **Không tìm thấy project `plan`:** dùng clone target `plan` như trên hoặc cập nhật helper theo roadmap.
- **FileNotFoundError ảnh:** xác minh đường dẫn `data/raw/color/<relative_path>`, không chỉ CSV.
- **Không tải pretrained weights:** cấp network khi chạy đầu hoặc chuẩn bị cache torchvision.
- **Thiếu checkpoint:** `.gitignore` loại trừ `*.pth`, `*.pt` nên kết quả CSV không có nghĩa model weights đã có để inference.
- **Kết quả khác report:** kiểm tra seed, version dependencies, split và `FORCE_TRAIN`. Số liệu chưa được xác minh qua multi-seed/CI.
- **Script `copy_artifacts.py` lỗi:** đang tham chiếu một số ảnh trực tiếp dưới `results/` không có trong snapshot tree; xem [limitations](limitations-and-roadmap.md).
