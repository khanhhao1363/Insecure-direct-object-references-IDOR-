# Khai Thác Lỗ Hổng Insecure direct object references (IDOR)

> VulnLab: Cross-Site Scripting (XSS)

> Người Thực Hiện: Nguyễn Khánh Hào

> Cập Nhật Lần Cuối: 7/10/2024

# Mục Lục

[Summary](#summary)

[1. Invoices](#1.-invoices)

[2. Ticket Sales](#2.-ticket-sales)

[3. Changing Password](#3.-changing-password)

[4. Money Transfer](#4.-monney-transfer)

[5. Address Entry](#5.-address-entry)

[6. About](#6.-abount)

[7. Shopping Cart](#7.-shopping-card)

[Khuyến Nghị Khắc Phục](#khuyen-nghi-khac-phuc)

# Nội Dung

# Summary

Có thể hiểu đơn giản IDOR là loại bug cho phép hacker truy cập trực tiếp tới các đối tượng thông qua untrusted data (thường là mã định danh / identifier) mà không hề kiểm tra quyền truy cập.

# 1. Invoices

![image](https://github.com/user-attachments/assets/7ff7a719-9e49-4f94-807c-228ccf7191ed)

Khi access vào lab thì có thể thấy trang web có chức năng xem hóa đơn của một user bất kì.

![image](https://github.com/user-attachments/assets/a4bd4ef7-d378-4343-8538-b38656c01f5f)

Có thể thấy trang web nhận vào một identifier có id là `1` thông qua param `invoices_id`.

![image](https://github.com/user-attachments/assets/00db5e61-ad15-496f-9142-837d8d72136f)

Ngó sơ qua source code thì có thể thấy biến `$_GET['invoice_id']` sẽ nhận vào một untrusted data từ param `invoice_id` với số id của một user chứa thông tin của hóa đơn.

Vậy câu hỏi đặt ra là: Nếu chúng ta thay đổi qua một số id bất kì thì liệu có thể xem hóa đơn của một user khác hay không ?

![image](https://github.com/user-attachments/assets/763a6816-9241-4ec1-ad7f-d608719dae70)

Sau khi thay đổi qua id bằng 2 thì có thể thấy chúng ta đã xem được thông tin hóa đơn của một user khác. 
=> Vậy có thể kết luận, chức năng xem hóa đơn này không hề kiểm tra quyền truy cập của người dùng, --> dẫn đến có thể leo thang đặc quyền theo chiều ngang.

# 2. Ticket Sales

![image](https://github.com/user-attachments/assets/d521f138-4afe-461a-94ee-6bb878830f8a)

Lab tiếp theo cho chúng ta một chức năng mua vé.

![image](https://github.com/user-attachments/assets/89b91396-93f0-4d58-a9f1-821840632fdc)

Có thể thấy 2 biến `$_POST['amount']` và `$_POST['ticket_money']` được lấy từ input của user và không hề kiểm tra tính hợp lệ. 
Mình sẽ sử dụng burp suite để capture lại request xem bên trong ruột của gói tin chứa thông tin gì.

![image](https://github.com/user-attachments/assets/9ad96fb3-a387-45d2-9b13-5e04f5decac7)

Chúng ta có thể thao túng được 2 param `amount` và `ticket_money`. Với việc thay đổi giá trị truyền vào chúng ta có thể thay đổi số tiền trả bằng 0 =))))

![image](https://github.com/user-attachments/assets/63f486c4-8872-4581-8922-8e1cc8a3b0bd)

![image](https://github.com/user-attachments/assets/2fac17a6-3a4c-4fa8-8bf1-c6f3a84132cf)

# 3. Changing Password

![image](https://github.com/user-attachments/assets/aa8a04d5-914e-4bf9-afdd-da61d9fdc4b7)

Đối với bài lab này thì chúng ta sẽ có chức năng để thay đổi password. Để solve được lab này chúng ta sẽ leo quyền và thay đổi pass của user khác.

![image](https://github.com/user-attachments/assets/5ee4f3e8-4f46-4fd1-8cb8-1a890c5d7bc0)

Sau khi sử dụng burp suite để capture lại request thì chúng ta có thể thấy chức năng thay đổi pass nhân vào 2 param `password` và `user_id`.

![image](https://github.com/user-attachments/assets/1bc7c89a-9a37-4cf4-8536-1ed374c8032d)

Hai biến `$_POST['user_id']` và `$_POST['password']` nhận vào input từ user và không hề kiểm tra quyền truy cập nếu thay đổi số id của một user khác.

![image](https://github.com/user-attachments/assets/bd7ed620-4f6c-4842-b8fe-6da2b446d46d)

![image](https://github.com/user-attachments/assets/33854389-921a-4766-be13-eef2b55a68ea)

=> Solve lab.

# 4.  Money Transfer

![image](https://github.com/user-attachments/assets/b7e32b7c-0eb5-493e-ab7f-2a8d81d678e7)

Để solve được lab này chúng ta sẻ chuyển tiền từ user khác về tài khoản của chính mình.

đổi số id từ người gửi thành 2 từ param `sender_id` về account của chúng ta là 1.

![image](https://github.com/user-attachments/assets/bb7727a5-e762-4150-860b-87361682ba1f)

![image](https://github.com/user-attachments/assets/c7ef7088-c3ac-4c34-9121-c9a00914eec6)

=> solve.

# 5.  Address Entry

![image](https://github.com/user-attachments/assets/03bece58-8692-4674-8763-6c120b7bae45)

Khi access vào thì trang web có chứng năng update address và xác nhận đơn hàng.

![image](https://github.com/user-attachments/assets/4f270359-87cf-475c-88c1-0f6e41b20985)

![image](https://github.com/user-attachments/assets/f4b907f6-6ab7-4e1e-bd7d-d0db611b66d7)

Câu hỏi đặt ra là nếu chúng ta thay đổi tham số từ param `addressID` thì chuyện gì sẽ xảy ra.

![image](https://github.com/user-attachments/assets/cda15ee3-0a3a-4a6d-a1a4-3f0f9765d3af)

=> solve.

# 6. About

![Screenshot 2024-10-02 210323](https://github.com/user-attachments/assets/bf472132-832b-449a-9405-948b8d826f78)

Để solve được lab này chúng ta sẽ phải thay đổi thông tin của user khác.

![Screenshot 2024-10-02 210720](https://github.com/user-attachments/assets/0ee2c0cb-8364-4e5f-8ffa-6bcc9f8ea854)

Trang web có chức năng edit profile, chúng ta sẽ sử dụng burp suite để capture lại request và tiến hành sửa đổi id.
Ngó qua source code thì có thể thấy, `userid` được truyền vào biến `$_COOKIE['userid']`.

![Screenshot 2024-10-02 211001](https://github.com/user-attachments/assets/0f4eb5a6-b1d0-42e0-9d38-15c9c9d7db90)

Sau khi capture lại request chúng ta sẽ focus vào trường cookie.

![Screenshot 2024-10-02 211126](https://github.com/user-attachments/assets/7b747346-e03c-4c5a-909e-9abe88147152)

Mình sẽ sửa id thành 4.

![Screenshot 2024-10-02 211315](https://github.com/user-attachments/assets/c99b7f09-78d5-4746-afb4-7153c374ead2)

![Screenshot 2024-10-02 211411](https://github.com/user-attachments/assets/b77a509c-fe59-491d-acbc-4f4d0eb3e2ff)

# 7. Shopping Cart

![Screenshot 2024-10-02 211437](https://github.com/user-attachments/assets/15cf7124-4099-4c70-8dad-c32b07596239)

Mục tiêu của lab này là mua được flag, vì Balance (số tiền còn lại) chỉ có $1000 nên chúng ta sẽ tìm cách mua được flag với giá của các sản phẩm rẻ hơn.

![Screenshot 2024-10-02 212516](https://github.com/user-attachments/assets/5723378a-dbbc-4c79-86c8-43860ab56b33)

Để làm được điều đó đầu tiên chúng ta sẽ mua đại 1 sản phẩm bất kì và sử dụng burp suite để capture lại request và copy code (code của sản phẩm nào sẽ tương ứng với số tiền của sản phẩm đó).

![Screenshot 2024-10-02 212831](https://github.com/user-attachments/assets/3c85d2db-3611-46a7-a478-84d473967d98)

`/lab/idor/shopping-cart/3Dvalid.php?code=SSBoZWFyZCB0aGUgYWRtaW4gaXMgZm9yZ2V0ZnVs`

Sau khi đã có thứ cần có chúng ta sẽ tiến hành mua flag.

![Screenshot 2024-10-02 213448](https://github.com/user-attachments/assets/5591a5bf-3a0f-4635-b9fe-10e5f1525e7f)

Chúng ta sẽ quăng cái đống vừa copy từ sản phẩm trước vào đây để bypass.

![Screenshot 2024-10-02 213728](https://github.com/user-attachments/assets/a4af5fd4-18c0-417b-91ad-359b42d8d058)

![Screenshot 2024-10-02 213817](https://github.com/user-attachments/assets/86aeb438-639f-44ef-92be-9eb6223e6719)

=> solve lab.

# Khuyến Nghị Khắc Phục

1. Xác thực người dùng: Đảm bảo rằng người dùng đã xác thực trước khi truy cập vào tài nguyên. Chỉ cho phép truy cập nếu người dùng có quyền hợp lệ.

2. Phân quyền: Kiểm tra quyền truy cập của người dùng đối với từng tài nguyên. Mỗi khi người dùng yêu cầu một tài nguyên, hãy xác minh rằng họ có quyền truy cập vào tài nguyên đó.

3. Sử dụng tham số ngẫu nhiên: Tránh sử dụng các ID dễ đoán như số nguyên liên tiếp. Thay vào đó, có thể sử dụng UUID hoặc các giá trị ngẫu nhiên.

5. Áp dụng kiểm tra đầu vào: Kiểm tra và xác thực tất cả các tham số đầu vào từ người dùng. Đảm bảo rằng các ID hoặc tham số yêu cầu là hợp lệ và thuộc về người dùng hiện tại.
