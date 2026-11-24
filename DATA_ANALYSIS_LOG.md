# Nhật ký phân tích dữ liệu AQI Hà Nội 2022–2025

> Tài liệu đồng hành với hai notebook của đồ án. Chỉ ghi kết quả đã kiểm chứng. Kết quả khảo sát CSV trước khi viết notebook được phân biệt rõ với output của notebook.

## 1. Tổng quan đồ án

**Chủ đề:** Phân tích chất lượng không khí (AQI) tại Hà Nội giai đoạn 2022–2025 và xây dựng hệ thống dự đoán AQI trung bình ngày tiếp theo.

**Mục tiêu:** Kiểm tra và tiền xử lý bốn CSV gốc; trả lời bốn câu hỏi nghiên cứu bằng thống kê và biểu đồ; xây dựng, đánh giá baseline và Ridge Regression cho bài toán dự đoán ngày tiếp theo. Có hai thành viên; tên thành viên chưa được cung cấp.

**Bốn câu hỏi đã được xác nhận:**

1. Chỉ số AQI tại Hà Nội thay đổi như thế nào giữa các năm trong nửa đầu và nửa cuối năm?
2. AQI tại Hà Nội có xuất hiện mẫu hình theo tháng và mùa nhất quán qua các năm hay không?
3. Các chỉ số PM2.5, PM10, CO, NO2, O3 và SO2 có mối liên hệ như thế nào với AQI?
4. Nhiệt độ, độ ẩm, lượng mưa và tốc độ gió có mối liên hệ như thế nào với AQI?

**Phạm vi hiện tại:** Cả hai notebook và Giai đoạn 1–11 đã hoàn thành, được người dùng xác nhận. Notebook 2 dùng Pipeline Ridge alpha 0,1 đã lưu để dự đoán AQI trung bình ngày tiếp theo từ bảy AQI trung bình ngày liên tiếp. Không có giao diện web hoặc mô hình khác.

## 2. Dataset

Bốn file gốc nằm trong `dataset/2022.csv` đến `dataset/2025.csv`. Notebook mẫu `dataanalysis.ipynb` ghi một đường dẫn nguồn Kaggle, nhưng nguồn trạm, đơn vị của các chỉ số và quy tắc tạo AQI chưa được xác minh từ chính các CSV. Không coi liên kết trong notebook mẫu là bằng chứng đầy đủ về phương pháp thu thập.

Mỗi file có 19 cột: `Local Time`, `UTC Time`, `City`, mã quốc gia, múi giờ, `AQI`, `CO`, `NO2`, `O3`, `PM10`, `PM25`, `SO2`, `Clouds`, `Precipitation`, `Pressure`, `Relative Humidity`, `Temperature`, `UV Index`, `Wind Speed`. `Local Time` là giờ địa phương; `UTC Time` là giờ UTC. `City`, mã quốc gia và múi giờ dùng để kiểm tra phạm vi. AQI là chỉ số chất lượng không khí; sáu cột CO–SO2 là chỉ số chất ô nhiễm; bảy cột còn lại là chỉ số thời tiết. Đơn vị đo của các chỉ số chưa được xác minh.

| File | Dòng × cột | Khoảng giờ địa phương | Ghi chú độ bao phủ |
|---|---:|---|---|
| `2022.csv` | 8.472 × 19 | 13/01/2022 00:00 – 31/12/2022 23:00 | Thiếu 01–12/01 |
| `2023.csv` | 8.736 × 19 | 01/01/2023 00:00 – 30/12/2023 23:00 | Thiếu toàn bộ 31/12 |
| `2024.csv` | 8.788 × 19 | 01/01/2024 00:00 – 31/12/2024 23:00 | Có 4 giờ bị ghi hai dòng |
| `2025.csv` | 4.345 × 19 | 01/01/2025 00:00 – 30/06/2025 23:00 | Chỉ có sáu tháng đầu năm; 1 giờ bị ghi hai dòng |

Tổng cộng 30.341 dòng và 30.336 mốc giờ duy nhất. Đã đối chiếu với output Notebook 1 và kiểm tra đọc lại file đã làm sạch ở bước 8.1.

## 3. Cấu trúc project

- `dataset/`: bốn CSV gốc, chỉ đọc; không ghi đè.
- `Slide/`: tài liệu môn học.
- `dataanalysis.ipynb`: notebook mẫu, chỉ tham khảo; không sửa.
- `notebooks/01_AQI_Data_Analysis.ipynb`: đã có Giới thiệu, Data Understanding, Data Cleaning & Preprocessing, EDA tổng quan, Câu hỏi 1–4, bước bàn giao 8.1 và phần Markdown tổng kết ở cuối.
- `notebooks/02_AQI_Prediction.ipynb`: đã hoàn thành chuẩn bị dữ liệu lag7, đánh giá Persistence/Weekly/Ridge, lưu Pipeline Ridge và chức năng `predict_next_day_aqi()`.
- `output/data/aqi_hanoi_cleaned.csv`: dữ liệu đã xử lý, 30.336 dòng × 19 cột, đầu vào Notebook 2.
- `output/data/aqi_next_day_lag7.csv`: 1.234 mẫu × 9 cột cho huấn luyện và đánh giá dự đoán.
- `output/tables/duplicate_conflicts_audit.csv`: bảng kiểm toán 13 ô bất đồng tại 5 giờ trùng, có số dòng nguồn và cả hai giá trị gốc.
- `output/figures/`: 18 hình PNG gồm 16 hình phân tích Notebook 1 và 2 hình đánh giá dự đoán Notebook 2.
- `output/model/`: `aqi_next_day_ridge.joblib` và `aqi_next_day_model_metadata.json` của Ridge alpha 0,1 đã chọn.
- `README.md`: hướng dẫn chạy project, hai notebook và chức năng dự đoán trong Notebook 2; đã cập nhật đến Giai đoạn 11.
- `DATA_ANALYSIS_LOG.md`: nhật ký này; cập nhật cùng notebook sau mỗi bước đã chạy và kiểm chứng.

## 4. Môi trường thực hiện

Môi trường đã dùng: `.venv/Scripts/python.exe`, Python 3.14.7; pandas 3.1.1, NumPy 2.5.5, Matplotlib 3.11.2, Seaborn 0.13.2, Jupyter 1.1.1, ipykernel 7.4.0, scikit-learn 1.9.1, nbformat 5.11.1 và nbclient 0.11.0. Một cell Jupyter thử nghiệm đã in `5`. Notebook 1 hiện có 65 Code cell và Notebook 2 có 20 Code cell; cả hai đã chạy lại từ đầu thành công bằng môi trường này. Cảnh báo của `zmq` về event loop/TCP xuất hiện khi khởi động kernel nhưng không gây lỗi Code cell.

**Cách chạy đã kiểm chứng:** Mở terminal tại thư mục project, chọn kernel Python của `.venv` trong Jupyter, rồi chạy Notebook 1 trước Notebook 2. Đường dẫn dữ liệu trong cả hai notebook hoạt động khi thư mục làm việc là gốc project hoặc `notebooks/`. Notebook 2 đã hoàn thành chuẩn bị dữ liệu, đánh giá mô hình và chức năng dự đoán bằng cùng môi trường.

## 5. Data Understanding

### Khảo sát CSV trước triển khai notebook

**Mục tiêu:** Xác định cấu trúc, độ bao phủ và các vấn đề cần kiểm tra lại trong Notebook 1. **Lý do:** Không chọn cách làm sạch trước khi biết dữ liệu thực tế. **Đầu vào:** Bốn CSV gốc, toàn bộ dòng và cột. **Phương pháp:** Đọc CSV ở chế độ chỉ đọc; đếm dòng/cột và giá trị trống; đối chiếu tên cột, giờ địa phương với UTC+7; kiểm tra khoảng cách giữa các giờ, dòng trùng hoàn toàn, mốc giờ trùng và phạm vi số. Đây là khảo sát bằng công cụ đọc CSV, **không phải Code cell hay output của Notebook 1**. **Vị trí notebook:** Chưa có.

**Kết quả và ảnh hưởng đến bước tiếp theo:**

| Phát hiện | Bằng chứng từ CSV | Ảnh hưởng, trạng thái tại thời điểm khảo sát ban đầu |
|---|---|---|
| Tên cột khác nhau | 2022 dùng `Country Code`, `Timezone`; 2023–2025 dùng `Country code`, `timezone` | Cần chuẩn hóa trước khi ghép; **chưa xử lý** |
| Giá trị thiếu 2022 | `AQI`, `PM10`, `PM25` thiếu 2 giá trị/cột tại 01/07 06:00–07:00; `CO`, `NO2`, `O3`, `SO2` thiếu 74 giá trị/cột từ 30/06 05:00 đến 03/07 06:00 | Cần đánh giá ảnh hưởng theo phép tính; không tự động điền qua 74 giờ; **chưa xử lý** |
| Giờ trùng và xung đột | 2024: 01/03, 01/04, 01/11, 01/12 lúc 00:00; 2025: 01/05 lúc 00:00. Hai dòng 01/12/2024 có AQI 165 và 173 | Không có dòng trùng hoàn toàn; cần kiểm tra từng cặp và quy tắc minh bạch; **chưa xử lý** |
| Khoảng thời gian thiếu | 2022 thiếu 12 ngày đầu; 2023 thiếu 24 giờ ngày 31/12; 2025 kết thúc 30/06 | Không so sánh 2025 như một năm đầy đủ; **chưa xử lý** |
| Cực trị cần xem xét | 2024 có 59 dòng AQI = 500, 3 dòng CO = 0; CO tối đa 15956,2, O3 tối đa 700, PM10 tối đa 1103. 2025 có SO2 tối đa 721 | Không đủ thông tin đơn vị và nguồn trạm để tự kết luận là sai; **chưa xử lý** |
| PM25 nhỉnh hơn PM10 | 69 dòng trong 2023–2025, chênh lệch tối đa 0,04 | Có thể liên quan làm tròn; cần ghi nhận, không tự sửa; **chưa xử lý** |
| Hai cột thời gian | Toàn bộ giờ UTC khớp với giờ địa phương theo UTC+7 trong lần khảo sát | Cần kiểm tra lại sau chuyển kiểu thời gian trong notebook; **chưa xử lý** |

### Các bước đã chạy trong Notebook 1

Các mục dưới đây đối chiếu với Code cell và Markdown kết luận cùng mã bước trong `notebooks/01_AQI_Data_Analysis.ipynb`. Không có Code cell lỗi; notebook hợp lệ với 40 cell (27 Markdown, 13 Code), mỗi Code cell có Markdown ngay trước và ngay sau. Chạy lại từ đầu thành công cả 13 Code cell.

**Bước 1.1 — Import thư viện.** Mục tiêu/lý do: xác nhận công cụ trước khi đọc dữ liệu. Đầu vào: môi trường `.venv`. Phương pháp: `import` và đọc `__version__`. Output: Python 3.14.7; pandas 3.1.1, NumPy 2.5.5, Matplotlib 3.11.2, Seaborn 0.13.2. Giải thích/quyết định: đủ thư viện phân tích; tiếp tục kiểm tra đường dẫn. Vị trí: Notebook 1, bước 1.1.

**Bước 1.2 — Kiểm tra đường dẫn.** Mục tiêu/lý do: tránh đọc nhầm hoặc thiếu file. Đầu vào: thư mục `dataset/`. Phương pháp: `pathlib.Path`, kiểm tra tồn tại và kích thước. Output: cả bốn CSV tồn tại, kích thước lần lượt 1.112.039, 1.132.940, 1.138.974 và 564.112 byte. Quyết định: dùng đường dẫn tương đối, tiếp tục đọc file. Vị trí: Notebook 1, bước 1.2.

**Bước 1.3 — Đọc bốn CSV.** Mục tiêu/lý do: tạo bảng trong bộ nhớ mà không sửa file gốc. Đầu vào: bốn CSV. Phương pháp: `pd.read_csv()`. Output: bốn DataFrame lần lượt 8.472, 8.736, 8.788, 4.345 dòng. Quyết định: tiếp tục đo kích thước cột. Vị trí: Notebook 1, bước 1.3.

**Bước 1.4 — Kiểm tra kích thước.** Mục tiêu/lý do: nắm quy mô và phát hiện độ bao phủ khác nhau. Đầu vào: bốn DataFrame. Phương pháp: `shape`. Output: mỗi file 19 cột, tổng 30.341 dòng. Giải thích: số dòng khác nhau cần đối chiếu với thời gian. Quyết định: kiểm tra schema. Vị trí: Notebook 1, bước 1.4.

**Bước 1.5 — Kiểm tra tên cột.** Mục tiêu/lý do: tránh tách cột khi ghép. Đầu vào: danh sách cột từng DataFrame. Phương pháp: so sánh danh sách/tập tên cột với 2022. Output: 2023–2025 dùng `Country code`, `timezone`; 2022 dùng `Country Code`, `Timezone`. Quyết định: ghi vấn đề để chuẩn hóa ở Giai đoạn 2, chưa sửa. Vị trí: Notebook 1, bước 1.5.

