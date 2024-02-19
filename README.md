#  Marketing Strategy Analysis

# :briefcase: Business Case and Requirement
#  1.Phân tích về khách hàng và sự tương tác của họ với doanh nghiệp trong năm 2010. 
* Đánh Giá Tính Hiệu Quả của Chiến Lược Tiếp Thị và Chăm Sóc Khách Hàng: Phân tích cohort cho phép bạn đánh giá hiệu quả của các chiến lược tiếp thị và chăm sóc khách hàng bằng cách theo dõi lượng khách hàng theo thời gian từ lần mua đầu tiên. Bạn có thể đo lường tỷ lệ giữa khách hàng trung thành và khách hàng lẻ mua một lần, từ đó đánh giá hiệu quả của các chiến lược tiếp thị và chăm sóc khách hàng.

* Xác Định Tần Suất Mua Hàng của Khách Hàng: Phân tích cohort giúp xác định chu kỳ mua hàng của khách hàng trong suốt quãng thời gian họ tương tác với doanh nghiệp. Bằng cách này, bạn có thể hiểu rõ hơn về nhu cầu mua hàng của khách hàng và điều chỉnh chiến lược tiếp thị và cung cấp sản phẩm/dịch vụ phù hợp.

* Phát Hiện Xu Hướng và Thay Đổi trong Hành Vi Khách Hàng: Bằng cách theo dõi lượng khách hàng qua các cohort theo thời gian, bạn có thể phát hiện xu hướng và thay đổi trong hành vi của khách hàng. Điều này giúp bạn dự đoán và phản ứng đúng đắn đối với các thay đổi trong thị trường và nhu cầu của khách hàng.

* Tối Ưu Hóa Chiến Lược Giao Tiếp và Tương Tác: Dựa trên hiểu biết sâu sắc về hành vi của khách hàng qua các cohort, bạn có thể tối ưu hóa chiến lược giao tiếp và tương tác với khách hàng. Điều này bao gồm việc tạo ra các thông điệp và ưu đãi phù hợp với từng nhóm khách hàng cụ thể và lựa chọn kênh giao tiếp hiệu quả nhất.
#  .Phân tích về khách hàng và sự tương tác của họ với doanh nghiệp trong năm 2010. 
## Các bước làm việc với tập dữ liệu:
1. Đọc dữ liệu và loại bỏ các giá trị thiếu:
Sử dụng pd.read_excel để đọc dữ liệu từ tập tin "Online Retail.xlsx" vào DataFrame data.
Sử dụng data.dropna(subset=['CustomerID']) để loại bỏ các hàng không có CustomerID.
2. Tạo cột tháng hóa đơn:
Định nghĩa hàm get_month(x) để lấy ngày đầu tiên của tháng từ một đối tượng datetime x.
Sử dụng data['InvoiceMonth'] = data['InvoiceDate'].apply(get_month) để tạo cột InvoiceMonth chứa tháng và năm của hóa đơn.
3. Tạo cột tháng bắt đầu:
Sử dụng data['Cohort Month'] = data.groupby('CustomerID')['InvoiceMonth'].transform('min') để tạo cột Cohort Month chứa tháng đầu tiên (tháng xuất hiện đầu tiên) của mỗi khách hàng.
4. Trích xuất các phần tử ngày tháng:
Định nghĩa hàm get_date_elements(df, column) để trích xuất ngày, tháng, năm từ cột datetime column trong DataFrame df.
Sử dụng hàm này để trích xuất các phần tử ngày tháng từ InvoiceMonth và Cohort Month.
5. Tạo chỉ số nhóm:
Tính toán year_diff và month_diff để lấy số năm và tháng chênh lệch giữa ngày hóa đơn và ngày bắt đầu.
Tạo CohortIndex bằng cách kết hợp year_diff và month_diff.
6. Đếm khách hàng theo nhóm:
Mã này tạo ra một biến tạm thời cohort_data nhưng không sử dụng nó.
7. Tạo bảng xoay và biểu đồ nhiệt:
Sử dụng cohort_table = cohort_data.pivot(index='Cohort Month', columns=['CohortIndex'],values='CustomerID') để tạo bảng xoay với các hàng là tháng bắt đầu và các cột là chỉ số nhóm. Giá trị mỗi ô là số lượng khách hàng thuộc nhóm đó.
Sử dụng sns.heatmap để tạo biểu đồ nhiệt từ bảng xoay.
8. Tính phần trăm và tạo biểu đồ:
Sử dụng new_cohort_table = cohort_table.divide(cohort_table.iloc[:,0],axis=0) để tính tỷ lệ khách hàng trong mỗi chỉ số nhóm so với nhóm đầu tiên trong mỗi tháng bắt đầu.
Sử dụng sns.heatmap để tạo biểu đồ nhiệt hiển thị tỷ lệ phần trăm.
---
### Kết quả
Xu hướng thu hút khách hàng:
Dựa vào bảng dữ liệu, số lượng khách hàng mới (CohortIndex 1) có xu hướng giảm dần trong vài tháng đầu năm 2011 sau khi đạt đỉnh vào tháng 12/2010.
Sau tháng 5/2011, số lượng khách hàng mới có xu hướng tăng trở lại và ổn định ở mức trung bình khoảng 300 khách hàng mỗi tháng.
Xu hướng theo tháng: Có thể quan sát thấy một sự gia tăng nhẹ số lượng khách hàng mới vào các tháng cuối năm (tháng 10, 11, 12). Tuy nhiên, dữ liệu chỉ trong một năm nên cần thêm thời gian để xác định xu hướng theo tháng rõ ràng hơn.
Giữ chân khách hàng:
Dựa vào dữ liệu hiện có, ta có thể thấy một số nhóm có tỷ lệ giữ chân cao hơn trong vài tháng đầu tiên
Nhóm CohortIndex 1 (tháng 12/2010): 62% khách hàng vẫn còn mua hàng sau 3 tháng.
Nhóm CohortIndex 4 (tháng 4/2011): 58% khách hàng vẫn còn mua hàng sau 3 tháng.
So sánh nhóm:
Hành vi mua hàng:
Có thể so sánh số lượng giao dịch, doanh thu trung bình, sản phẩm mua thường xuyên của các nhóm khách hàng khác nhau.
Nhóm CohortIndex 1 có thể có số lượng giao dịch cao hơn nhưng doanh thu trung bình thấp hơn so với nhóm CohortIndex 4.
Nhóm CohortIndex 1 có thể có tỷ lệ khách hàng cao hơn so với nhóm CohortIndex 4.---



# 🧾 What can you practice with this case study?
- Python
  - pandas, numpy,matplotlib,seaborn.
  - cleaning, check Null values, transforming.
  - import, save csv file. 

