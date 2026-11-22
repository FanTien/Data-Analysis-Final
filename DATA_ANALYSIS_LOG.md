# Nhật ký phân tích dữ liệu AQI Hà Nội 2022–2025

> Tài liệu đồng hành với hai notebook của đồ án. Chỉ ghi kết quả đã kiểm chứng. Kết quả khảo sát CSV trước khi viết notebook được phân biệt rõ với output của notebook.

## 1. Tổng quan đồ án

**Chủ đề:** Phân tích chất lượng không khí (AQI) tại Hà Nội giai đoạn 2022–2025 và xây dựng hệ thống dự đoán AQI trung bình ngày tiếp theo.

**Mục tiêu:** Kiểm tra và tiền xử lý bốn CSV gốc; trả lời bốn câu hỏi nghiên cứu bằng thống kê và biểu đồ; xây dựng, đánh giá baseline và Ridge Regression cho bài toán dự đoán ngày tiếp theo. Có hai thành viên; tên thành viên chưa được cung cấp.

**Bốn câu hỏi đã được xác nhận:**

1. AQI tại Hà Nội thay đổi giữa các năm 2022–2025 như thế nào khi so sánh cùng khoảng thời gian?
2. AQI tại Hà Nội có xuất hiện mẫu hình theo tháng và mùa nhất quán qua các năm hay không?
3. Các chỉ số PM2.5, PM10, CO, NO2, O3 và SO2 có mối liên hệ như thế nào với AQI?
4. Nhiệt độ, độ ẩm, lượng mưa và tốc độ gió có mối liên hệ như thế nào với AQI?

**Phạm vi hiện tại:** Giai đoạn 1–2 đã được người dùng xác nhận. Giai đoạn 3 EDA tổng quan đã thực hiện các bước 3.1–3.6 và đang chờ xác nhận trước Câu hỏi nghiên cứu 1. Chưa trả lời bốn câu hỏi hoặc xây dựng hệ thống dự đoán.

## 2. Dataset

Bốn file gốc nằm trong `dataset/2022.csv` đến `dataset/2025.csv`. Notebook mẫu `dataanalysis.ipynb` ghi một đường dẫn nguồn Kaggle, nhưng nguồn trạm, đơn vị của các chỉ số và quy tắc tạo AQI chưa được xác minh từ chính các CSV. Không coi liên kết trong notebook mẫu là bằng chứng đầy đủ về phương pháp thu thập.

Mỗi file có 19 cột: `Local Time`, `UTC Time`, `City`, mã quốc gia, múi giờ, `AQI`, `CO`, `NO2`, `O3`, `PM10`, `PM25`, `SO2`, `Clouds`, `Precipitation`, `Pressure`, `Relative Humidity`, `Temperature`, `UV Index`, `Wind Speed`. `Local Time` là giờ địa phương; `UTC Time` là giờ UTC. `City`, mã quốc gia và múi giờ dùng để kiểm tra phạm vi. AQI là chỉ số chất lượng không khí; sáu cột CO–SO2 là chỉ số chất ô nhiễm; bảy cột còn lại là chỉ số thời tiết. Đơn vị đo của các chỉ số chưa được xác minh.

| File | Dòng × cột | Khoảng giờ địa phương | Ghi chú độ bao phủ |
|---|---:|---|---|
| `2022.csv` | 8.472 × 19 | 13/01/2022 00:00 – 31/12/2022 23:00 | Thiếu 01–12/01 |
| `2023.csv` | 8.736 × 19 | 01/01/2023 00:00 – 30/12/2023 23:00 | Thiếu toàn bộ 31/12 |
| `2024.csv` | 8.788 × 19 | 01/01/2024 00:00 – 31/12/2024 23:00 | Có 4 giờ bị ghi hai dòng |
| `2025.csv` | 4.345 × 19 | 01/01/2025 00:00 – 30/06/2025 23:00 | Chỉ có sáu tháng đầu năm; 1 giờ bị ghi hai dòng |

Tổng cộng 30.341 dòng và 30.336 mốc giờ duy nhất. Các số này đến từ khảo sát trực tiếp CSV trước khi triển khai notebook; cần đối chiếu lại với output Notebook 1 khi notebook chạy được.

## 3. Cấu trúc project

- `dataset/`: bốn CSV gốc, chỉ đọc; không ghi đè.
- `Slide/`: tài liệu môn học.
- `dataanalysis.ipynb`: notebook mẫu, chỉ tham khảo; không sửa.
- `notebooks/01_AQI_Data_Analysis.ipynb`: đã có Giới thiệu, Data Understanding, Data Cleaning & Preprocessing và EDA tổng quan đến bước 3.6. Bốn câu hỏi chưa triển khai.
- `notebooks/02_AQI_Prediction.ipynb`: dự kiến chứa phần dự đoán, chỉ bắt đầu sau khi Notebook 1 hoàn thành và được xác nhận. **Chưa tạo.**
- `output/data/aqi_hanoi_cleaned.csv`: dữ liệu 19 cột đã xử lý để dùng ở các giai đoạn sau.
- `output/tables/duplicate_conflicts_audit.csv`: bảng kiểm toán 13 ô bất đồng tại 5 giờ trùng, có số dòng nguồn và cả hai giá trị gốc.
- `output/figures/`: năm hình EDA được tạo ở các bước 3.2–3.6; `output/model/` chưa có sản phẩm.
- `README.md`: hướng dẫn chạy project. **Chưa thực hiện.**
- `DATA_ANALYSIS_LOG.md`: nhật ký này; cập nhật cùng notebook sau mỗi bước đã chạy và kiểm chứng.