**Bước 1.6 — Kiểm tra kiểu dữ liệu.** Mục tiêu/lý do: xác định cột cần chuyển kiểu. Đầu vào: bốn DataFrame. Phương pháp: `dtypes`. Output: hai cột thời gian là `str`; AQI 2022 là `float64`, AQI 2023–2025 là `int64`; các chất ô nhiễm là `float64`. Quyết định: chuyển thời gian và thống nhất kiểu ở Giai đoạn 2. Vị trí: Notebook 1, bước 1.6.

**Bước 1.7 — Xem đầu và cuối file.** Mục tiêu/lý do: kiểm tra ranh giới file bằng bản ghi thực. Đầu vào: thời gian, AQI, PM25. Phương pháp: `head(2)` và `tail(2)`. Output: 2022 bắt đầu 13/01, 2023 kết thúc 30/12, 2024 đến 31/12 và 2025 đến 30/06. Quyết định: kiểm tra toàn bộ thời gian ở bước 1.12. Vị trí: Notebook 1, bước 1.7.

**Bước 1.8 — Thống kê mô tả.** Mục tiêu/lý do: nhận diện phạm vi và cực trị trước khi xử lý. Đầu vào: 14 cột số của từng năm. Phương pháp: `describe()` với count, min, median, max. Output: AQI 2022 có 8.470 giá trị; trung vị AQI bốn năm là 107, 112, 105, 136. 2024 có AQI tối đa 500, CO 15.956,2, O3 700, PM10 1.103. Giải thích: các trung vị thô chưa trả lời câu hỏi so sánh năm do độ bao phủ khác nhau. Quyết định: kiểm tra thiếu và cực trị chi tiết. Vị trí: Notebook 1, bước 1.8.

**Bước 1.9 — Kiểm tra giá trị thiếu.** Mục tiêu/lý do: xác định mức thiếu và vị trí trước khi chọn cách xử lý. Đầu vào: bốn DataFrame. Phương pháp: `isna().sum()` và lọc `Local Time` của dòng thiếu. Output: 2022 có 302 ô thiếu; AQI/PM10/PM25 thiếu 2 ô mỗi cột, CO/NO2/O3/SO2 thiếu 74 ô mỗi cột. Các năm còn lại có 0 ô thiếu. Quyết định: cụm 74 giờ phải đánh giá riêng ở Giai đoạn 2, chưa điền. Vị trí: Notebook 1, bước 1.9.

**Bước 1.10 — Kiểm tra dòng trùng hoàn toàn.** Mục tiêu/lý do: phân biệt trùng cả dòng với trùng thời điểm. Đầu vào: bốn DataFrame. Phương pháp: `duplicated()`. Output: 0 dòng trùng hoàn toàn ở mỗi năm. Quyết định: tiếp tục kiểm tra khóa thời gian. Vị trí: Notebook 1, bước 1.10.

**Bước 1.11 — Kiểm tra giờ trùng và xung đột.** Mục tiêu/lý do: tìm nhiều giá trị tại một giờ. Đầu vào: `Local Time` và các chỉ số. Phương pháp: `duplicated(keep=False)`, `groupby()` theo giờ để so cột khác nhau. `groupby()` gom những dòng cùng một mốc giờ thành một nhóm để kiểm tra từng cặp. Output: 2024 có 4 giờ lặp, 2025 có 1; cả 5 cặp đều khác ít nhất một cột. Tại 01/12/2024 00:00, AQI là 165 và 173. Quyết định: chưa chọn dòng; Giai đoạn 2 phải có quy tắc minh bạch và đánh giá ảnh hưởng. Vị trí: Notebook 1, bước 1.11.

**Bước 1.12 — Kiểm tra phạm vi và tính liên tục.** Mục tiêu/lý do: kiểm tra giờ thiếu và tính nhất quán hai cột thời gian. Đầu vào: `Local Time`, `UTC Time`. Phương pháp: chuyển thời gian trên biến tạm bằng `pd.to_datetime()`, tạo `date_range(freq='h')`, đối chiếu UTC+7. Output: giờ duy nhất theo năm là 8.472, 8.736, 8.784, 4.344; không có giờ thiếu trong khoảng từng file; toàn chuỗi thiếu 24 giờ của 31/12/2023; 0 giờ không đọc được và 0 sai lệch UTC. Quyết định: ghi khoảng thiếu, không tạo dữ liệu giả. Vị trí: Notebook 1, bước 1.12.

**Bước 1.13 — Kiểm tra giá trị bất thường.** Mục tiêu/lý do: gắn cờ giá trị cần xác minh, không tự xóa. Đầu vào: AQI và sáu chất ô nhiễm. Phương pháp: điều kiện phạm vi, đếm bằng phép so sánh, tìm cực đại bằng `idxmax()`. Output: 0 AQI ngoài 0–500, 0 chỉ số chất ô nhiễm âm; 2024 có 59 dòng AQI = 500 và 3 dòng CO = 0; PM25 > PM10 ở 4, 50, 15 dòng của 2023–2025. Cực đại cần xem xét: CO 15.956,2, O3 700, PM10 1.103 ở 2024; SO2 721 ở 2025. Quyết định: chưa sửa vì thiếu đơn vị và nguồn trạm; Giai đoạn 2 đánh giá thêm. Vị trí: Notebook 1, bước 1.13.

## 6. Data Cleaning & Preprocessing

Đã thực hiện và chạy các bước sau trong `notebooks/01_AQI_Data_Analysis.ipynb`. Mỗi bước có Markdown giới thiệu, Code cell với output thực tế và Markdown kết luận ngay sau. Các thao tác hiện chỉ trên bản sao trong bộ nhớ; bốn CSV gốc không bị sửa.

**Bước 2.1 — Chuẩn hóa tên cột trên bản sao.** Mục tiêu/lý do: tránh tách cột khi ghép. Đầu vào: bốn DataFrame gốc. Phương pháp: `rename(columns=...)` cho `Country Code` → `Country code`, `Timezone` → `timezone`, sau đó `copy()` và so schema. Output: cả bốn bản sao cùng 19 cột, giữ nguyên số dòng 8.472, 8.736, 8.788, 4.345. Quyết định: dùng schema thống nhất trên bản sao; chưa ghép. Ảnh hưởng: các năm có thể căn cột chính xác về sau. Vị trí: Notebook 1, bước 2.1.

**Bước 2.2 — Chuyển kiểu số và thời gian.** Mục tiêu/lý do: thời gian đang là chuỗi và AQI khác kiểu. Đầu vào: các bản sao từ 2.1. Phương pháp: `pd.to_numeric(errors='coerce')`, `astype('float64')`, `pd.to_datetime()`; đối chiếu giá trị trước/sau bằng NumPy. Output: 0 giá trị số bị thay đổi, 0 NaN mới, 0 thời gian không đọc được ở bốn năm; AQI cùng kiểu `float64`. Quyết định: giữ bản sao đã chuyển kiểu để kiểm tra tiếp. Ảnh hưởng: chưa làm thay đổi kết quả phân tích. Vị trí: Notebook 1, bước 2.2.

**Bước 2.3 — Xác minh định danh và thời gian.** Mục tiêu/lý do: đảm bảo không lẫn thành phố hay lệch UTC. Đầu vào: `City`, `Country code`, `timezone`, `Local Time`, `UTC Time` của bản sao. Phương pháp: `value_counts()` và quy đổi giờ địa phương sang UTC. Output: toàn bộ 30.341 dòng có Hanoi, VN, Asia/Ho_Chi_Minh; 0 dòng lệch UTC. Quyết định: dùng giờ địa phương làm mốc phân tích, tiếp tục xử lý giờ trùng. Vị trí: Notebook 1, bước 2.3.

**Bước 2.4 — Đối chiếu từng cặp giờ xung đột.** Mục tiêu/lý do: xác định cột nào bất đồng mà không dùng `drop_duplicates()` toàn dòng. Đầu vào: các bản sao đã chuyển kiểu. Phương pháp: `duplicated(keep=False)` theo `Local Time`, `groupby()` để đối chiếu hai dòng trong từng giờ. Output cụ thể:

| Giờ địa phương | Dòng CSV (tính cả header) | Cột bất đồng và hai giá trị |
|---|---|---|
| 01/03/2024 00:00 | 1442, 1443 | O3: 27,8 / 33,7; SO2: 38,4 / 35,8 |
| 01/04/2024 00:00 | 2187, 2188 | SO2: 30,3 / 20,0 |
| 01/11/2024 00:00 | 7324, 7325 | CO: 1464,7 / 454,8; NO2: 20,7 / 29,3; O3: 18,6 / 27,3; SO2: 81,0 / 119,7 |
| 01/12/2024 00:00 | 8045, 8046 | AQI: 165 / 173; PM10: 71,3 / 81,0; PM25: 66,67 / 72,33 |
| 01/05/2025 00:00 | 2882, 2883 | CO: 393,7 / 230,7; O3: 32,1 / 36,7; SO2: 288,0 / 73,7 |

Giải thích: năm giờ có đúng hai bản ghi mỗi giờ và không có cơ sở xác định dòng đúng. Quyết định: chưa chọn hay loại bản ghi. Ảnh hưởng: Câu hỏi 3–4 có thể bị ảnh hưởng bởi chỉ số chất ô nhiễm; Câu hỏi 1–2 và dự đoán bị ảnh hưởng trực tiếp ở một giờ AQI xung đột. Vị trí: Notebook 1, bước 2.4.

**Bước 2.5 — Đánh giá phương án.** Mục tiêu/lý do: lượng hóa ảnh hưởng trước khi quyết định. Đầu vào: năm cặp từ 2.4. Phương pháp: tính chênh tuyệt đối và trung bình từng cặp chỉ để so sánh phương án, **không áp dụng giá trị trung bình vào dữ liệu**. Output: AQI ở 01/12/2024 có lựa chọn 165, 173 hoặc trung bình 169 (chênh đầu/cuối 8); CO ở 01/11/2024 chênh 1.009,9; SO2 ở 01/05/2025 chênh 214,3. Các phương án:

1. Giữ dòng đầu hoặc dòng cuối: còn một dòng mỗi giờ nhưng chọn giá trị thiếu căn cứ, có thể làm sai AQI và chất ô nhiễm.
2. Lấy trung bình hai dòng: còn một dòng mỗi giờ, nhưng tạo giá trị mới không có chứng cứ là phép gộp đúng.
3. Giữ cả hai: không mất thông tin gốc nhưng giờ không duy nhất, gây đếm đôi và khó lập chuỗi ngày.
4. Loại cả hai dòng ở năm giờ: tránh chọn tùy ý nhưng mất năm giờ quan sát, kể cả các cột hai dòng vốn giống nhau.
5. **Đề xuất chờ xác nhận:** tạo một dòng cho mỗi giờ, giữ giá trị của những cột hai dòng đồng ý, đặt `NaN` cho từng cột bất đồng; lưu bảng kiểm toán riêng ghi hai giá trị gốc. Phương án này giữ 30.336 giờ duy nhất và 19 cột trong bảng phân tích, nhưng AQI thiếu thêm một giờ (tại 01/12/2024) và một số chất ô nhiễm có thêm NaN. Không tự chọn bản ghi đúng. Có thể đánh giá độ nhạy bằng cách tính lại kết quả quan trọng với từng lựa chọn gốc.

**Quyết định được người dùng xác nhận:** Áp dụng phương án 5 ở bước 2.6; giữ nguyên cột đồng thuận, đặt `NaN` ở cột bất đồng, không lấy dòng đầu/cuối/trung bình. Bảng kiểm toán riêng giữ hai giá trị và số dòng nguồn.

**Bước 2.6 — Giải quyết xung đột và lưu kiểm toán.** Mục tiêu/lý do: một dòng mỗi giờ mà không tự chọn giá trị bất đồng. Đầu vào: bốn bản sao và năm cặp đã kiểm tra. Phương pháp: giữ các dòng không xung đột; với từng cặp, giữ giá trị cột đồng thuận, gán `NaN` cột bất đồng; `to_csv()` ghi kiểm toán. Output: `output/tables/duplicate_conflicts_audit.csv` có 13 dòng thuộc 5 mốc, ghi năm, `local_time` đầy đủ đến giây, số dòng CSV thứ nhất/thứ hai, tên cột, hai giá trị, quyết định và giá trị sau xử lý. Lần ghi đầu chỉ hiện ngày ở các mốc 00:00; đã sửa định dạng và chạy lại notebook, xác minh CSV hiện ghi `00:00:00`. Quyết định: không điền 13 ô thiếu mới. Vị trí: Notebook 1, bước 2.6.

