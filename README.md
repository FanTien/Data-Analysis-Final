# Phân tích AQI Hà Nội 2022–2025

Đồ án cuối kỳ môn Phân tích dữ liệu. Notebook 1 đã phân tích dữ liệu theo giờ, làm sạch và trả lời bốn câu hỏi về biến động giữa các năm, mẫu hình tháng/mùa, chất ô nhiễm và thời tiết. Notebook 2 đã chuẩn bị dữ liệu, đánh giá baseline và Ridge Regression, đồng thời bổ sung chức năng dự đoán từ bảy AQI trung bình ngày. **Cả hai notebook đã hoàn thành và được xác nhận.**

## Cấu trúc project

```text
DA/
├── dataset/                         # 2022.csv–2025.csv, dữ liệu gốc, giữ nguyên
├── Slide/                           # tài liệu môn học
├── dataanalysis.ipynb               # notebook mẫu, giữ nguyên
├── notebooks/
│   ├── 01_AQI_Data_Analysis.ipynb   # Notebook 1: xử lý, EDA, bốn câu hỏi
│   └── 02_AQI_Prediction.ipynb      # Notebook 2: lag7, mô hình, đánh giá và dự đoán
├── output/
│   ├── data/aqi_hanoi_cleaned.csv   # dữ liệu đã làm sạch, đầu vào Notebook 2
│   ├── data/aqi_next_day_lag7.csv  # 1.234 mẫu ngày đích, bảy lag và AQI mục tiêu
│   ├── tables/duplicate_conflicts_audit.csv  # kiểm toán 5 giờ xung đột
│   ├── figures/                     # biểu đồ phân tích và đánh giá dự đoán
│   └── model/                       # Pipeline Ridge và metadata phương pháp được chọn
├── DATA_ANALYSIS_LOG.md             # quy trình, số liệu, quyết định và giới hạn
└── README.md
```

## Môi trường Python và thư viện

Môi trường đã dùng để chạy và kiểm tra cả hai notebook là `D:\Learning\DA\.venv\Scripts\python.exe`. Từ PowerShell tại gốc project:

```powershell
& .\.venv\Scripts\python.exe --version
& .\.venv\Scripts\python.exe -m pip install pandas numpy matplotlib seaborn jupyter notebook ipykernel nbformat nbclient scikit-learn joblib
& .\.venv\Scripts\python.exe -m jupyter notebook
```

Nếu tạo môi trường mới, chạy `py -m venv .venv` trước lệnh cài thư viện. Notebook 2 dùng `scikit-learn` cho Pipeline `StandardScaler`–`Ridge` và `joblib` để lưu mô hình.

Trong Jupyter, mở `notebooks/01_AQI_Data_Analysis.ipynb` và chọn **Kernel → Change Kernel** để dùng kernel của `.venv`. Nếu kernel này chưa xuất hiện, đăng ký bằng:

```powershell
& .\.venv\Scripts\python.exe -m ipykernel install --user --name da-aqi --display-name "Python (DA AQI)"
```

Sau đó chọn **Python (DA AQI)** và chạy **Run All** từ đầu. Chạy Notebook 1 trước để tạo `aqi_hanoi_cleaned.csv`, rồi Notebook 2 để tạo `aqi_next_day_lag7.csv`, đánh giá mô hình và dùng hàm dự đoán. Cả hai dùng đường dẫn tương đối và đã kiểm tra chạy được khi Jupyter bắt đầu ở gốc project hoặc trong thư mục `notebooks/`. Notebook 1 tạo lại dữ liệu sạch, bảng kiểm toán và biểu đồ từ CSV gốc; Notebook 2 đọc dữ liệu sạch và ghi file lag7, hình đánh giá, Pipeline Ridge cùng metadata. Không notebook nào ghi vào `dataset/` hoặc notebook mẫu.

## Đầu ra và giới hạn

