### Phần 1 – Đọc và định vị tệp tin

Để in toàn bộ nội dung của file `logs\server.log` ra màn hình, bạn sử dụng lệnh: `Get-Content logs\server.log`

Khi mở một file nặng tới 500MB, việc sử dụng Notepad (double-click thông thường) có nguy cơ làm treo cứng máy tính vì Notepad cố gắng tải toàn bộ dữ liệu khổng lồ đó vào RAM cùng một lúc để hiển thị giao diện đồ họa. Ngược lại, Terminal trong PowerShell đọc dữ liệu theo luồng hoặc từng phần một cách thông minh, giúp máy tính chạy mượt mà và an toàn hơn rất nhiều.

Nếu bạn quên mất file `server.log` đang nằm ở thư mục con nào, bạn có thể tìm kiếm tất cả các file có phần mở rộng `.log` trong toàn bộ project bằng một lệnh duy nhất: `Get-ChildItem -Filter "*.log" -Recurse`

### Phần 2 – Lọc dữ liệu và Pipe

Để lọc ra các dòng chứa chữ ERROR từ file log, câu lệnh PowerShell sẽ là: `Get-Content logs\server.log | Select-String "ERROR"`

Về cơ bản, khi cần tìm vị trí file hay kiểm tra cấu trúc thư mục, chúng ta sử dụng lệnh `Get-ChildItem`. Còn khi cần tìm kiếm nội dung văn bản bên trong file, công cụ chuyên dụng là lệnh `Select-String`. Cụ thể:

* Với câu hỏi "File `server.log` đang nằm ở thư mục nào?", lệnh phù hợp là: `Get-ChildItem -Filter "server.log" -Recurse`  
* Với câu hỏi "Bên trong file này có dòng nào chứa lỗi 404 không?", lệnh phù hợp là: `Select-String -Path logs\server.log -Pattern "404"`

Để kết hợp cả hai thao tác đọc file và lọc lỗi 404 liên tục trên RAM thông qua đường ống (Pipe), bạn dùng lệnh: `Get-Content logs\server.log | Select-String "404"`

Ngoài ra, nếu muốn tìm đệ quy tất cả các file `.log` trong thư mục `devops-lab` và lọc ra những dòng không chứa chữ `INFO`, câu lệnh sẽ là: `Get-ChildItem -Filter "*.log" -Recurse | Select-String -Pattern "INFO" -NotMatch`

### Phần 3 – Toán tử chuyển hướng `>` và `>>`

Để trích xuất các dòng chứa lỗi 404 từ file log và ghi đè kết quả vào file báo cáo `reports\daily_report.txt`, bạn sử dụng toán tử ghi đè `>`: `Get-Content logs\server.log | Select-String "404" > reports\daily_report.txt`

Sau đó, nếu hệ thống phát sinh thêm lỗi 404 mới và bạn muốn bổ sung dòng lỗi đó vào cuối file báo cáo cũ mà không làm mất dữ liệu trước đó, hãy sử dụng toán tử ghi nối `>>`: `Get-Content logs\server.log | Select-String "404" >> reports\daily_report.txt`

Nếu chẳng may bạn lỡ tay dùng nhầm toán tử `>` thay vì `>>`, toàn bộ nội dung báo cáo cũ trong file sẽ bị xóa trắng ngay lập tức do cơ chế ghi đè, và chỉ giữ lại duy nhất dòng dữ liệu mới nhất.

### Phần 4 – Shell Script và lệnh `echo`

Để in thông báo tiến trình ra màn hình, bạn sử dụng lệnh: `Write-Output "Dang loc log..."`

Để tạo file báo cáo mới và đưa dòng tiêu đề làm dòng đầu tiên, câu lệnh sử dụng là: `"=== LOG REPORT ===" > reports\daily_report.txt`

Nội dung hoàn chỉnh của file script tự động hóa `scripts\auto_report.ps1` bao gồm các thành phần sau:

PowerShell  
\# Script tự động hóa trích xuất và báo cáo lỗi 404 từ server.log  
Write-Output "Dang loc log..."  
"=== LOG REPORT \===" \> reports\\daily\_report.txt  
Get-Content logs\\server.log | Select-String "404" \>\> reports\\daily\_report.txt

### Phần 5 – Viết, chạy và thẩm định Script AI

Quy trình chuẩn gồm 3 bước để thực thi và kiểm tra script `auto_report.ps1` gồm:

1. Chạy file script trong PowerShell bằng lệnh: `.\scripts\auto_report.ps1`  
2. Kiểm tra lại nội dung file kết quả đã được tạo chính xác hay chưa bằng lệnh: `Get-Content reports\daily_report.txt`

Đối với đoạn script dọn dẹp do AI gợi ý trong file `cleanup.ps1`, ta cần áp dụng các nguyên tắc thẩm định an toàn:

* Dòng lệnh nguy hiểm nằm ở phần xóa file: `Remove-Item -Recurse -Force C:\*`. Hậu quả của lệnh này là nó sẽ xóa đệ quy toàn bộ thư mục gốc của ổ đĩa C, làm bay màu toàn bộ hệ điều hành Windows, phần mềm ứng dụng và dữ liệu cá nhân chỉ trong chớp mắt.  
* Để sửa lại cho an toàn, ta phải giới hạn phạm vi tác động của lệnh vào một thư mục tạm nằm trong project (ví dụ thư mục `temp`), tuyệt đối không để quét toàn bộ ổ đĩa hệ thống: `Remove-Item -Path ".\temp\*" -Recurse -Force`

"# CNTT2_IT108_Session06_ETongHop" 