**Bước 2.7 — Xác minh bảo toàn.** Mục tiêu/lý do: chứng minh thao tác chỉ ảnh hưởng các cặp xung đột. Đầu vào: bản sao trước/sau, bảng kiểm toán. Phương pháp: `assert` trên số dòng, ô thiếu, tập giờ, dòng không xung đột và cột đồng thuận. Output: 30.341 → 30.336 dòng; 302 → 315 ô thiếu, đúng 13 ô mới; 0 giờ trùng sau xử lý; không mất mốc giờ; mọi dòng không xung đột và cột đồng thuận giữ nguyên. NaN mới theo cột: SO2 4, O3 3, CO 2, NO2 1, AQI 1, PM10 1, PM25 1. Quyết định: kết quả khớp quy tắc. Vị trí: Notebook 1, bước 2.7.

**Bước 2.8 — Đánh giá dữ liệu thiếu.** Mục tiêu/lý do: phân biệt ô trống gốc, ô trống do xung đột và giờ không có dòng. Đầu vào: bảng trước/sau theo năm. Phương pháp: `isna().sum()` theo cột/năm và đối chiếu phạm vi giờ. Output: 2022 giữ nguyên 302 ô thiếu gốc; 2024 thêm 10, 2025 thêm 3; ngày 31/12/2023 vắng 24 dòng chứ không phải 24 ô NaN. Cụm CO/NO2/O3/SO2 thiếu 74 giờ và AQI/PM10/PM25 thiếu 2 giờ được giữ nguyên. Các phương án cân nhắc: điền tiến/lùi/nội suy, loại dòng, giữ NaN. **Lựa chọn: giữ NaN** vì chưa có căn cứ ước lượng đáng tin, đặc biệt qua 74 giờ; pandas có thể tính trên cặp hợp lệ và phải báo số quan sát. Chưa tạo giờ giả. Vị trí: Notebook 1, bước 2.8.

**Bước 2.9 — Đánh giá ảnh hưởng.** Mục tiêu/lý do: biết hệ quả của một AQI xung đột trước EDA và dự đoán. Đầu vào: dữ liệu 2024 trước/sau xử lý. Phương pháp: tính trung bình ngày chỉ trên AQI quan sát, so độ nhạy giả định chọn 165 hoặc 173; đếm cặp AQI–chất ô nhiễm hợp lệ. Output: 01/12/2024 còn 23 giờ AQI, trung bình quan sát 160,8261. Nếu chọn 165 hoặc 173, trung bình 24 giờ sẽ là 161,0000 hoặc 161,3333; khoảng chênh giữa hai lựa chọn là 0,3333 theo ngày, 0,010753 theo tháng và 0,000911 theo năm nếu mẫu số đủ giờ. Số cặp hợp lệ giảm do gộp dòng và đặt NaN; ví dụ AQI–SO2 của 2024 giảm từ 8.788 xuống 8.780. 01/07/2022 có 22 giờ AQI, 01/12/2024 có 23; mô hình dự đoán phải định nghĩa ngày đủ giờ ở Giai đoạn 9. Đây là đánh giá độ nhạy tiền xử lý, **chưa trả lời câu hỏi nghiên cứu**. Vị trí: Notebook 1, bước 2.9.

**Bước 2.10 — Đánh giá cực trị.** Mục tiêu/lý do: tránh xóa nhầm giờ ô nhiễm có thể thật. Đầu vào: AQI và chất ô nhiễm sau xử lý xung đột. Phương pháp: kiểm tra miền cơ bản, số âm, AQI=500, CO=0 và cực đại; không dùng IQR/Z-score để loại. Output: 0 AQI ngoài 0–500 và 0 chất ô nhiễm âm; giữ 59 AQI=500 và 3 CO=0 ở 2024. Cực đại còn lại gồm CO 15.956,2, O3 700, PM10 1.103 ở 2024 và SO2 721 ở 2025. Các phương án cân nhắc: xóa/cắt ngưỡng hoặc giữ và kiểm tra độ nhạy. **Lựa chọn: giữ nguyên** vì đơn vị, trạm và quy tắc nguồn chưa xác minh; chưa đủ căn cứ gọi là lỗi. Vị trí: Notebook 1, bước 2.10.

**Bước 2.11 — Ghép và sắp xếp.** Mục tiêu/lý do: tạo bảng chung duy nhất theo giờ quan sát. Đầu vào: bốn bảng sau xử lý trùng. Phương pháp: `pd.concat()`, `sort_values('Local Time')`, đặt lại index. Output: 30.336 dòng × 19 cột, từ 13/01/2022 00:00 đến 30/06/2025 23:00, 0 giờ trùng, 315 ô thiếu và thời gian tăng dần. Quyết định: dùng bảng này cho các giai đoạn sau, không bổ sung giờ vắng. Vị trí: Notebook 1, bước 2.11.

**Bước 2.12 — Đối chiếu trước/sau.** Mục tiêu/lý do: xác minh không có biến đổi ngoài quy tắc. Đầu vào: bảng 30.341 dòng đã chuẩn hóa tên cột và bảng cuối 30.336 dòng. Phương pháp: lập bảng chỉ số trước/sau và `assert`. Output: giờ duy nhất 30.336 → 30.336; dòng dư do giờ trùng 5 → 0; ô thiếu 302 → 315; AQI thiếu 2 → 3; AQI=500 giữ 59; CO=0 giữ 3; phạm vi thời gian không đổi. Ngày 31/12/2023 vẫn không có bản ghi; 2022 bắt đầu 13/01 và 2025 kết thúc 30/06. Quyết định: đủ điều kiện lưu file mới. Vị trí: Notebook 1, bước 2.12.

**Bước 2.13 — Lưu và đọc lại.** Mục tiêu/lý do: cung cấp đầu vào ổn định cho Notebook 2 và các bước phân tích. Đầu vào: bảng sau ghép và sắp. Phương pháp: `to_csv()` vào `output/data/`, sau đó `read_csv()` kiểm tra. Output: `aqi_hanoi_cleaned.csv` có 30.336 dòng × 19 cột, 315 ô thiếu, 30.336 giờ duy nhất; dung lượng 4.384.992 byte khi chạy bước này. Quyết định: dùng file này ở các giai đoạn sau, nhưng vẫn phải xem xét giới hạn khi tính trung bình ngày và tương quan. Vị trí: Notebook 1, bước 2.13.

**Ảnh hưởng còn lại:** Câu hỏi 1–2 cần báo độ bao phủ và không coi 2025 là cả năm; Câu hỏi 3–4 cần đếm số cặp hợp lệ và đánh giá độ nhạy với cực trị; dự đoán cần quy tắc ngày đủ giờ, không sử dụng ngày 31/12/2023 như quan sát thật và không để thông tin tương lai lọt vào đặc trưng. Chưa đưa ra kết luận của bốn câu hỏi hay kết quả mô hình.

## 7. Exploratory Data Analysis (EDA)

Đã dùng `output/data/aqi_hanoi_cleaned.csv`; không thay đổi quy tắc làm sạch hoặc ghi đè file này. Những thống kê dưới đây mô tả dữ liệu quan sát, **chưa trả lời bốn câu hỏi nghiên cứu**. Các hình nằm trong `output/figures/` và được nhúng ở Code cell tương ứng trong Notebook 1.

**Bước 3.1 — Thống kê tổng quan.** Mục tiêu/lý do: nắm số giá trị hợp lệ, mức trung tâm, độ phân tán và đuôi phân phối. Đầu vào: 30.336 dòng, 14 cột số của file đã xử lý. Phương pháp: pandas `agg()` và `quantile()` cho count, mean, median, std, min, p05, p25, p75, p95, max. Output: AQI có 30.333 giá trị, trung bình 123,79, trung vị 112, độ lệch chuẩn 57,71, p25=80, p75=157, p95=228, phạm vi 4–500. Bảy biến thời tiết đủ 30.336; chất ô nhiễm có 30.258–30.333 giá trị hợp lệ. Trung bình dễ bị giá trị lớn chi phối hơn trung vị; phân vị cho biết vị trí trong phân phối, độ lệch chuẩn đo mức phân tán. CO có trung bình 425,43 và trung vị 181,50, gợi ý cần xem phân phối. Quyết định: quan sát biểu đồ trước khi diễn giải; không thay đổi dữ liệu. Vị trí: Notebook 1, bước 3.1, không tạo hình.

**Bước 3.2 — Phân phối AQI.** Mục tiêu/lý do: kiểm tra hình dạng và ảnh hưởng giá trị lớn. Đầu vào: 30.333 AQI hợp lệ. Phương pháp: `skew()`, phân vị, đếm AQI≥300 và AQI=500; Seaborn `histplot()` và `boxplot()`. Output: trung bình 123,787, trung vị 112, skew 1,494, p95=228, p99=270; 173 giờ AQI≥300, trong đó 59 giờ AQI=500. Histogram và boxplot cho thấy đuôi phải và các điểm cao ngoài râu boxplot. Giải thích: giá trị cao góp phần kéo trung bình lên; điểm ngoài râu không tự động là lỗi. Quyết định: giữ nguyên và đánh giá độ nhạy khi phù hợp. Hình: `output/figures/eda_aqi_distribution.png`; vị trí Notebook 1, bước 3.2.

**Bước 3.3 — Độ bao phủ ngày/tháng/năm.** Mục tiêu/lý do: tránh nhầm giờ không có bản ghi với ô AQI thiếu. Đầu vào: `Local Time`, `AQI` của file đã xử lý; lịch 01/01/2022–31/12/2025 chỉ dùng để kiểm tra độ bao phủ, không điền giá trị AQI. Phương pháp: `groupby()` theo ngày, `reindex()` lịch, tổng hợp tháng/năm và heatmap tỷ lệ giờ có bản ghi. Output: 2022 có 353 ngày ghi nhận, thiếu 12 ngày đầu; 2023 có 364 ngày, thiếu 31/12; 2024 đủ 366 ngày; 2025 có 181 ngày đến 30/06 và vắng 184 ngày sau đó. Số giờ có bản ghi theo năm: 8.472, 8.736, 8.784, 4.344; AQI hợp lệ: 8.470, 8.736, 8.783, 4.344. Ngày 01/07/2022 có 24 dòng/22 AQI; 01/12/2024 có 24 dòng/23 AQI. Heatmap biểu diễn **giờ có bản ghi**, nên tháng 01/2022 ~61%, tháng 12/2023 ~97%, tháng 07–12/2025 0%; nó không thể hiện trực tiếp giờ AQI hợp lệ. Quyết định: các so sánh sau phải báo độ bao phủ. Hình: `output/figures/eda_coverage.png`; vị trí Notebook 1, bước 3.3.

**Bước 3.4 — Biến động AQI theo thời gian.** Mục tiêu/lý do: xem biến thiên ngày/tháng trước phân tích chuyên sâu. Đầu vào: AQI và giờ hợp lệ theo ngày/tháng. Phương pháp: `groupby()` và trung bình bỏ qua NaN; đường Matplotlib cho chuỗi ngày và tháng. Output: 1.264 ngày có trung bình AQI quan sát, 197 ngày trong lịch kiểm tra không có AQI ngày; 42 tháng có dữ liệu, trung bình tháng nằm trong 68,54–187,09. Bảng tháng nêu số giờ hợp lệ, ví dụ 01/2022 có 456 giờ. Một đỉnh ngày trên hình được kiểm tra riêng: 14/04/2024 có đủ 24 giờ và cả 24 AQI đều bằng 500. Giải thích: đỉnh không phát sinh do thiếu giờ, nhưng nguồn gốc cụm AQI=500 chưa xác minh. Quyết định: không suy ra xu hướng dài hạn từ hình tổng quan; Câu hỏi 1–2 cần thiết kế so sánh công bằng và cân nhắc độ nhạy. Hình: `output/figures/eda_aqi_over_time.png`; vị trí Notebook 1, bước 3.4.

**Bước 3.5 — Phân phối chất ô nhiễm.** Mục tiêu/lý do: xác định phạm vi, độ lệch và cụm cực trị trước Câu hỏi 3. Đầu vào: CO, NO2, O3, PM10, PM25, SO2. Phương pháp: count, median, p95, p99, max, `skew()` và sáu histogram với **trục đếm log** để vẫn thấy đuôi hiếm; không cắt trục giá trị và không gán đơn vị chưa xác minh. Output: tất cả skew dương; PM10 skew 6,92, CO skew 5,84. CO trung vị 181,50, p99 3.643,95, max 15.956,2; PM10 trung vị 52,20, p99 305, max 1.103. Kiểm tra cụm cực trị: PM10=1.103 có 58 giờ từ 11–22/04/2024; SO2≥700 có 78 giờ từ 20/04–03/05/2025. Quyết định: chưa đánh giá đúng/sai hoặc tương quan; Câu hỏi 3 cần xem độ nhạy với cụm cực trị. Hình: `output/figures/eda_pollutants.png`; vị trí Notebook 1, bước 3.5.