`output/data/aqi_hanoi_cleaned.csv` có **30.336 dòng × 19 cột**, 30.336 giờ duy nhất và 3 ô AQI thiếu. `output/tables/duplicate_conflicts_audit.csv` lưu 13 ô bất đồng ở 5 giờ trùng, gồm giá trị hai bản ghi gốc và quyết định đặt NaN. `output/figures/` chứa 16 hình phân tích của Notebook 1 và 2 hình đánh giá dự đoán của Notebook 2. Xem [nhật ký phân tích](DATA_ANALYSIS_LOG.md) để truy lại các phép tính và giới hạn.

`output/data/aqi_next_day_lag7.csv` có **1.234 hàng × 9 cột**: `target_date`, `AQI_lag_1`…`AQI_lag_7`, `AQI_target`. Chỉ giữ mẫu có đủ bảy ngày lịch liên tiếp trước ngày đích và ngày đích đều có 24 giờ AQI hợp lệ. Chia theo ngày đích: train 2022–2023 (702 mẫu), validation 2024 (351), test 01–06/2025 (181).

`output/model/aqi_next_day_ridge.joblib` chứa Pipeline Ridge alpha 0,1 đã fit lại trên Train + Validation; `output/model/aqi_next_day_model_metadata.json` ghi phương pháp, thứ tự bảy đặc trưng và kết quả đánh giá. Ridge được chọn bằng Validation (MAE **23,3883**, RMSE **34,8658**) và đạt Test 01–06/2025 (MAE **22,3992**, RMSE **29,6577**), thấp hơn Persistence (24,7083/32,0491) và Weekly (37,3027/49,1494) trên Test này. Hai hình `pred_test_first45.png` và `pred_test_error_hist.png` nằm trong `output/figures/`. Test chỉ có sáu tháng đầu 2025; nhóm ngày AQI cao có sai số lớn hơn.

## Dùng chức năng dự đoán trong Notebook 2

Chạy Notebook 2 từ đầu bằng kernel `.venv`, sau đó gọi `predict_next_day_aqi(last_date, aqi_history)`. `last_date` là ngày của giá trị AQI cuối cùng, định dạng `YYYY-MM-DD`. `aqi_history` gồm **đúng bảy giá trị AQI trung bình ngày**, theo thứ tự từ ngày cũ nhất đến ngày mới nhất. Ví dụ với dữ liệu thực tế có sẵn trong notebook:

```python
result = predict_next_day_aqi(
    last_date="2025-06-30",
    aqi_history=daily.loc["2025-06-24":"2025-06-30", "AQI_daily"].tolist(),
)
print(result["prediction_date"], round(result["predicted_AQI_daily"], 2))
print(result["warnings"])
```

Ví dụ này cho dự đoán **89,57** cho **01/07/2025**. Dataset hiện tại không có AQI thực tế ngày đó để đối chiếu. Hàm báo lỗi với ngày sai định dạng, số lượng giá trị khác bảy, giá trị âm, thiếu, vô hạn hoặc không phải số; các giá trị ngoài phạm vi dữ liệu fit Train + Validation được cảnh báo. Người dùng cần bảo đảm bảy giá trị nhập là của **bảy ngày lịch liên tiếp** vì chỉ từ bảy số và ngày cuối, hàm không thể kiểm tra ngày gốc của từng số. Hàm dùng Pipeline đã lưu và không huấn luyện lại. Dự đoán là AQI trung bình ngày, không phải AQI theo giờ hoặc cảnh báo sức khỏe chính thức. Chưa có giao diện web.

Dữ liệu bắt đầu 13/01/2022, năm 2025 chỉ đến 30/06; thiếu toàn bộ 31/12/2023 trong khoảng quan sát. Các NaN và giá trị cực trị chưa xác minh vẫn được giữ. Đơn vị một số biến, nguồn trạm và công thức tính AQI chưa được xác minh, nên kết quả tương quan không được hiểu là quan hệ nhân quả.
