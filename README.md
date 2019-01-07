# Đồ án cuối kỳ Data Science - HCMUS - 2018/2019

Thành viên nhóm:
- Đặng Thành Phát 1512396
- Võ Thanh Phương 1512420

Đề tài: Dự đoán giá phòng khách sạn trên mytour.vn
Ý nghĩa: Khi thêm mới một phòng khách sạn vào hệ thống thì sẽ dự đoán được giá khách sạn đó, hoặc chủ khách sạn có thể chủ động đánh giá để giá khách sạn của mình phù hợp

Thực hiện: Trước hết nhóm thực hiện khách sạn trên một phạm vi cụ thể là ở Đà Lạt. Nếu có thể sẽ mở rộng cho toàn bộ những khách sạn trên mytour.vn

Crawl dữ liệu:
* source/get_hotel_infomation: lấy tất cả các khách sạn tại một địa điểm nào đó. Dữ liệu sẽ lấy những khách sạn có rating và lượng người đánh giá mức cụ thể nào đó để tăng tính đúng đắn của dữ liệu.
  - Dữ liệu data_hotel crawl được sẽ gồm các thuộc tính:
            
            + id : ID khách sạn            
            + name : Tên khách sạn            
            + url : Đường dẫn website của khách sạn đó trên mytour.vn            
            + rating : Rating khách sạn            
            + rate_number : Số lượng người đánh giá            
            + star : Số sao khách sạn            
            + lat, long (vị trí địa lý của khách sạn)
 
* source/get_room_infomation: dựa vào đường dẫn website thu được ở get_hotel_infomation. Tiến hành vào từng đường dẫn để thu thập. Giá thu thập tại mỗi phòng chỉ tính thời gian thuê là 01 ngày. Tên tập tin thu được sau khi crawl sẽ là {0}.csv với {0} là ngày hôm đó. Thu thập dữ liệu từ ngày hiện tại đến 7 ngày gần nhất.
  - Dữ kiệu data_room crawl được sẽ gồm các thuộc tính:
            
            + id : ID khách sạn            
            + checkin_day : Ngày check-in            
            + size : Kích thước phòng            
            + orientation : Hướng phòng (Hướng vườn, hướng phố, không cửa số, ...)            
            + bed : Thông tin về giường ngủ            
            + attribute_number : Số lượng tiện nghi            
            + max_guest_number : Số lượng khách tối đa có thể ở            
            + have_breakfast : Có bao gồm bửa sáng hay không            
            + cancel_ticket : Có thể hoàn hủy vé hay không            
            + price : Giá phòng
 
* Tập tin source/create_raw_data: Dữ liệu được thu thập theo ngày, mối ngày lưu vào một tập tin (.csv) có tên là ngày đó. Thực hiện nối các tập tin data_room lại và merge với tập tin data_hotel. Dữ liệu được lưu với tên raw_data.csv. Một số cột có giá trị thiếu như orientation và bed sẽ được điền những giá mới
 
* Hiện tại nhóm đã crawl được hơn 5000 dòng dữ liệu
 
Tiền xử lý:
- Sau khi đã crawl dữ liệu, nhóm tiến hành tiền xử lý.
- Tập tin source/preprocesscing_data tiến hành xử lý dữ liệu và train model
- Tạo thêm một số cột thuộc tính mới từ dữ liệu có sắn:
            
            + distance_airpot: Khoảng cách so với sân bay thành phố Đà Lạt            
            + distance_station: Khoảng cách so với bến xe thành phố Đà Lạt            
            + distance_center: Khoảng cách so với trung tâm thành phố Đà Lạt (bưu điện thành phố Đà Lạt)            
            + week_day : Thứ trong tuần            
            + is_holiday: Có gần kì nghĩ lể hay không, kì nghĩ lễ tính trong khoảng 3 ngày của kì nghĩ lễ. Ví dụ tết dương 1/1 thì 31/12, 1/1, 2/1 có is_holiday=1            
            + order_day: Thời gian đặt phòng so với ngày check_in
            
- Như vậy, một record sẽ bao gồm các thuộc tính
            
            + star            
            + attribute_number            
            + distance_airpot            
            + distance_station            
            + distance_center            
            + week_day            
            + is_holiday            
            + order_day            
            + size            
            + orientation            
            + bed            
            + max_guest_number            
            + have_breakfast            
            + cancel_ticket            
            + PRICE (Y)

- Một số thuộc tính dùng OneHotEncoder như orientation, bed và week_day
- Sau quá trình tiền xử lý, một vector dữ liệu sẽ có 32 chiều

- Train model
- Nhóm đã thử sử dụng mô hình Linear Regression nhưng kết quả đạt được không cao.
    + RMSE, R2 = 345066, 0.62 trên tập train
    + RMSE, R2 = 375213, 0.62 trên tập test
- Nhóm sử dụng model Neural Net (MLPRegressor với hàm activation là 'relu', hidden layer = (21,)) đạt được kết quả:
    + RMSE, R2 = 96263, 0.97 trên tập train
    + RMSE, R2 = 126027, 0.96 trên tập test