**Bước 3.6 — Phân phối thời tiết.** Mục tiêu/lý do: nhận biết đặc trưng bảy biến trước Câu hỏi 4. Đầu vào: Temperature, Relative Humidity, Precipitation, Wind Speed, Pressure, Clouds, UV Index. Phương pháp: count, mean, median, p95, max, số giá trị 0; histogram bốn biến trọng tâm. Output: mọi biến đủ 30.336 giá trị. Temperature 7–40, trung vị 25,7; Relative Humidity trung vị 82; Wind Speed trung vị 2,0, max 14,9. Precipitation bằng 0 ở 25.365 giờ (~83,6%); UV Index bằng 0 ở 15.010 giờ. Pressure trung vị 1.010, Clouds 79. Giải thích: mưa tập trung mạnh tại 0, cần chú ý khi chia nhóm hoặc diễn giải. Quyết định: chưa phân tích quan hệ thời tiết–AQI, không gán đơn vị chưa xác minh. Hình: `output/figures/eda_weather.png`; vị trí Notebook 1, bước 3.6.

**Giới hạn EDA:** Phạm vi năm không đều; 302 ô thiếu gốc và 13 ô thiếu do xung đột vẫn được giữ; thiếu đơn vị đo, thông tin trạm và nguồn gốc cụm cực trị. Các bảng và hình chưa là bằng chứng cho quan hệ nhân quả hoặc câu trả lời của bốn câu hỏi.

**Kiểm tra cuối Giai đoạn 3:** Notebook hợp lệ, gồm 98 cell với 32 Code cell; mọi Code cell có Markdown ngay trước và sau, không có output lỗi và có năm hình nhúng. Đã chạy lại toàn bộ 32 Code cell thành công. Hash của dữ liệu đã xử lý và bảng kiểm toán trước/sau lần chạy lại không đổi.

## 8. Bốn câu hỏi nghiên cứu

Các câu hỏi đã xác nhận ở Mục 1. Câu hỏi 1 đã được người dùng cập nhật và triển khai theo ba phần dưới đây. Câu hỏi 2–4 đã triển khai ở Giai đoạn 5–7 bên dưới. Không suy luận quan hệ nhân quả từ các bảng so sánh.

### Câu hỏi 1 — AQI thay đổi giữa các năm trong nửa đầu và nửa cuối năm như thế nào?

**Bước 4.1 — Phần A, nửa đầu 2022–2025.** Mục tiêu: so sánh bốn năm trên cùng tập ngày 13/01–30/06. Lý do: 2022 bắt đầu 13/01, 2025 kết thúc 30/06, còn 2024 có ngày nhuận. Đầu vào: `Local Time`, `AQI` từ dữ liệu đã xử lý. Phương pháp: lọc chuỗi tháng-ngày, loại 29/02/2024 (24 giờ), dùng `groupby().agg()` tính count, mean, median, std, min, max và phép trừ trung bình năm kề nhau. `groupby()` gom các giờ cùng năm để tính thống kê riêng cho năm đó. Mỗi năm có đúng 169 ngày, 4.056 giờ có bản ghi và 4.056 AQI hợp lệ.

| Năm | Giờ AQI | Trung bình | Trung vị | Độ lệch chuẩn | Min | Max |
|---|---:|---:|---:|---:|---:|---:|
| 2022 | 4.056 | 125,828 | 112 | 53,293 | 21 | 475 |
| 2023 | 4.056 | 121,880 | 115 | 46,234 | 21 | 276 |
| 2024 | 4.056 | 119,832 | 100 | 76,184 | 4 | 500 |
| 2025 | 4.056 | 138,409 | 131 | 51,056 | 22 | 470 |

Chênh trung bình 2022→2023 là −3,947; 2023→2024 là −2,049; 2024→2025 là +18,577; 2022→2025 là +12,581. Biểu đồ cột từ mốc 0 ở `output/figures/q1_first_half.png`, nhúng tại Notebook 1 bước 4.1. **Trả lời Phần A:** AQI trung bình trong nửa đầu giảm từ 2022 đến 2024 rồi tăng vào 2025. Độ phân tán 2024 cao, có cụm AQI=500 đã phát hiện; chưa giải thích nguyên nhân hoặc xu hướng dài hạn.

**Bước 4.2 — Phần B, nửa cuối 2022–2024.** Mục tiêu: so sánh ba năm trên cùng tập ngày 01/07–30/12. Lý do: 2023 thiếu 31/12 và 2025 không có nửa cuối. Đầu vào: `Local Time`, `AQI` đã xử lý. Phương pháp: lọc ngày, `groupby().agg()` cùng sáu chỉ số, đếm giờ có bản ghi và giờ AQI hợp lệ. Mỗi năm có 183 ngày, 4.392 giờ có bản ghi; AQI thiếu ở 01/07/2022 06:00–07:00 và 01/12/2024 00:00.

| Năm | Giờ AQI | Trung bình | Trung vị | Độ lệch chuẩn | Min | Max |
|---|---:|---:|---:|---:|---:|---:|
| 2022 | 4.390 | 112,746 | 102 | 47,376 | 21 | 276 |
| 2023 | 4.392 | 121,126 | 103 | 60,726 | 25 | 487 |
| 2024 | 4.391 | 118,775 | 109 | 58,453 | 12 | 276 |

Chênh trung bình 2022→2023 là +8,380; 2023→2024 là −2,350; 2022→2024 là +6,030. Biểu đồ cột từ mốc 0 ở `output/figures/q1_second_half.png`, nhúng tại Notebook 1 bước 4.2. **Trả lời Phần B:** AQI trung bình nửa cuối tăng từ 2022 đến 2023 rồi giảm nhẹ vào 2024. Ba giờ AQI thiếu được loại khỏi mẫu số của trung bình và phải được báo cùng số giờ hợp lệ.

**Bước 4.3 — Phần C, đối chiếu.** Mục tiêu: kiểm tra thay đổi giữa các năm có nhất quán ở hai nửa hay không. Đầu vào: hai bảng từ bước 4.1–4.2. Phương pháp: đặt trung bình của 2022–2024 cạnh nhau, tính chênh năm kề nhau ở từng nửa và kiểm tra dấu thay đổi bằng NumPy. Biểu đồ cột nhóm ở `output/figures/q1_half_year_comparison.png`, nhúng tại Notebook 1 bước 4.3.

| Khoảng so sánh | Chênh nửa đầu | Chênh nửa cuối | Cùng chiều? |
|---|---:|---:|---|
| 2022→2023 | −3,947 | +8,380 | Không |
| 2023→2024 | −2,049 | −2,350 | Có, đều giảm |

**Trả lời Câu hỏi 1:** Thay đổi không nhất quán hoàn toàn giữa hai nửa năm: 2022→2023 hai nửa ngược chiều, 2023→2024 cả hai cùng giảm. Nửa đầu 2025 có AQI trung bình 138,409, nhưng không có dữ liệu nửa cuối 2025 để đối chiếu. Kết quả chỉ mô tả hai khoảng ngày được chọn; không chứng minh nguyên nhân hay xu hướng dài hạn. Các nửa có số ngày khác nhau (169 so với 183), vì vậy biểu đồ nhóm chỉ đối chiếu **mức trung bình trong từng khoảng**, không coi hai nửa là cùng một tập ngày.

### Phân tích độ nhạy AQI = 500 (bổ sung cho Câu hỏi 1)

**Mục đích:** Đánh giá 59 giá trị AQI=500 ảnh hưởng bao nhiêu đến thống kê và kết luận của Câu hỏi 1. Đây là phép lọc **tạm trong bộ nhớ**; file dữ liệu đã làm sạch và kết quả chính ở bước 4.1–4.3 giữ nguyên. Không có bằng chứng xác định AQI=500 là sai.

**Bước 4.4 — Đếm giá trị AQI=500.** Đầu vào: dữ liệu đã làm sạch và hai tập Phần A/B của Câu hỏi 1. Phương pháp: pandas `groupby()` theo năm và đếm điều kiện `AQI == 500`. Output: toàn bộ 59 giờ thuộc 2024 và Phần A (13/01–30/06, đã loại 29/02); Phần B có 0, các năm 2022/2023/2025 có 0. Ý nghĩa: phép lọc tạm chỉ có thể làm thay đổi thống kê nửa đầu 2024. Vị trí: Notebook 1, bước 4.4.

**Bước 4.5 — Thống kê đối chiếu.** Đầu vào: đúng hai phạm vi của bước 4.1–4.2. Phương pháp: lập bản lọc tạm `AQI != 500`, dùng `groupby().agg()` tính count, mean, median; lấy chênh `mean_without_500 - mean_original` và chia cho `mean_original` để ra phần trăm. Bảng Phần A:

| Năm | Giờ gốc → lọc tạm | Trung bình gốc → lọc tạm | Chênh trung bình | Chênh % | Trung vị gốc → lọc tạm |
|---|---:|---:|---:|---:|---:|
| 2022 | 4.056 → 4.056 | 125,8279 → 125,8279 | 0 | 0% | 112 → 112 |
| 2023 | 4.056 → 4.056 | 121,8804 → 121,8804 | 0 | 0% | 115 → 115 |
| 2024 | 4.056 → 3.997 | 119,8319 → 114,2202 | −5,6117 | −4,683% | 100 → 99 |
| 2025 | 4.056 → 4.056 | 138,4090 → 138,4090 | 0 | 0% | 131 → 131 |

Ở Phần B, cả ba năm giữ nguyên số giờ hợp lệ, trung bình và trung vị: 2022 là 4.390 giờ, trung bình 112,7456, trung vị 102; 2023 là 4.392 giờ, trung bình 121,1257, trung vị 103; 2024 là 4.391 giờ, trung bình 118,7752, trung vị 109. Mọi chênh tuyệt đối và phần trăm ở Phần B đều bằng 0. Ý nghĩa: 59 giờ ở 2024 kéo trung bình nửa đầu lên 5,6117 điểm, trong khi trung vị chỉ thay đổi 1 điểm. Vị trí: Notebook 1, bước 4.5; bảng in trực tiếp, không tạo hình mới.

**Bước 4.6 — Đánh giá kết luận.** Phương pháp: sắp thứ tự năm theo trung bình ở hai phương án, tính chênh năm kề nhau và đối chiếu dấu bằng NumPy. Phần A có cùng thứ tự cao→thấp ở cả hai phương án: 2025 > 2022 > 2023 > 2024. Phần B giữ thứ tự 2023 > 2024 > 2022. Chiều thay đổi cũng không đổi: Phần A giảm 2022→2024 rồi tăng 2024→2025; Phần B tăng 2022→2023 rồi giảm 2023→2024. Nhưng độ lớn ở Phần A thay đổi: 2023→2024 từ −2,0486 thành −7,6603, 2024→2025 từ +18,5772 thành +24,1889. **Kết luận:** Câu trả lời chính vẫn phù hợp về thứ tự và chiều, nhưng độ lớn liên quan 2024 nhạy với AQI=500. Giữ 59 giá trị trong dữ liệu chính vì chưa xác minh được là lỗi; chỉ dùng lọc tạm để minh bạch mức ảnh hưởng. Vị trí: Notebook 1, bước 4.6.

**Kiểm tra cuối Giai đoạn 4:** Notebook hợp lệ, 118 cell gồm 38 Code cell; mọi Code cell có Markdown ngay trước/sau và 0 output lỗi. Đã chạy lại toàn bộ 38 Code cell thành công. Hash của dữ liệu đã làm sạch và bảng kiểm toán xung đột không đổi sau lần chạy lại.

### Giai đoạn 5 — Câu hỏi 2: mẫu hình AQI theo tháng và mùa

**Mục tiêu:** Kiểm tra AQI theo tháng và nhóm mùa có lặp lại giữa các năm. Phân tích chính dùng 2022–2024 vì có bản ghi ở cả 12 tháng; 2025 chỉ đối chiếu tháng 1–6. Đầu vào là `output/data/aqi_hanoi_cleaned.csv` đã được xác nhận ở Giai đoạn 2, đọc qua notebook. Không điền thời điểm thiếu, không đổi quy tắc làm sạch.

**Bước 5.1 — Năm, tháng và độ bao phủ.** Tạo `year`, `month` từ `Local Time`; `groupby().agg()` đếm giờ có bản ghi và giờ AQI hợp lệ, so với số giờ lịch của từng tháng bằng `pandas.Period`. Cả 2022–2024 có 12 tháng có bản ghi. Bốn tháng dưới 100% giờ AQI: 01/2022 456/744 (61,29%), 07/2022 742/744 (99,73%), 12/2023 720/744 (96,77%) và 12/2024 743/744 (99,87%). Tháng 7–12/2025 không có bản ghi và được giữ trống. Thiếu giờ lịch khác với ô AQI thiếu trong giờ có bản ghi.

**Bước 5.2 — Quy ước mùa.** Phân nhóm lịch: xuân 3–5, hè 6–8, thu 9–11, đông 12–2. Nhãn `season_year` là năm dương lịch của từng bản ghi: đông 2024 gồm tháng 1–2 và tháng 12/2024, không phải một mùa đông liền mạch. Cả 2022–2024 có bản ghi ở ba tháng mỗi nhóm. Năm 2025 chỉ có hai tháng đông, ba tháng xuân, một tháng hè nên không được xếp hạng mùa trọn năm. Quy ước này không xác nhận điều kiện khí tượng thực tế.

