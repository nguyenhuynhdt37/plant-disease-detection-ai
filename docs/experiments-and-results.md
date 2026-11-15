# Tổng hợp thực nghiệm và kết quả

## Kết quả cuối

Nguồn chính: `results/10_final_research_synthesis/phase_10_final_research_synthesis/final_cross_phase_comparison.csv`.

| Experiment | Accuracy | Macro F1 | Minority Recall | Worst Recall |
|---|---:|---:|---:|---:|
| `mobilenetv2_residual_se` | 0.991530 | **0.988808** | 0.994118 | 0.941176 |
| `mobilenetv2_winner` | 0.986559 | 0.982922 | 0.990943 | 0.936170 |
| `efficientnet_b0_winner` | 0.984165 | 0.979556 | 0.979141 | 0.897959 |
| `simple_cnn_winner` | 0.942368 | 0.929444 | 0.974950 | 0.710000 |

Trên snapshot artifacts, improved MobileNetV2 dẫn đầu **Macro F1**, nhưng không được diễn dịch thành độ chính xác trên mọi loài cây/ảnh ngoài thực địa.

## Phase 09: residual / SE ablation

Nguồn: `results/09_improved_mobilenetv2_architecture_study/phase_09_improved_mobilenetv2/tables/improved_mobilenetv2_ablation_results.csv`.

| Variant | Params | Macro F1 | Minority Recall | Worst Recall |
|---|---:|---:|---:|---:|
| `mobilenetv2_baseline` | 2,272,550 | 0.982922 | 0.990943 | 0.936170 |
| `mobilenetv2_residual` | 2,758,806 | 0.988389 | **0.994865** | 0.940000 |
| `mobilenetv2_residual_se` | 2,761,218 | **0.988808** | 0.994118 | **0.941176** |

Residual+SE hơn baseline ~0.005886 **điểm F1 (absolute)**, với khoảng 488,668 tham số nhiều hơn. Lưu ý: residual-only nhỉnh hơn **Minority Recall**, nên không tuyên bố residual+SE thắng trên mọi metric.

## Efficiency & statistical cautions

Báo cáo Phase 10 ghi inference latency ~6.38 ms/image cho MobileNetV2 baseline và ~7.99 ms/image cho residual+SE trong bảng efficiency của Phase 09; **đây là số đo theo môi trường nghiên cứu**, chưa được xác nhận trên thiết bị mobile/edge cụ thể. Bảng so sánh cuối có cột latency để trống, vì thế không điền số giả vào đó.

Báo cáo có Wilcoxon paired analysis trên **38 giá trị per-class** trong single run, có cảnh báo đây **không thay thế thí nghiệm đa seed**. Không diễn giải p-value từ các class như chứng cứ tổng quát hóa trên nhiều lần huấn luyện độc lập.

## Tính tái lập

Các số liệu là artifacts commit sẵn, **chưa chạy lại trong lượt viết docs này**. Không có model weights `*.pth` trong repo do `.gitignore`; không thể tái thực hiện inference chỉ với CSV. Để làm benchmark mạnh hơn cần: pin dependencies, fixed dataset manifest + hash, leakage audit, multi-seed mean/std và kiểm thử ảnh thực địa.

Xem báo cáo gốc: [Final research report](../results/10_final_research_synthesis/phase_10_final_research_synthesis/final_project_report.md).
