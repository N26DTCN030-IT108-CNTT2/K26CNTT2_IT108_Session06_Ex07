\# Báo cáo Phân tích \& Gỡ lỗi: Debug Shell Script Xử lý Log CRITICAL



\## Phần 1: Phân tích Lỗi

\* \*\*Nguyên nhân gốc rễ:\*\* 

&#x20; 1. Trong script ban đầu, câu lệnh `Select-String` có sử dụng tham số `-CaseSensitive` kết hợp với pattern tìm kiếm là `"critical"` (chữ thường). 

&#x20; 2. Trong khi đó, dữ liệu thực tế bên trong file `server.log` lại lưu các dòng lỗi bằng chữ hoa hoàn toàn: `CRITICAL`. 

&#x20; 3. Sự xung đột giữa cờ phân biệt hoa/thường (`-CaseSensitive`) với mẫu tìm kiếm khác định dạng đã khiến lệnh không khớp được bất kỳ dòng nào, làm cho phần trích xuất lỗi bị bỏ trống hoàn toàn.



\---



\## Phần 2: Sửa lỗi và Kiểm thử



\### 1. Đoạn script đã được sửa đúng

```powershell

\# ==========================================

\# Script: deploy\_checker.ps1 (Đã sửa lỗi)

\# Mục đích: Trích xuất lỗi CRITICAL từ server.log vào daily\_critical.txt

\# ==========================================



\# 1. Ghi tiêu đề bắt đầu quết log

"=== Bat dau quet log: $(Get-Date) ===" | Out-File daily\_critical.txt



\# 2. Sửa bằng cách bỏ cờ -CaseSensitive hoặc đổi pattern thành "CRITICAL" (hoặc dùng ký tự hoa/thường phù hợp)

Select-String -Path server.log -Pattern "CRITICAL" | Out-File -Append daily\_critical.txt



\# 3. Ghi dòng tiêu đề kết quả đếm

"=== Hoan tat. So dong loi: ===" | Out-File -Append daily\_critical.txt



\# 4. Đếm và ghi số lượng dòng lỗi thực tế vào cuối file

(Get-Content daily\_critical.txt | Select-String "CRITICAL").Count | Out-File -Append daily\_critical.txt # Hoặc đếm chính xác số dòng trích xuất từ file log