**Bước 5.3 — Thống kê tháng.** `groupby(['year','month'])['AQI'].agg(['count','mean','median','std','min','max'])`, ghép độ bao phủ và `pivot`. Bảng chi tiết và ma trận năm × tháng nằm tại Notebook 1 bước 5.3. Tháng 04/2024: trung bình 187,09, trung vị 141,00, độ lệch chuẩn 129,55; khoảng cách trung bình–trung vị đòi hỏi kiểm tra AQI=500. Tháng không có dữ liệu của 2025 là `NaN` trong ma trận.

**Bước 5.4 — Biểu đồ tháng.** Heatmap năm × tháng tại `output/figures/q2_monthly_heatmap.png` và đường 12 tháng từng năm tại `output/figures/q2_monthly_lines.png`, nhúng ở Notebook 1 bước 5.4. Dấu `*` và vòng tròn rỗng nhận diện bốn tháng chưa đủ giờ; ô 2025 tháng 7–12 để trắng. Hình gợi ý mức thấp quanh 6–8, nhưng thứ hạng và hệ số ở bước 5.6 mới dùng để đánh giá tính lặp lại.

**Bước 5.5 — Thống kê mùa.** `groupby()` tính số giờ AQI, trung bình, trung vị, độ lệch chuẩn, min, max; đếm ngày hợp lệ và so với giờ lịch. Biểu đồ cột ở `output/figures/q2_seasonal_means.png`, nhúng trong Notebook 1 bước 5.5. Hè thấp nhất ở cả ba năm: 87,16 (2022), 90,13 (2023), 82,83 (2024). Đông cao nhất 2022 (148,50) và 2023 (153,98), xuân cao nhất 2024 (135,72). Chênh cao–thấp là 61,34; 63,85; 52,89. Đông 2022 có 1.872/2.160 giờ AQI (86,67%, 78 ngày), đông 2023 có 2.136/2.160 (98,89%, 89 ngày), đông 2024 có 2.183/2.184 (99,95%, 91 ngày). Vì vậy xếp hạng đông phải đọc cùng độ bao phủ.

**Bước 5.6 — Tính lặp lại.** Tháng 7 thấp nhất cả 2022–2024: 83,26; 84,75; 68,54. Tháng cao nhất là tháng 1 ở 2022 (153,15, nhưng chỉ 61,29% giờ lịch) và 2023 (168,75); tháng 4 ở 2024 (187,09). Tương quan Spearman của thứ hạng 12 trung bình tháng: 2022–2023 = 0,888; 2022–2024 = 0,692; 2023–2024 = 0,720. Hè luôn thấp nhất, nhưng thứ tự các mùa còn lại khác nhau. Trong sáu tháng có dữ liệu năm 2025, tháng 1 cao nhất (176,08) và tháng 6 thấp nhất (109,26); không suy rộng ra cả năm.

**Bước 5.7 — Độ nhạy AQI=500.** Lọc tạm trong bộ nhớ đối với 2024; giữ nguyên dữ liệu chính và các kết quả chính. Có 58 giờ AQI=500 ở tháng 4, một giờ ở tháng 2. Khi lọc tạm, tháng 4 giảm 187,093→159,678 (−27,415), tháng 2 giảm 96,772→96,191 (−0,580); xuân giảm 135,725→125,898 (−9,827), đông giảm 134,148→133,980 (−0,168). Tháng cao nhất 2024 đổi từ tháng 4 sang tháng 12, mùa cao nhất đổi từ xuân sang đông; tháng 7 và hè vẫn thấp nhất. Đây là kiểm tra độ nhạy, không phải xác định AQI=500 là sai.

**Bước 5.8 — Trả lời Câu hỏi 2.** Mẫu hình lặp lại **một phần**: giai đoạn AQI thấp (tháng 7, nhóm hè) nhất quán trong cả ba năm, trong khi tháng và mùa cao nhất thay đổi; ngoại lệ đỉnh 2024 nhạy với AQI=500. Spearman 0,692–0,888 cho thấy thứ tự tháng tương đồng nhưng không trùng khớp. Hạn chế: 01/2022 và 12/2023 thiếu ngày, nhóm đông ghép tháng không liên tục trong cùng năm, chỉ ba năm có đủ 12 tháng quan sát và 2025 chỉ có sáu tháng. Kết quả là mô tả trên dữ liệu quan sát, không chứng minh nguyên nhân hoặc một chu kỳ ổn định dài hạn.

**Quyết định:** Giữ nguyên file dữ liệu đã xử lý và 59 giá trị AQI=500. Chỉ dùng bảng lọc tạm để diễn giải độ nhạy. Giai đoạn 6 chỉ bắt đầu sau khi người dùng xác nhận Giai đoạn 5.

**Kiểm tra cuối Giai đoạn 5:** Notebook hợp lệ, 143 cell gồm 46 Code cell; mỗi Code cell có Markdown ngay trước và ngay sau, mọi output đã lưu không có lỗi. Chạy lại toàn bộ 46 Code cell từ đầu thành công. SHA-256 của `output/data/aqi_hanoi_cleaned.csv` trước và sau lần chạy lại đều là `aff2960ae7e4e79e557eacce7846726662d9301a1aff28b234ca5ab1f65c6fb7`.

### Giai đoạn 6 — Câu hỏi 3: AQI và sáu chất ô nhiễm

**Mục tiêu và dữ liệu:** Phân tích liên hệ thống kê của AQI với PM25, PM10, CO, NO2, O3, SO2 theo giờ, toàn kỳ và từng năm. Dùng `Local Time` và bảy cột số trong dữ liệu đã làm sạch. Tính từng cặp hợp lệ riêng; không điền NaN, không xóa dòng khỏi các cặp khác. Tương quan chỉ mô tả sự đồng biến, không chứng minh nguyên nhân.

**Bước 6.1 — Chất lượng đầu vào.** Cả bảy biến là `float64`. Số cặp AQI–chất hợp lệ: PM25 30.333; PM10 30.333; CO 30.259; NO2 30.260; O3 30.258; SO2 30.257. Bảng kiểm toán xung đột cho thấy 13 ô thiếu mới: AQI 1, PM25 1, PM10 1, CO 2, NO2 1, O3 3, SO2 4. Ô thiếu gốc tương ứng: 2, 2, 2, 74, 74, 74, 74. Đã đối chiếu không có số thiếu gốc âm. Phương pháp: `notna()`, `dropna()` trên từng cặp và `value_counts()` của bảng kiểm toán.

**Bước 6.2 — Phân phối và cực trị.** `median`, phân vị 99%, `max`, `skew()` cho thấy mọi biến lệch phải: AQI 1,494; PM25 2,780; PM10 6,925; CO 5,840; NO2 3,524; O3 1,457; SO2 4,827. PM10 max 1.103 so với p99 305; CO max 15.956,2 so với p99 3.643,946. Có 59 giờ AQI=500 trong 2024. Không tự loại cực trị hoặc khẳng định chúng sai.

**Bước 6.3–6.4 — Pearson và Spearman.** Pearson đo liên hệ tuyến tính giữa giá trị gốc; Spearman đo liên hệ đơn điệu qua thứ hạng và ít nhạy hơn với cực trị. Quy ước mô tả: |r|≥0,7 mạnh, 0,3–<0,7 vừa, <0,3 yếu; đây không phải kiểm định. Hệ số tính bằng `Series.corr()`/`DataFrame.corr()` trên từng cặp hợp lệ:

| Chất | Cặp hợp lệ | Pearson | Spearman |
|---|---:|---:|---:|
| PM25 | 30.333 | 0,8107 | 0,9850 |
| PM10 | 30.333 | 0,8195 | 0,9545 |
| CO | 30.259 | −0,0176 | 0,1076 |
| NO2 | 30.260 | 0,2540 | 0,2107 |
| O3 | 30.258 | −0,0099 | −0,0017 |
| SO2 | 30.257 | 0,2022 | 0,2649 |

PM10 đứng đầu theo trị tuyệt đối Pearson, PM25 đứng đầu theo Spearman; O3 thấp nhất cả hai. Chênh Spearman–Pearson của PM25 là +0,1743, PM10 +0,1351; của CO +0,1252 và đổi dấu nhưng các hệ số CO đều nhỏ. Không quy chênh lệch vào một nguyên nhân khi chưa xác định hình dạng quan hệ và cơ chế dữ liệu.

**Bước 6.5 — Biểu đồ.** Heatmap hai phương pháp tại `output/figures/q3_correlation_heatmap.png`; ba hexbin AQI–PM10, AQI–PM25, AQI–CO tại `output/figures/q3_pollutant_hexbins.png`, nhúng ở Notebook 1 bước 6.5. Hexbin dùng mọi cặp hợp lệ, thang màu log cho số giờ để hạn chế chồng điểm. Hình PM25 có các dải tăng theo đoạn, PM10 phân tán hơn, CO không có dải tăng tuyến tính rõ; hình không xác định công thức AQI. Không gán đơn vị chất ô nhiễm chưa xác minh.

**Bước 6.6 — Theo năm.** Tính Pearson/Spearman và số cặp riêng cho 2022–2025. Pearson PM25 lần lượt 0,884; 0,873; 0,716; 0,852 và PM10 0,874; 0,884; 0,814; 0,796: luôn dương mạnh. Spearman PM25 0,991; 0,992; 0,976; 0,975 và PM10 0,985; 0,985; 0,931; 0,879. CO Pearson biến thiên từ +0,519 (2022) đến −0,129 (2024); NO2 từ +0,452 (2023) đến +0,030 (2024); O3 đổi dấu ở 2024. SO2 dương nhưng không ổn định về độ lớn. Năm 2025 chỉ có tháng 1–6, khoảng 4.343–4.344 cặp mỗi chất, nên so sánh năm phải thận trọng.

**Bước 6.7 — Độ nhạy.** Chỉ lọc tạm 59 giờ AQI=500 của 2024 trong bộ nhớ. Pearson PM25 tăng 0,7163→0,8017 (Δ+0,0854), PM10 0,8139→0,8383 (+0,0245); Spearman PM25 0,9759→0,9798 (+0,0039), PM10 0,9313→0,9299 (−0,0014). Pearson các chất khác thay đổi không quá 0,0262; Spearman không quá 0,0103. Kết luận PM25/PM10 dương mạnh vẫn giữ, dù độ lớn Pearson PM25 năm 2024 nhạy đáng chú ý. Dataset chính và kết quả chính vẫn giữ AQI=500.

**Bước 6.8 — Khả năng AQI phụ thuộc chất ô nhiễm.** Kiểm tra bằng nhau về mặt số học trên các cặp hợp lệ: AQI bằng PM25 ở 791/30.333 giờ (2,608%), PM10 119/30.333 (0,392%); các chất còn lại 12–62 giờ (0,040–0,205%). Cùng với Spearman PM25 rất cao và các dải trên hexbin, đây là dấu hiệu cần xét khả năng AQI được xây dựng từ chỉ số chất ô nhiễm. Không có tài liệu nguồn xác nhận đơn vị hoặc công thức; số bằng nhau và tương quan cao không chứng minh công thức hoặc lỗi dữ liệu.

**Bước 6.9 — Trả lời Câu hỏi 3.** PM25/PM10 có liên hệ dương mạnh nhất với AQI trong dữ liệu; thứ hạng hai chất đổi theo Pearson/Spearman. O3 yếu nhất theo trị tuyệt đối ở cả hai. NO2/SO2 dương yếu trên toàn kỳ; CO rất nhỏ và chiều phụ thuộc phương pháp. PM25/PM10 ổn định dương mạnh qua các năm, các chất còn lại thay đổi nhiều hơn. Cực trị AQI=500 ảnh hưởng Pearson PM25 năm 2024 nhưng không đổi nhận định chính. Đây là tương quan mô tả trên dữ liệu giờ; AQI có thể phụ thuộc về mặt xây dựng chỉ số vào chất ô nhiễm, và không thể suy luận chất nào gây ô nhiễm từ bảng này. Hạn chế thêm: đơn vị chất ô nhiễm chưa xác minh, phân phối lệch/cực trị và 2025 chỉ sáu tháng.

**Quyết định tại cuối Giai đoạn 6:** Giữ nguyên dữ liệu đã làm sạch và mọi cực trị; chỉ dùng bản lọc tạm cho độ nhạy. Người dùng đã xác nhận kết quả này trước khi triển khai Giai đoạn 7.

**Kiểm tra cuối Giai đoạn 6:** Notebook hợp lệ với 171 cell, gồm 55 Code cell và 9 bước 6.1–6.9. Mỗi Code cell có Markdown giới thiệu và kết luận ngay trước/sau; không có output lỗi. Đã chạy lại toàn bộ notebook từ đầu thành công. SHA-256 dữ liệu đã làm sạch trước/sau lần chạy là `aff2960ae7e4e79e557eacce7846726662d9301a1aff28b234ca5ab1f65c6fb7`; SHA-256 bảng kiểm toán là `ca243577951e2ed0e5b02d03568249d85f299f70bad25f68486effa9d110c41e`. Cả hai không đổi.

