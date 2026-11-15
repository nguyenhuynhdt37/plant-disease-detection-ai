# Tài liệu kỹ thuật — Plant Disease Detection AI

Bộ tài liệu này mô tả **code và artifacts đang tồn tại**, không mô tả một production service chưa được xây dựng.

| Tài liệu | Nội dung |
|---|---|
| [Getting started](getting-started.md) | Môi trường, dữ liệu, cách mở notebook, lỗi đường dẫn |
| [Architecture](architecture.md) | Module, data flow, training/evaluation, model variants |
| [Data & methodology](data-and-methodology.md) | PlantVillage, CSV, split, sampling, augmentation, metrics |
| [Notebooks](notebooks.md) | Mục tiêu 11 notebook, thứ tự và tác động khi chạy |
| [Experiments & results](experiments-and-results.md) | Kết quả có nguồn, các so sánh và giới hạn diễn giải |
| [Limitations & roadmap](limitations-and-roadmap.md) | Những vấn đề thực tế và kế hoạch khắc phục |

## Nguồn sự thật

- **Cấu hình thực thi:** `src/*.py` và code cells trong `notebooks/*.ipynb`.
- **Thống kê dữ liệu:** `reports/02_data_audit/final_eda_insights.txt`.
- **So sánh mô hình:** `results/10_final_research_synthesis/phase_10_final_research_synthesis/final_cross_phase_comparison.csv`.
- **Ablation:** `results/09_improved_mobilenetv2_architecture_study/phase_09_improved_mobilenetv2/tables/improved_mobilenetv2_ablation_results.csv`.
- **Báo cáo đầy đủ:** `results/10_final_research_synthesis/phase_10_final_research_synthesis/final_project_report.md`.

Giữ nhất quán các mức độ chứng cứ: **đã có code**, **đã có artifact báo cáo**, **được suy luận**, và **chưa xác minh bằng chạy lại**. Tài liệu được viết dựa trên kiểm tra tĩnh GitHub; không khẳng định đã tái thực hiện training.
