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