### Giai đoạn 7 — Câu hỏi 4: AQI và yếu tố thời tiết

**Mục tiêu và dữ liệu:** Khảo sát liên hệ thống kê giữa AQI và Temperature, Relative Humidity, Precipitation, Wind Speed; đối chiếu thêm Pressure vì hệ số khám phá đáng chú ý. Dùng dữ liệu đã làm sạch và `Local Time`. Chia nhóm theo số liệu thực tế, tính từng cặp hợp lệ, không điền thiếu hoặc chỉnh sửa dữ liệu. Quy ước mùa giữ như Câu hỏi 2 (xuân 3–5, hè 6–8, thu 9–11, đông 12–2; nhóm theo năm dương lịch, không phải một mùa đông liền mạch).

**Bước 7.1 — Đầu vào.** Bốn biến trọng tâm và Pressure đều là `float64`, có 30.336 giá trị; AQI có 30.333 giá trị nên mỗi cặp AQI–thời tiết có 30.333 giờ hợp lệ. Phạm vi: Temperature 7–40, Relative Humidity 17–100, Precipitation 0–28, Wind Speed 0–14,9, Pressure 983–1.033. Không gán đơn vị chưa xác minh. Precipitation có trung vị 0, phù hợp phát hiện EDA trước đó.

**Bước 7.2 — Pearson và Spearman.** Pearson đo liên hệ tuyến tính trên giá trị gốc, Spearman đo liên hệ đơn điệu theo thứ hạng. Dùng `Series.corr()` riêng trên mỗi cặp hợp lệ; quy ước mô tả |r|≥0,7 mạnh, 0,3–<0,7 vừa, <0,3 yếu:

| Biến | Cặp hợp lệ | Pearson | Spearman |
|---|---:|---:|---:|
| Temperature | 30.333 | −0,3014 | −0,3349 |
| Relative Humidity | 30.333 | −0,0437 | −0,0735 |
| Precipitation | 30.333 | −0,1201 | −0,1564 |
| Wind Speed | 30.333 | −0,1520 | −0,1558 |
| Pressure (bổ sung) | 30.333 | +0,3327 | +0,3736 |

Trong bốn biến trọng tâm, Temperature có |r| lớn nhất; Pressure bổ sung có |r| lớn nhất toàn bảng. Hai phương pháp cùng dấu trên dữ liệu gộp, chênh cao nhất là Pressure +0,0409. Hệ số không chứng minh tác động nhân quả.

**Bước 7.3 — Biểu đồ.** Heatmap Pearson/Spearman tại `output/figures/q4_weather_correlation_heatmap.png`; hexbin cho Temperature và Relative Humidity tại `output/figures/q4_temperature_humidity_hexbins.png`, nhúng ở Notebook 1 bước 7.3. Hexbin dùng đủ 30.333 cặp/biến với thang màu log. Temperature có mật độ AQI thường gặp thấp hơn ở phía nhiệt độ cao, nhưng AQI phân tán rộng tại cùng mức; Relative Humidity không có dải tuyến tính rõ. Không gán đơn vị chưa xác minh.

**Bước 7.4 — Giờ có/không ghi nhận mưa.** Tách đúng `Precipitation == 0` và `> 0` (không tự đặt ngưỡng). Tổng giờ không mưa là 25.365, trong đó 25.362 giờ AQI hợp lệ; có mưa 4.971 giờ hợp lệ. AQI không mưa: mean 127,135, median 115, std 57,659; có mưa: mean 106,701, median 94, std 54,879. Chênh có mưa trừ không mưa: −20,434 trung bình, −21 trung vị. Đây là khác biệt đồng thời theo giờ; chưa kiểm soát mùa hoặc yếu tố khác, nên không kết luận mưa làm AQI giảm.

**Bước 7.5 — Nhóm Wind Speed.** `qcut(q=4, duplicates='drop')` dùng các mốc phân vị 0; 1,0; 2,0; 2,1; 14,9. Số giờ các nhóm: ≤1,0 có 8.361; (1,0;2,0] có 14.330; (2,0;2,1] có **100**; >2,1 có 7.542. Nhiều giá trị trùng quanh 2,0 làm nhóm 100 giờ rất hẹp; không coi bốn nhóm cân bằng hoặc suy rộng nhóm hẹp. AQI mean/median nhóm ≤1,0 là 132,435/123, nhóm >2,1 là 111,157/100; nhóm (2,0;2,1] có mean 132,850, nên thứ tự không giảm đều. Boxplot tại `output/figures/q4_wind_groups.png`, nhúng ở bước 7.5; ẩn điểm ngoài hộp để đọc trung tâm, nhưng bảng tính trên tất cả giờ.

**Bước 7.6 — Nhóm Temperature và Relative Humidity.** `qcut` chia theo phân vị thực tế, mỗi nhóm có 7.480–7.627 giờ nhiệt độ hoặc 6.445–8.299 giờ độ ẩm. AQI mean theo bốn nhóm Temperature tăng dần: 149,834; 132,067; 109,037; 103,807 (ranh giới 20,9; 25,7; 28,9). Trung vị từ 151 xuống 95. AQI mean theo bốn nhóm độ ẩm: 125,130; 128,177; 123,875; 116,857 (ranh giới 68; 82; 91), không giảm đều. Những bảng này mô tả quan sát, chưa tách yếu tố mùa.

**Bước 7.7 — Theo năm và mùa.** Pearson Temperature âm từng năm 2022–2025 (−0,400 đến −0,202), Pressure dương (+0,234 đến +0,428), Precipitation âm (−0,137 đến −0,091), Wind Speed âm (−0,253 đến −0,055). Relative Humidity gần 0, riêng 2022 +0,011. Trong nhóm mùa 2022–2024, Temperature Pearson: xuân −0,043, hè +0,128, thu −0,295, đông −0,032; Pressure: +0,051, −0,060, +0,350, +0,065. Wind Speed âm ở cả bốn mùa, mạnh nhất đông −0,325; Precipitation âm xuân/hè/thu nhưng đông +0,027 gần 0. Mỗi mùa có 6.191–6.624 cặp hợp lệ; nhóm đông có thiếu ngày. Hệ số gộp khác biệt đáng kể với hệ số trong mùa, phù hợp khả năng cùng thay đổi theo mùa nhưng không xác định cơ chế. Năm 2025 chỉ có tháng 1–6.

**Bước 7.8 — Độ nhạy AQI=500.** Lọc tạm 59 giờ năm 2024, không thay dữ liệu chính. Pearson Pressure +0,2598→+0,3107 (Δ+0,0509), Temperature −0,2023→−0,2451 (Δ−0,0428), Wind Speed −0,0547→−0,0822 (Δ−0,0275); các biến còn lại đổi ≤0,0154. Spearman mọi biến đổi ≤0,0076, không đổi dấu. Theo ngưỡng mô tả |r|=0,3, riêng Pressure 2024 chuyển từ yếu sang vừa trong phép lọc tạm, nên nhãn cường độ của năm đó nhạy với cực trị.

**Bước 7.9 — Trả lời Câu hỏi 4.** Trên dữ liệu gộp, Temperature là yếu tố trọng tâm liên hệ âm đáng chú ý nhất, Pressure bổ sung liên hệ dương lớn nhất; mưa và gió liên hệ âm yếu, độ ẩm gần 0. Giờ có mưa thấp hơn giờ không mưa 20,434 điểm AQI trung bình; nhóm gió cao >2,1 thấp hơn nhóm ≤1,0 khoảng 21,278 điểm, nhưng nhóm gió không cho thứ tự đều. Theo năm, dấu Temperature và Pressure khá ổn định; theo mùa, hệ số Temperature/Pressure gần 0 hoặc đổi dấu ở một số mùa. Vì vậy không diễn giải hệ số gộp thành tác động thời tiết. Hạn chế: nhóm mùa theo lịch, độ bao phủ không hoàn toàn đều, nhóm gió hẹp 100 giờ, 2025 nửa năm, đơn vị chưa xác minh và chưa kiểm soát yếu tố gây nhiễu.

**Quyết định:** Giữ nguyên dữ liệu đã làm sạch và AQI=500; mọi lọc độ nhạy chỉ trong bộ nhớ. Chờ người dùng xác nhận trước khi viết phần tổng kết Notebook 1.

**Kiểm tra cuối Giai đoạn 7:** Notebook hợp lệ với 199 cell, gồm 64 Code cell và 9 bước 7.1–7.9. Mỗi Code cell có Markdown giới thiệu và kết luận ngay trước/sau; không có output lỗi. Chạy lại toàn bộ notebook từ đầu thành công. SHA-256 dữ liệu đã làm sạch trước/sau là `aff2960ae7e4e79e557eacce7846726662d9301a1aff28b234ca5ab1f65c6fb7`; SHA-256 bảng kiểm toán là `ca243577951e2ed0e5b02d03568249d85f299f70bad25f68486effa9d110c41e`. Cả hai không đổi.

### Giai đoạn 8 — Tổng kết và hoàn thiện Notebook 1

**Mục tiêu:** Gom kết quả bốn câu hỏi đã được xác nhận thành phần Markdown cuối notebook, lập bảng tóm tắt cho báo cáo, kiểm tra toàn bộ cấu trúc và tính tái lập, và xác nhận file bàn giao cho Notebook 2. Không thêm câu hỏi, không đổi phương pháp làm sạch hay dữ liệu.

**Tổng hợp bốn câu hỏi:** Câu hỏi 1: trong 13/01–30/06, AQI trung bình 2022–2025 là 125,828; 121,880; 119,832; 138,409; trong 01/07–30/12 của 2022–2024 là 112,746; 121,126; 118,775. Biến động 2022→2023 khác chiều giữa hai nửa; 59 giờ AQI=500 nâng trung bình nửa đầu 2024 thêm 5,6117 điểm nhưng không đổi thứ tự năm. Câu hỏi 2: tháng 7 và hè thấp nhất ở cả 2022–2024, Spearman thứ hạng tháng giữa từng cặp năm 0,692–0,888; tháng/mùa cao nhất 2024 nhạy với AQI=500. Câu hỏi 3: PM10 Pearson 0,8195 và PM25 Spearman 0,9850 là hai liên hệ dương mạnh nhất theo từng phương pháp; không suy ra công thức AQI hoặc nguyên nhân. Câu hỏi 4: Temperature Pearson −0,3014, Pressure bổ sung +0,3327; giờ có mưa thấp hơn giờ không mưa 20,434 điểm AQI trung bình, nhưng hệ số Temperature/Pressure gộp khác hệ số theo mùa, không suy ra tác động nhân quả. Các bảng và kết luận chi tiết giữ tại bước 4.1–7.9 của notebook; phần tổng kết chỉ dẫn lại số đã tính.

**Bước 8.1 — Bàn giao dữ liệu.** Đọc lại `output/data/aqi_hanoi_cleaned.csv` với `parse_dates` cho Local Time/UTC Time; dùng `date_range().difference()` để tìm giờ không có bản ghi và `groupby()` đếm AQI hợp lệ mỗi ngày. Bốn file gốc có 8.472, 8.736, 8.788, 4.345 dòng; tổng 30.341. File sạch có **30.336 dòng × 19 cột**, 30.336 Local Time duy nhất, 0 giờ trùng, **315 ô thiếu**, trong đó AQI thiếu **3**. Phạm vi 13/01/2022 00:00–30/06/2025 23:00. Trong phạm vi này, chỉ thiếu 24 giờ của **31/12/2023**; hai ngày có bản ghi nhưng AQI <24 giờ là **01/07/2022 (22/24)** và **01/12/2024 (23/24)**. Hai cột thời gian đọc lại là datetime (UTC Time có timezone UTC); chỉ số số đọc là `float64`, định danh là chuỗi. Không có dòng thiếu toàn bộ. Vị trí: Notebook 1, bước 8.1.

**Quyết định bàn giao tại cuối Giai đoạn 8:** Notebook 2 cần tạo AQI trung bình ngày từ giờ AQI hợp lệ và công bố quy tắc ngày đủ giờ trước khi học. Không lấp ngày 31/12/2023 hay các giờ NaN; bảo đảm các ngày trong cửa sổ quá khứ và ngày mục tiêu liên tiếp theo lịch. Đặc trưng phải có sẵn tại thời điểm dự đoán, chỉ dùng AQI quá khứ theo phương án đã xác nhận; chia train/validation/test theo thứ tự thời gian, fit các bước học tham số trên train, không dùng thời tiết/chất ô nhiễm tương lai chưa biết. Tại thời điểm bàn giao này, Notebook 2 chưa được tạo hoặc huấn luyện; Giai đoạn 9–11 đã thực hiện các bước đó.

