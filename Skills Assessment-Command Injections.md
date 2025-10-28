# Skills Assessment-Command Injections

<img width="1028" height="398" alt="image" src="https://github.com/user-attachments/assets/601957ca-c502-421c-937f-198a639dc764" />

Sau khi đăng nhập vào 

<img width="1874" height="800" alt="image" src="https://github.com/user-attachments/assets/cdae6588-9a7f-4ce8-8ca0-a08bc174a49b" />

Để ý thì ở đây có tính năng move file, ở phần hiển thị giống với output sau khi thực thi lệnh move

<img width="1863" height="697" alt="image" src="https://github.com/user-attachments/assets/dfe22c22-74d0-49ad-afd1-4aab1bf0e7ad" />

Tôi có thử vài payload nhưng bị chặn lại, tôi thử bằng `$(wh'o'ami)` và thấy lệnh thực thi thành công:

<img width="1008" height="165" alt="image" src="https://github.com/user-attachments/assets/e6a49157-a4be-4657-93f3-330463846447" />

Phát triển payload thành `$(c%27a%27t%09${PWD:0:1}flag.txt)` để mở flag:

<img width="1220" height="199" alt="image" src="https://github.com/user-attachments/assets/b7cf3c4e-a377-49a8-b8fb-34ec88079d11" />







