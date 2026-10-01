Phần 1: Phân tích IPO (Input - Process - Output)
  Input (Đầu vào):
    Mỗi đơn hàng thành công: 2 bức ảnh được tài xế chụp trực tiếp từ ứng dụng (Shipper App)
    .Dữ liệu kèm theo (Metadata): Mã đơn hàng (Order ID), mã tài xế (Shipper ID), thời gian chụp (Timestamp), tọa độ GPS vị trí giao hàng

    
  .Process (Xử lý):
    Kiểm tra kích thước: Ứng dụng kiểm tra dung lượng ảnh chụp. Nếu ảnh vượt quá giới hạn an toàn ($> 5\text{ MB}$), hệ thống tiến hành nén hoặc giảm độ phân giải trực tiếp trên thiết bị của tài xế
    .Truyền tải: Mã hóa dữ liệu và gửi gói tin (gồm ảnh và metadata) qua API lên Server
    .Lưu trữ: Server tiếp nhận, phân loại ảnh theo mã đơn hàng, lưu trữ tệp ảnh vào hệ thống Cloud Storage và cập nhật trạng thái đơn hàng thành công vào cơ sở dữ liệu (Database).

    
  Output (Đầu ra):
    Tệp ảnh POD được lưu trữ thành công trên server/cloud.
    Trạng thái đơn hàng được cập nhật thời gian thực (Real-time) trên hệ thống quản lý của FastShip.
    Thông báo thành công trả về cho ứng dụng tài xế để chuyển sang đơn tiếp theo.
    

Phần 2: Giải pháp xử lý "Bẫy dữ liệu" (Ảnh vượt quá 5 MB)
Để ngăn chặn tình trạng server báo đầy hoặc lỗi tải lên do ảnh chụp từ điện thoại đời mới có độ phân giải quá cao ($> 5\text{ MB}$), em đề xuất giải pháp Xử lý nén ảnh tự động ở phía Client (App tài xế) trước khi gửi:


1.Kiểm tra trước khi upload: Ngay khi tài xế bấm chụp, Shipper App sẽ kiểm tra dung lượng tệp ảnh.


2.Thuật toán nén thông minh: Nếu dung lượng tệp $> 5\text{ MB}$, ứng dụng sẽ tự động nén (giảm kích thước độ phân giải xuống mức chuẩn, ví dụ Full HD 1920x1080, hoặc tối ưu hóa chất lượng nén JPEG/WEBP) để đưa dung lượng về dưới mức $3\text{ MB}$ mà vẫn đảm bảo độ sắc nét để làm bằng chứng giao hàng.


3.Cơ chế dự phòng (Server-side Fallback): Server đặt một lớp kiểm tra bảo vệ (Middleware validation). Nếu phát hiện tệp gửi lên vượt ngưỡng $5\text{ MB}$, server sẽ tự động từ chối yêu cầu và trả mã lỗi để App tự động thực hiện nén lại, đảm bảo an toàn tuyệt đối cho kho chứa dữ liệu.


Phần 3: Tính toán dung lượng lưu trữ trong 1 tháng


  1. Số liệu cơ bản
    Số lượng đơn hàng mỗi ngày: $50,000$ đơn.
    Số lượng ảnh mỗi đơn: $2$ ảnh.
    Dung lượng trung bình mỗi ảnh: $3\text{ MB}$.


  2. Các bước tính toánTổng số ảnh chụp mỗi ngày:$$50,000 \times 2 = 100,000\text{ ảnh/ngày}$$Tổng dung lượng lưu trữ mỗi ngày:$$100,000 \times 3\text{ MB} = 300,000\text{ MB/ngày}$$Tổng dung lượng lưu trữ trong 1 tháng (30 ngày):$$300,000\text{ MB} \times 30 = 9,000,000\text{ MB}$$


  3.Quy đổi sang đơn vị Terabyte (TB)Quy đổi theo chuẩn nhị phân ($1\text{ GB} = 1024\text{ MB}$ và $1\text{ TB} = 1024\text{ GB}$):Đổi sang GB: $9,000,000\text{ MB} \div 1024 \approx 8,789.06\text{ GB}$Đổi sang TB: $8,789.06\text{ GB} \div 1024 \approx \mathbf{8.58\text{ TB}}$(Hoặc tính theo hệ thập phân tròn $1000$ thì bằng $9\text{ TB}$).

HỌ VÀ TÊN : BÙI MINH HOÀNG
LỚP : HCM-CNTT1
BÀI Thiết kế và tối ưu hệ thống lưu trữ ảnh bằng chứng giao hàng tại FastShip