## 4. Môi trường thực hiện

Môi trường đã dùng: `.venv/Scripts/python.exe`, Python 3.14.7; pandas 3.1.1, NumPy 2.5.5, Matplotlib 3.11.2, Seaborn 0.13.2, Jupyter 1.1.1, ipykernel 7.4.0, scikit-learn 1.9.1, nbformat 5.11.1 và nbclient 0.11.0. Một cell Jupyter thử nghiệm đã in `5`; 13 cell Notebook 1 cũng đã chạy và chạy lại từ đầu thành công bằng chính môi trường này. Cảnh báo của `zmq` về event loop/TCP xuất hiện khi khởi động kernel nhưng không gây lỗi Code cell.

**Cách chạy đã kiểm chứng:** Mở terminal tại thư mục project, chọn kernel Python của `.venv` trong Jupyter, rồi chạy `notebooks/01_AQI_Data_Analysis.ipynb` từ đầu theo thứ tự. Đường dẫn dữ liệu trong notebook hoạt động khi thư mục làm việc là gốc project hoặc `notebooks/`. Notebook 2 chưa được tạo; khi triển khai sẽ dùng cùng môi trường này.

## 5. Data Understanding

### Khảo sát CSV trước triển khai notebook

**Mục tiêu:** Xác định cấu trúc, độ bao phủ và các vấn đề cần kiểm tra lại trong Notebook 1. **Lý do:** Không chọn cách làm sạch trước khi biết dữ liệu thực tế. **Đầu vào:** Bốn CSV gốc, toàn bộ dòng và cột. **Phương pháp:** Đọc CSV ở chế độ chỉ đọc; đếm dòng/cột và giá trị trống; đối chiếu tên cột, giờ địa phương với UTC+7; kiểm tra khoảng cách giữa các giờ, dòng trùng hoàn toàn, mốc giờ trùng và phạm vi số. Đây là khảo sát bằng công cụ đọc CSV, **không phải Code cell hay output của Notebook 1**. **Vị trí notebook:** Chưa có.

**Kết quả và ảnh hưởng đến bước tiếp theo:**

| Phát hiện | Bằng chứng từ CSV | Ảnh hưởng, trạng thái quyết định |
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

Các câu hỏi đã xác nhận ở Mục 1. **Chưa thực hiện** phép tính, biểu đồ hoặc câu trả lời cho bất kỳ câu hỏi nào. Khi triển khai, ghi mục tiêu, dữ liệu, phương pháp, số quan sát, kết quả, vị trí biểu đồ và giới hạn diễn giải theo từng câu hỏi; không suy luận nhân quả từ tương quan.

## 9. Hệ thống dự đoán

**Chưa thực hiện.** Bài toán đã xác nhận là dự đoán AQI trung bình ngày tiếp theo từ 7 ngày AQI trước đó. Dự kiến so sánh baseline với Ridge Regression bằng MAE và RMSE trên các tập chia theo thời gian; lựa chọn cuối cùng dựa trên kết quả thực tế. Notebook 2 chỉ bắt đầu sau khi Notebook 1 hoàn thành và được người dùng xác nhận. Chưa tạo đặc trưng, chia tập, huấn luyện hay viết chức năng nhập dữ liệu.

## 10. Tổng kết

**Chưa thực hiện.** Chưa có kết luận nghiên cứu hoặc kết quả mô hình. Giới hạn đã biết trước triển khai: độ bao phủ các năm khác nhau, giờ thiếu và giờ xung đột, thiếu thông tin đơn vị đo và nguồn trạm.

## 11. Nhật ký triển khai

| Giai đoạn | Trạng thái | Kết quả chính | Vấn đề cần lưu ý |
|---|---|---|---|
| 1. Giới thiệu và Data Understanding | Hoàn thành | 13 bước đã chạy, kết luận từng cell và chạy lại từ đầu thành công; người dùng đã xác nhận | Các vấn đề dữ liệu chuyển sang Giai đoạn 2 |
| 2. Data Cleaning & Preprocessing | Hoàn thành | Bước 2.1–2.13 đã chạy; 30.336 giờ duy nhất, 315 ô thiếu; đã lưu dữ liệu và audit; người dùng đã xác nhận | Giữ NaN và cực trị chưa xác minh |
| 3. EDA tổng quan | Chờ xác nhận | Bước 3.1–3.6 đã chạy; có bảng thống kê và năm hình EDA | Độ bao phủ không đều, cụm AQI=500 và cực trị chất ô nhiễm cần thận trọng |
| 4. Câu hỏi 1 | Chưa thực hiện | — | — |
| 5. Câu hỏi 2 | Chưa thực hiện | — | — |
| 6. Câu hỏi 3 | Chưa thực hiện | — | — |
| 7. Câu hỏi 4 | Chưa thực hiện | — | — |
| 8. Tổng kết Notebook 1 | Chưa thực hiện | — | — |
| 9. Chuẩn bị dữ liệu dự đoán | Chưa thực hiện | — | Cần xác nhận Notebook 1 trước |
| 10. Xây dựng và đánh giá mô hình | Chưa thực hiện | — | — |
| 11. Chức năng dự đoán và tổng kết Notebook 2 | Chưa thực hiện | — | — |

**Quy tắc đồng bộ:** Sau mỗi Code cell chạy thành công, đọc output rồi mới viết kết luận trong notebook và cập nhật mục tương ứng ở đây. Nếu code, số liệu hoặc quyết định thay đổi, cập nhật cả hai nơi trước khi báo cáo hoàn thành giai đoạn.
