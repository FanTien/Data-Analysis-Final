# Phân tích AQI Hà Nội 2022–2025

Đồ án cuối kỳ môn Phân tích dữ liệu. Project phân tích bốn file AQI Hà Nội theo giờ và dự kiến xây dựng hệ thống dự đoán AQI trung bình ngày tiếp theo.

## Trạng thái

Đã hoàn thành Data Understanding, Data Cleaning & Preprocessing và EDA tổng quan trong `notebooks/01_AQI_Data_Analysis.ipynb`. Bốn câu hỏi nghiên cứu và notebook dự đoán chưa triển khai.

## Cấu trúc

- `dataset/`: bốn CSV gốc, giữ nguyên.
- `Slide/`: tài liệu môn học.
- `dataanalysis.ipynb`: notebook mẫu, giữ nguyên.
- `notebooks/01_AQI_Data_Analysis.ipynb`: notebook đồ án hiện tại.
- `output/data/aqi_hanoi_cleaned.csv`: dữ liệu đã xử lý.
- `output/tables/duplicate_conflicts_audit.csv`: kiểm toán năm mốc giờ xung đột.
- `output/figures/`: biểu đồ EDA.
- `DATA_ANALYSIS_LOG.md`: quy trình, kết quả và các quyết định xử lý.

## Chạy notebook

Tạo môi trường Python và cài `pandas`, `numpy`, `matplotlib`, `seaborn`, `jupyter`, `ipykernel`; phần dự đoán sau này cần thêm `scikit-learn`. Mở notebook từ thư mục gốc project hoặc thư mục `notebooks/`, chọn kernel của môi trường đó và chạy các cell theo thứ tự.

Dữ liệu 2025 chỉ đến 30/06; đơn vị của một số chỉ số và nguồn trạm chưa được xác minh. Xem nhật ký để biết các giới hạn và quy tắc làm sạch.
