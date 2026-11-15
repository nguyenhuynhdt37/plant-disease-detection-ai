# Hạn chế, vấn đề kỹ thuật và roadmap

## Verified by static repository inspection

| Priority | Vấn đề | Bằng chứng | Hướng xử lý |
|---|---|---|---|
| P0 | Project root chỉ nhận thư mục tên `plan` | `src/paths.py::find_project_dir` | Tìm root bằng markers, viết tests cho nhiều cwd và tên clone |
| P0 | Thiếu ảnh gốc và checkpoint trên GitHub | `.gitignore` bỏ qua `data/`, `*.pth` | Viết download/setup guide, artifact versioning + checksum, không commit dữ liệu trái phép |
| P0 | Fallback im lặng sang ảnh color | `src/data.py::PlantDataset._resolve_image_path` | Strict mode, validate missing paths và variant correspondence |
| P1 | Chưa xác minh leakage giữa split | Audit dHash nêu duplicate nội lớp | Hash/cross-split near-duplicate audit và group-based split nếu cần |
| P1 | Dependency không pin | `requirements.txt` | constraints/lockfile và CI smoke tests |
| P1 | Script copy artifacts có thể lỗi | `copy_artifacts.py` trỏ `results/class_distribution_chart.png` và các path không có trong snapshot | Dùng đường dẫn artifact thực tế; kiểm tra tồn tại và báo lỗi rõ |
| P1 | Notebook 08 cưỡng bức huấn luyện lại | `FORCE_TRAIN=True` | Default replay/read-only hoặc bật train bằng tham số rõ ràng |
| P2 | Notebook 01 / README cũ lỗi thời | 01 liệt kê 10, README cũ nói 9; tree có 11 | Đồng bộ bảng mục lục notebook |
| P2 | Không có model inference service/app | Không có API/frontend entrypoint ở tree | Chỉ bổ sung sau khi xác định yêu cầu ứng dụng |

## Scientific limitations

- PlantVillage có môi trường chụp tương đối kiểm soát; chưa có bằng chứng khả năng generalize ngoài thực địa.
- Một số thí nghiệm chỉ single-run, một số phase dùng subset; không nên cộng gộp thành kết luận hoàn toàn tương đương.
- Thiếu independent external validation, multi-seed confidence intervals, và đánh giá calibration / OOD / uncertainty.
- Grad-CAM hữu ích để quan sát vùng chú ý nhưng không chứng minh quan hệ nhân quả.
- Không đủ căn cứ kết luận chẩn đoán, phác đồ điều trị hay độ an toàn nông nghiệp.

## Roadmap có thứ tự

**Mốc A — Reliability:** sửa path resolution; chuẩn hóa dataset manifest và kiểm tra missing files; tests cho transforms/loader/model forward/checkpoint; lock versions; smoke test notebook.

**Mốc B — Reproducibility:** duy trì immutable splits, kiểm tra leakage/dedup, seed matrix, experiment config YAML, lưu metric JSON/CSV và checkpoint registry, quy trình phục hồi best checkpoint.

**Mốc C — Scientific validation:** external dataset/field photos, per-crop/per-class evaluation, confidence intervals qua multi-seed, calibration/OOD, analysis false positives/negatives.

**Mốc D — Deployment (tùy mục tiêu):** inference CLI trước, sau đó API/UI; packaging weights + label mapping + exact transforms, benchmark thiết bị thực và kiểm tra security file upload.

## Những việc tài liệu này KHÔNG khẳng định

Không nói đã test toàn bộ notebook, chưa verify downloadable dataset URL/license, chưa deploy ứng dụng, chưa xác nhận checkpoint có thể tải, và chưa chứng minh mô hình đạt chất lượng thực địa.
