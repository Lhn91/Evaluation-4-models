# Nâng Cao Khả Năng Tổng Quát Hóa Của Mô Hình YOLO Cho Bài Toán Tìm Kiếm Cứu Nạn (SAR) Bằng UAV

## 1. Mục Tiêu Dự Án

Dự án tập trung giải quyết thách thức trong việc phát hiện con người từ UAV cho các nhiệm vụ Tìm kiếm và Cứu nạn (SAR). Do dữ liệu tổng hợp cắt ghép (như bộ C2A) tạo ra khoảng cách về mặt biểu diễn (domain gap), mô hình thường chỉ học các "vết nhiễu" thay vì đặc trưng người thật. Giải pháp cốt lõi là huấn luyện mô hình trên tập dữ liệu hỗn hợp (mixed-dataset) giữa ảnh tổng hợp và ảnh UAV thực tế, đồng thời kiểm chứng chéo trên các tập dữ liệu cứu hộ hoàn toàn mới (unseen).

---

## 2. Ma Trận Mô Hình (Model Matrix)

Dự án so sánh 4 phiên bản mô hình YOLO với cùng một kiến trúc và cấu hình để đảm bảo tính công bằng:

- **M1 (Baseline):** Huấn luyện trên tập C2A gốc.

- **M2 (Robustness):** Huấn luyện trên C2A + Weather Augmentation (thêm nhiễu thời tiết).

- **M3 (Domain Gap Fix):** Trộn dữ liệu tổng hợp và thực tế (C2A + CrowdHuman + TinyPerson + VisDrone) - chưa có Weather Augmentation 

- **M4 (Tối ưu):** Trộn dữ liệu hỗn hợp + Weather Augmentation.

---

## 3. Kế Hoạch Kiểm Chứng (Evaluation Protocol)

Mô hình được đánh giá qua 3 nhóm tập dữ liệu để kiểm tra khả năng tổng quát hóa thực sự:

- **T1 (In-domain):** C2A-Test.

- **T2 (Seen Domains):**  
  VisDrone-Test-Weather
  (kiểm tra hiệu suất trên ảnh thực tế đã dùng huấn luyện).

- **T3 (Unseen SAR Domains):**  
  SARD, Heridal (no weather) 
  (đánh giá khả năng chuyển giao sang môi trường cứu hộ chưa từng thấy).

---

## 4. Các Chỉ Số Đánh Giá Chính (Metrics)

Báo cáo không chỉ sử dụng AP50 làm thước đo duy nhất. Đối với bài toán SAR, an toàn sinh mạng là trên hết, do đó tập trung vào các chỉ số:

- **Recall (Độ nhạy):** Quan trọng nhất để đảm bảo không bỏ sót nạn nhân.

- **FPPI (False Positives Per Image):** Giảm thiểu báo động giả để tránh làm kiệt sức đội vận hành.

- **AP75 & mAP@[0.5:0.95]:** Đo lường chất lượng định vị hộp (localization).

- **AP_small / AP_tiny:** Đánh giá khả năng nhận diện các đối tượng có kích thước rất nhỏ từ UAV.

---


| Model             |   T1_C2A_Original_F1 |   T1_C2A_Original_Precision |   T1_C2A_Original_Recall |   T1_C2A_Original_mAP50 |   T1_C2A_Original_mAP50-95 |   T1_C2A_Weather_F1 |   T1_C2A_Weather_Precision |   T1_C2A_Weather_Recall |   T1_C2A_Weather_mAP50 |   T1_C2A_Weather_mAP50-95 |   T2_VisDrone_F1 |   T2_VisDrone_Precision |   T2_VisDrone_Recall |   T2_VisDrone_mAP50 |   T2_VisDrone_mAP50-95 |   T3_Heridal_F1 |   T3_Heridal_Precision |   T3_Heridal_Recall |   T3_Heridal_mAP50 |   T3_Heridal_mAP50-95 |   T3_SARD_F1 |   T3_SARD_Precision |   T3_SARD_Recall |   T3_SARD_mAP50 |   T3_SARD_mAP50-95 |
|:------------------|---------------------:|----------------------------:|-------------------------:|------------------------:|---------------------------:|--------------------:|---------------------------:|------------------------:|-----------------------:|--------------------------:|-----------------:|------------------------:|---------------------:|--------------------:|-----------------------:|----------------:|-----------------------:|--------------------:|-------------------:|----------------------:|-------------:|--------------------:|-----------------:|----------------:|-------------------:|
| M1_K_Original_C2A | 0.6659 | 0.7796 | 0.5811 | 0.7058 | 0.4414 | 0.5248 | 0.6841 | 0.4257 | 0.5763 | 0.3286 | 0.0726 | 0.2608 | 0.0422 | 0.1485 | 0.0648 | 0.2924 | 0.3091 | 0.2774 | 0.2579 | 0.1152 | 0.5202 | 0.6649 | 0.4273 | 0.5754 | 0.2633 |
| M2_R_Weather_C2A  | 0.6368 | 0.7808 | 0.5377 | 0.6796 | 0.4140 | 0.6228 | 0.7597 | 0.5277 | 0.6677 | 0.3987 | 0.0986 | 0.2910 | 0.0594 | 0.1697 | 0.0709 | 0.2776 | 0.3420 | 0.2336 | 0.2481 | 0.1021 | 0.2932 | 0.3822 | 0.2378 | 0.3243 | 0.1559 |
| M3_Mix_Base       | 0.6511 | 0.7852 | 0.5562 | 0.6906 | 0.4256 | 0.5609 | 0.7529 | 0.4470 | 0.6181 | 0.3674 | 0.2215 | 0.6816 | 0.1322 | 0.4112 | 0.1869 | 0.3747 | 0.4113 | 0.3441 | 0.3305 | 0.1455 | 0.5810 | 0.6983 | 0.4974 | 0.6327 | 0.3124 |
| M4_Mix_Weather    | 0.6235 | 0.7767 | 0.5208 | 0.6687 | 0.4014 | 0.6089 | 0.7514 | 0.5119 | 0.6540 | 0.3838 | 0.2342 | 0.6122 | 0.1448 | 0.3840 | 0.1672 | 0.3013 | 0.4068 | 0.2392 | 0.3001 | 0.1295 | 0.3357 | 0.4963 | 0.2536 | 0.4042 | 0.2117 |