**Kiểm tra chất lượng và tái lập:** `nbformat.validate()` thành công. Notebook có **203 cell, 65 Code cell**; mỗi Code cell có Markdown ngay trước/sau, output đã lưu không có lỗi và không có Code cell trùng nguyên văn. Có **16 hình PNG** được code tham chiếu; tất cả tồn tại, dung lượng khác 0 và mở được. Cả hai CSV đầu ra có đúng cấu trúc mong đợi: dữ liệu sạch 30.336×19; bảng kiểm toán 13 hàng thuộc 5 mốc giờ. Chạy lại toàn bộ Code cell thành công khi thư mục làm việc là gốc project **và** `notebooks/`. SHA-256 trước/sau của dữ liệu sạch là `aff2960ae7e4e79e557eacce7846726662d9301a1aff28b234ca5ab1f65c6fb7`; bảng kiểm toán là `ca243577951e2ed0e5b02d03568249d85f299f70bad25f68486effa9d110c41e`. Hash của bốn CSV gốc và notebook mẫu cũng không đổi trong kiểm tra. Các hình được xem lại ở từng giai đoạn; tiêu đề, nhãn, chú thích và phạm vi được ghi ở code/hình tương ứng. Ghi chú giới hạn trong hình gió: nhóm (2,0;2,1] chỉ 100 giờ và boxplot ẩn điểm ngoài hộp để dễ đọc; bảng vẫn tính trên toàn bộ quan sát.

**Tài liệu:** `README.md` đã cập nhật cấu trúc project, thư viện và lệnh cài, cách chọn interpreter/kernel và Run All, vai trò file đầu ra, trạng thái Notebook 2. Phần Markdown cuối Notebook 1 có bốn kết luận, bảng tóm tắt, quyết định làm sạch và lưu ý bàn giao. Giai đoạn 8 đã được người dùng xác nhận trước khi bắt đầu Giai đoạn 9.

## 9. Hệ thống dự đoán

**Giai đoạn 9–11 đã thực hiện.** Bài toán là dự đoán AQI trung bình ngày tiếp theo từ AQI trung bình của 7 ngày lịch liền trước. Dữ liệu được chuẩn bị, baseline/Ridge được đánh giá và hàm dự đoán được triển khai trong `notebooks/02_AQI_Prediction.ipynb`. Ridge alpha 0,1 được chọn bằng Validation và đánh giá Test theo thứ tự thời gian; hàm chỉ sử dụng Pipeline đã lưu.

### Giai đoạn 9 — Chuẩn bị dữ liệu dự đoán

**Mục tiêu và đầu vào Giai đoạn 9:** Mỗi mẫu dùng AQI trung bình của đúng bảy ngày lịch liền trước (`AQI_lag_1`…`AQI_lag_7`) để dự đoán `AQI_target` của ngày đích. Đầu vào duy nhất là `output/data/aqi_hanoi_cleaned.csv`; không dùng thời tiết/chất ô nhiễm tương lai. Tại cuối Giai đoạn 9, Notebook 2 mới chứa bước chuẩn bị dữ liệu; huấn luyện và đánh giá được bổ sung ở Giai đoạn 10.

**Bước 9.1 — Đọc và kiểm tra.** `pd.read_csv(..., parse_dates=['Local Time','UTC Time'])` cho 30.336 dòng × 19 cột, 3 ô AQI thiếu, 30.336 Local Time duy nhất, 0 giờ trùng. Phạm vi 13/01/2022 00:00–30/06/2025 23:00. `date_range().difference()` phát hiện đúng 24 giờ không có bản ghi của 31/12/2023. File sạch được đọc, không sửa.

**Bước 9.2 — AQI ngày.** Nhóm theo ngày địa phương; đếm `recorded_hours` là số thời điểm duy nhất và `valid_aqi_hours` là số AQI không thiếu, tính trung bình của các giờ hợp lệ. Reindex theo lịch ngày đầy đủ 1.265 ngày. Quy tắc **hợp lệ khi có 24 mốc giờ duy nhất và 24 AQI hợp lệ** cho 1.262 ngày hợp lệ, 3 ngày không hợp lệ: 01/07/2022 (24 mốc, 22 AQI), 31/12/2023 (0 mốc, 0 AQI), 01/12/2024 (24 mốc, 23 AQI). Hai trung bình quan sát từ 22/23 giờ không được dùng làm `AQI_daily`; cột này là NaN cho mọi ngày không hợp lệ. Không điền, nội suy hoặc tự tạo ngày.

**Bước 9.3 — Bảy lag và gián đoạn.** Tạo `AQI_lag_k = AQI_daily.shift(k)` trên **lịch ngày đầy đủ**, sau đó chỉ giữ ngày đích và cả bảy lag không thiếu. Trong 1.265 ngày ứng viên có **1.234 mẫu hợp lệ, 31 ngày đích bị loại**: bảy ngày đầu 13–19/01/2022 chưa có đủ lịch sử; mỗi ngày không hợp lệ trong ba ngày trên làm loại chính ngày đó và bảy ngày kế tiếp (24 ngày). Mẫu đầu 20/01/2022, mẫu cuối 30/06/2025. Bảng trong notebook nêu từng ngày bị loại và lý do ngắn gọn.

**Bước 9.4 — Đối chiếu ngày cụ thể.** Hiển thị ba mẫu đầu/cuối, kiểm tra lag1 và lag7 bằng ngày lịch. Mẫu 20/01/2022 dùng lag1 19/01 và lag7 13/01; các mẫu đầu tiên sau gián đoạn lần lượt là 09/07/2022, 08/01/2024, 09/12/2024. Các assert đối chiếu lag và mục tiêu với bảng ngày gốc đều qua, không nối qua ngày thiếu.

**Bước 9.5 — Kiểm tra tính hợp lệ và leakage.** Bộ mẫu có 1.234 ngày đích duy nhất, thứ tự thời gian tăng, đúng bảy cột đầu vào và một cột mục tiêu, **0 ô thiếu**. Với mọi mẫu và k=1…7, `AQI_lag_k` bằng AQI ngày đích trừ k ngày; `AQI_target` bằng AQI ngày đích. Cột mục tiêu không thuộc danh sách đặc trưng. Không có thời tiết/chất ô nhiễm hoặc ngày tương lai trong đầu vào.

**Bước 9.6 — Thiết kế chia tập, chưa huấn luyện.** Theo `target_date`: train 702 mẫu 20/01/2022–30/12/2023 (**56,89%**), validation 351 mẫu 08/01–31/12/2024 (**28,44%**), test 181 mẫu 01/01–30/06/2025 (**14,67%**). Validation bắt đầu 08/01 vì 31/12/2023 thiếu; lag tại ranh giới được phép dùng AQI quá khứ đã quan sát, nhưng không dùng mục tiêu validation/test để fit hoặc chọn mô hình. Test 2025 chỉ đại diện sáu tháng đầu năm. Không chia ngẫu nhiên; các bước học tham số ở giai đoạn sau phải fit trên train, dùng validation để so mô hình, giữ test cho đánh giá cuối.

**Bước 9.7 — File trung gian.** Lưu `output/data/aqi_next_day_lag7.csv` có **1.234 hàng × 9 cột** gồm `target_date`, bảy lag và `AQI_target`; 0 ô thiếu, ngày đích tăng và duy nhất. Đọc lại file khớp toàn bộ cột, ngày và giá trị trong bộ nhớ. File đã làm sạch không bị ghi đè; SHA-256 vẫn là `aff2960ae7e4e79e557eacce7846726662d9301a1aff28b234ca5ab1f65c6fb7`. SHA-256 file lag7 là `2e5edff6e4223d77edbccbd3ee0395e7540f7a0bf9b6f83f97142fd1e59cff21`.

**Kiểm tra cuối Giai đoạn 9:** `nbformat.validate()` thành công; tại thời điểm này Notebook 2 có **22 cell, 7 Code cell**, mỗi Code cell có Markdown ngay trước/sau và 0 output lỗi. Đã chạy lại toàn bộ từ gốc project và thư mục `notebooks/`. Bốn CSV gốc, notebook mẫu, Notebook 1, file đã làm sạch và bảng kiểm toán có hash không đổi qua hai lần chạy. Giai đoạn 9 chưa có code fit mô hình hoặc tính chỉ số đánh giá; người dùng đã xác nhận trước khi bắt đầu Giai đoạn 10.

### Giai đoạn 10 — Baseline, Ridge và đánh giá cuối

**Mục tiêu và nguyên tắc:** Dự đoán AQI trung bình ngày đích từ đúng bảy AQI ngày trước trong `output/data/aqi_next_day_lag7.csv`. So sánh Persistence = `AQI_lag_1`, Weekly = `AQI_lag_7` và Ridge. Đặt trước quy tắc chọn theo **MAE Validation thấp nhất**, RMSE chỉ phân xử nếu MAE bằng nhau. Bốn alpha Ridge thử: 0,1; 1; 10; 100. Test chỉ dùng sau khi chọn phương pháp và alpha; không thay đổi lựa chọn theo Test. Đây là dự báo cuốn chiếu **một ngày**, dùng AQI thực tế đã có trong bảy ngày trước mỗi ngày đích.

**Bước 10.1 — Đầu vào và phân chia.** Đọc lại 1.234 mẫu × 8 cột số (bảy lag, một mục tiêu; `target_date` là index), toàn bộ `float64`, 0 NaN, ngày đích tăng và duy nhất. Train 702 mẫu 20/01/2022–30/12/2023; Validation 351 mẫu 08/01–31/12/2024; Test 181 mẫu 01/01–30/06/2025. `AQI_target` không nằm trong X.

**Bước 10.2 — Baseline Validation.** Hai công thức không fit tham số. Persistence: MAE **24,8181**, RMSE **38,7292**; Weekly: MAE **41,2527**, RMSE **58,3719** trên cùng 351 mẫu. Persistence tốt hơn Weekly 16,4346 MAE và 19,6427 RMSE.

**Bước 10.3 — Ridge và alpha.** Với mỗi alpha, fit `Pipeline(StandardScaler(), Ridge(alpha=...))` chỉ trên 702 Train rồi dự đoán Validation; scaler không học từ Validation/Test. Kết quả:

| Alpha | MAE Validation | RMSE Validation |
|---:|---:|---:|
| 0,1 | **23,3883** | **34,8658** |
| 1 | 23,3978 | 34,8746 |
| 10 | 23,4925 | 34,9690 |
| 100 | 24,4033 | 35,9880 |

Theo MAE rồi RMSE, chọn **alpha 0,1**.

**Bước 10.4 — Chọn phương pháp trên Validation.** Bảng so sánh cùng 351 mẫu:

| Phương pháp | MAE | RMSE | MAE − Persistence | RMSE − Persistence |
|---|---:|---:|---:|---:|
| Ridge alpha 0,1 | **23,3883** | **34,8658** | −1,4299 | −3,8634 |
| Persistence | 24,8181 | 38,7292 | 0 | 0 |
| Weekly | 41,2527 | 58,3719 | +16,4346 | +19,6427 |

**Quyết định trước Test:** Ridge alpha 0,1. Không mặc định Ridge tốt hơn baseline; quyết định đến từ bảng Validation.

**Bước 10.5 — Test.** Fit lại **cả StandardScaler và Ridge** với alpha đã cố định trên Train + Validation (1.053 mẫu), không dùng Test để fit. Đánh giá 181 mẫu Test; hai baseline được tính thêm để đối chiếu, không chọn lại:

| Phương pháp | MAE Test | RMSE Test | MAE − Persistence | RMSE − Persistence |
|---|---:|---:|---:|---:|
| Ridge alpha 0,1 | **22,3992** | **29,6577** | −2,3091 | −2,3915 |
| Persistence | 24,7083 | 32,0491 | 0 | 0 |
| Weekly | 37,3027 | 49,1494 | +12,5944 | +17,1002 |

Ridge thấp hơn hai baseline trên Test này, nhưng sai số không bằng 0 và Test chỉ có nửa đầu năm 2025.

**Bước 10.6 — Biểu đồ.** `output/figures/pred_test_first45.png` đối chiếu thực tế/dự đoán của **45 ngày Test đầu 01/01–14/02/2025** để đọc rõ đường; chỉ số ở trên vẫn tính toàn bộ 181 ngày. `output/figures/pred_test_error_hist.png` dùng cả 181 sai số dự đoán trừ thực tế. Dự đoán nhìn chung mượt hơn diễn biến AQI thực tế và bỏ lỡ một số đỉnh/đáy ngắn; biểu đồ không dùng để thay đổi mô hình.

**Bước 10.7 — Sai số.** Ridge có sai số có dấu trung bình **−2,417**, trung vị **−2,451**, sai số tuyệt đối lớn nhất **82,024**. Ngưỡng ngày AQI cao là phân vị 90% của **Train**, **179,046**. Trong 39 ngày Test vượt ngưỡng, MAE **30,419**, sai số trung bình **−30,236**; 142 ngày khác có MAE **20,197**, sai số trung bình **+5,224**. Năm ngày lỗi tuyệt đối lớn nhất: 07/02 (+82,024), 10/01 (+81,108), 29/04 (−79,314), 27/02 (−77,722), 21/06 (+76,557), đều thuộc 2025. Test không có ngày AQI trung bình bằng 500; max thực tế 233,333. Không suy đoán nguyên nhân hoặc tối ưu lại theo Test.

**Bước 10.8 — Mô hình cuối và tệp đầu ra.** Chọn Ridge alpha 0,1, fit lại Train + Validation. Lưu `output/model/aqi_next_day_ridge.joblib` (Pipeline gồm scaler và Ridge) và `output/model/aqi_next_day_model_metadata.json` (phương pháp, alpha, thứ tự bảy đặc trưng, số mẫu, chỉ số). Tại cuối Giai đoạn 10 chưa có chức năng người dùng nhập AQI; Giai đoạn 11 đã bổ sung hàm tái dùng đúng thứ tự đặc trưng và Pipeline này. Giới hạn: chỉ bảy lag AQI, ba ngày không hợp lệ làm gián đoạn cửa sổ, Test 2025 chỉ sáu tháng, sai số lớn hơn ở nhóm ngày AQI cao, chưa đánh giá các giai đoạn tương lai hoặc biến đổi ngoài dữ liệu này.

**Kiểm tra cuối Giai đoạn 10:** Tại thời điểm này, Notebook 2 hợp lệ với **47 cell, 15 Code cell**, mỗi Code cell có Markdown trước/sau và không có output lỗi. Đã chạy lại toàn bộ từ đầu; scikit-learn được import và fit thành công trong `.venv`. Một lần import đầu tiên gặp lỗi Windows Application Control chặn DLL, nhưng kiểm tra trực tiếp và trong kernel sau đó đều thành công; lần chạy hoàn chỉnh không lặp lại lỗi. Script xác minh không dùng `X_test`/`y_test` trong các cell chọn alpha/phương pháp, và Pipeline lưu có `StandardScaler.n_samples_seen_ = 1.053`, `Ridge.alpha = 0,1`. Tải artifact và tính lại Test cho MAE **22,3992**, RMSE **29,6577**, khớp metadata. Hash của bốn CSV gốc, notebook mẫu, Notebook 1, dữ liệu sạch, file lag7 và bảng kiểm toán không đổi sau chạy lại. Người dùng đã xác nhận Ridge alpha 0,1 trước Giai đoạn 11.

### Giai đoạn 11 — Chức năng dự đoán và tổng kết Notebook 2

**Mục tiêu:** Dùng đúng Pipeline Ridge alpha 0,1 đã fit lại trên Train + Validation để dự đoán AQI trung bình ngày kế tiếp từ bảy AQI trung bình ngày thực, nhập theo thứ tự cũ nhất→mới nhất. Hàm chạy trong Notebook 2, không fit lại, không cần giao diện web.

**Bước 11.1 — Tải mô hình có kiểm tra.** Xác nhận `output/model/aqi_next_day_ridge.joblib` và metadata JSON nằm trong thư mục model cố định của project; chỉ gọi `joblib.load()` cho artifact này. Metadata ghi Ridge alpha 0,1, đặc trưng `AQI_lag_1`…`AQI_lag_7`, Test MAE 22,399229/RMSE 29,65768. Artifact là `Pipeline` gồm `StandardScaler` và `Ridge`, có đúng bảy tên đặc trưng, scaler đã fit 1.053 mẫu; thử dự đoán một hàng cho số 164,687124. Khoảng đặc trưng và mục tiêu trong dữ liệu fit Train+Validation đều **42,875–500,0**. Không tải joblib do người dùng cung cấp hoặc nguồn không rõ.

**Bước 11.2 — Hàm nhập dữ liệu.** `predict_next_day_aqi(last_date, aqi_history)` yêu cầu `last_date` là chuỗi ISO `YYYY-MM-DD` và đúng bảy số thực hữu hạn không âm theo thứ tự cũ→mới. DataFrame dự đoán được dựng theo metadata: `AQI_lag_1 = aqi_history[-1]`, …, `AQI_lag_7 = aqi_history[0]`. Hàm chỉ gọi `loaded_pipeline.predict()`; trả dict chứa ngày cuối, ngày đích, bảy ngày/AQI đầu vào, dự đoán dạng `float` đầy đủ, tên Ridge/alpha, Test MAE/RMSE và cảnh báo. Không làm tròn đầu vào hoặc cắt dự đoán. Giá trị không âm ngoài phạm vi đặc trưng đã thấy khi fit được **cảnh báo ngoại suy**, vì chưa có tài liệu xác nhận ngưỡng AQI tối đa của nguồn. Nếu dự đoán ngoài phạm vi mục tiêu đã quan sát khi fit, hàm cảnh báo và giữ nguyên số.

**Giới hạn xác minh ngày liên tiếp:** Với chỉ một ngày cuối và bảy số, hàm gán nhãn bảy ngày lịch liên tiếp nhưng không thể chứng minh người nhập đã lấy AQI thực từ đúng các ngày đó. Kết quả luôn nhắc người dùng tự bảo đảm tính liên tiếp; không giả vờ đã kiểm chứng nguồn của bảy số.

**Bước 11.3 — Mẫu Test thật và tính nhất quán.** Dùng mẫu có ngày đích **01/01/2025**, lịch sử 25–31/12/2024. Hàm trả **164,68712363740414**, trùng dự đoán gọi trực tiếp từ Pipeline đã lưu với chênh **0**; ngày đích và thứ tự bảy ngày đúng. `AQI_target` của ngày đích không được dùng trong hàm.

**Bước 11.4 — Xử lý lỗi.** Bảy trường hợp đầu vào sai đều cho `ValueError` rõ ràng: chỉ sáu giá trị, AQI âm, NaN, Infinity, chuỗi không phải số, ngày không tồn tại và ngày sai định dạng. Không âm thầm thay giá trị. Bảy giá trị 501 (ngoài khoảng 42,875–500 đã fit) được nhận nhưng có cảnh báo ngoại suy; không tuyên bố 501 là sai khi chưa xác minh thang đo nguồn. Không dùng các kiểm thử này để điều chỉnh mô hình.

**Bước 11.5 — Demo dữ liệu thực.** AQI trung bình của 24–30/06/2025 lần lượt khoảng **126,917; 98,250; 100,417; 93,083; 85,958; 111,167; 88,792**. Với ngày cuối **30/06/2025**, hàm dự đoán AQI trung bình ngày **01/07/2025 = 89,57344319939507** (hiển thị **89,57**). Dataset kết thúc 30/06/2025, nên không có AQI thực 01/07 để xác nhận dự đoán demo. Kết quả in Test MAE/RMSE và các cảnh báo: Test chỉ 01–06/2025, dự đoán thấp ở nhóm AQI cao, không phải AQI giờ hoặc cảnh báo sức khỏe/môi trường chính thức.

**Tổng kết Notebook 2:** Phần Markdown cuối notebook tóm tắt 1.234 mẫu lag7, Train/Validation/Test 702/351/181, Persistence/Weekly/Ridge, Ridge alpha 0,1 chọn trên Validation (MAE 23,3883/RMSE 34,8658) và Test (22,3992/29,6577). Nhấn mạnh đánh giá cuốn chiếu một ngày, Test chỉ sáu tháng đầu 2025, sai số lớn hơn ở 39 ngày AQI cao (MAE 30,419; sai số có dấu −30,236), và không bảo đảm chính xác mọi ngày. Hướng phát triển chỉ là đánh giá thêm trên dữ liệu ngoài mẫu mới, không tự thay mô hình trong giai đoạn này.

**Kiểm tra cuối Giai đoạn 11:** Notebook 2 hợp lệ với **64 cell, 20 Code cell**, mỗi Code cell có Markdown trước/sau và không có output lỗi. Chạy lại toàn bộ từ gốc project và thư mục `notebooks/` thành công. Code của hàm không có `fit()`, chỉ dùng `predict()` của Pipeline đã lưu. Metadata và artifact vẫn là Ridge alpha 0,1 với scaler đã fit 1.053 mẫu. Hash của bốn CSV gốc, notebook mẫu, Notebook 1, dữ liệu sạch, file lag7 và bảng kiểm toán không đổi sau hai lần chạy lại. Giai đoạn 11 đã được người dùng xác nhận hoàn thành.

## 10. Tổng kết

Notebook 1 đã tổng kết cả bốn câu hỏi bằng số liệu và bảng ngắn phục vụ thuyết trình. Notebook 2 có 1.234 mẫu lag7 hợp lệ; Ridge alpha 0,1 được chọn trên Validation và đạt Test MAE 22,3992/RMSE 29,6577, thấp hơn hai baseline trong Test này. Hàm trong Notebook 2 nhận ngày cuối và bảy AQI trung bình ngày, kiểm tra dữ liệu và dự đoán ngày kế tiếp bằng Pipeline đã lưu; demo 01/07/2025 cho 89,57 nhưng chưa có giá trị thực để đối chiếu. Các giới hạn chung: độ bao phủ khác nhau, 2025 chỉ sáu tháng, 31/12/2023 không có bản ghi, NaN và cực trị chưa xác minh, thiếu thông tin đơn vị đo, nguồn trạm và công thức AQI. Dự đoán không phải cảnh báo chính thức. Cả hai notebook đã hoàn thành và được người dùng xác nhận.

## 11. Nhật ký triển khai

| Giai đoạn | Trạng thái | Kết quả chính | Vấn đề cần lưu ý |
|---|---|---|---|
| 1. Giới thiệu và Data Understanding | Hoàn thành | 13 bước đã chạy, kết luận từng cell và chạy lại từ đầu thành công; người dùng đã xác nhận | Các vấn đề dữ liệu chuyển sang Giai đoạn 2 |
| 2. Data Cleaning & Preprocessing | Hoàn thành | Bước 2.1–2.13 đã chạy; 30.336 giờ duy nhất, 315 ô thiếu; đã lưu dữ liệu và audit; người dùng đã xác nhận | Giữ NaN và cực trị chưa xác minh |
| 3. EDA tổng quan | Hoàn thành | Bước 3.1–3.6 đã chạy; có bảng thống kê và năm hình EDA; người dùng đã cho phép Giai đoạn 4 | Độ bao phủ không đều, cụm AQI=500 và cực trị chất ô nhiễm cần thận trọng |
| 4. Câu hỏi 1 | Hoàn thành, đã xác nhận | Bước 4.1–4.6 đã chạy; có bảng hai nửa năm, ba biểu đồ và bảng độ nhạy AQI=500 | 2025 không có nửa cuối; mức trung bình nửa đầu 2024 nhạy với 59 giờ AQI=500 |
| 5. Câu hỏi 2 | Hoàn thành, đã xác nhận | Bước 5.1–5.8 đã chạy; bảng theo tháng, mùa, thứ hạng, độ nhạy AQI=500 và ba biểu đồ | Tháng 01/2022 và tháng 12/2023 thiếu ngày; đỉnh 2024 nhạy với AQI=500 |
| 6. Câu hỏi 3 | Hoàn thành, đã xác nhận | Bước 6.1–6.9 đã chạy; hai phương pháp tương quan, bảng theo năm, độ nhạy 2024 và hai biểu đồ | Công thức AQI chưa xác minh; PM25 Pearson 2024 nhạy với AQI=500 |
| 7. Câu hỏi 4 | Hoàn thành, đã xác nhận | Bước 7.1–7.9 đã chạy; hệ số toàn kỳ/năm/mùa, các nhóm thời tiết, độ nhạy và ba biểu đồ | Hệ số gộp khác theo mùa; nhóm gió hẹp chỉ 100 giờ; thiếu xác minh đơn vị |
| 8. Tổng kết Notebook 1 | Hoàn thành, đã xác nhận | Bước 8.1 và phần Markdown tổng kết; kiểm tra 203 cell, 65 Code cell, 16 hình và chạy lại từ hai thư mục | Dữ liệu 2025 nửa năm; cần quy tắc ngày hợp lệ trước dự đoán |
| 9. Chuẩn bị dữ liệu dự đoán | Hoàn thành, đã xác nhận | Bước 9.1–9.7 đã chạy; 1.262 ngày hợp lệ, 1.234 mẫu lag7, chia train/validation/test theo ngày đích | Ba ngày không hợp lệ làm gián đoạn cửa sổ; test 2025 chỉ sáu tháng |
| 10. Xây dựng và đánh giá mô hình | Hoàn thành, đã xác nhận | Bước 10.1–10.8; Ridge alpha 0,1 chọn trên Validation, Test MAE 22,3992/RMSE 29,6577 | Ngày AQI cao sai số lớn hơn; Test chỉ 01–06/2025 |
| 11. Chức năng dự đoán và tổng kết Notebook 2 | Hoàn thành, đã xác nhận | Bước 11.1–11.5, demo 01/07/2025 = 89,57, kiểm thử đầu vào và phần Markdown tổng kết | Hàm không tự xác minh bảy ngày do người dùng nhập thực sự liên tiếp; dự đoán không phải cảnh báo chính thức |

**Quy tắc đồng bộ:** Sau mỗi Code cell chạy thành công, đọc output rồi mới viết kết luận trong notebook và cập nhật mục tương ứng ở đây. Nếu code, số liệu hoặc quyết định thay đổi, cập nhật cả hai nơi trước khi báo cáo hoàn thành giai đoạn.
