# Web Attacks - Skills Assessment

<img width="1127" height="762" alt="image" src="https://github.com/user-attachments/assets/6bd79644-a6cc-430a-a758-7e3b250ee112" />

Truy cập vào thì là trang login, tôi đăng nhập vào và có giao diện như này:

<img width="1909" height="1017" alt="image" src="https://github.com/user-attachments/assets/e7e99d27-ddcf-401a-bd5d-ad61032e6a74" />

Tôi vào phần setting xem thử, ở đây có tính năng đổi mật khẩu:

<img width="1906" height="1008" alt="image" src="https://github.com/user-attachments/assets/e08551b3-f73d-49ad-9f31-d6868d44ee75" />

Kiểm tra các request được gửi đi thì gồm có 2 cái là `/api.php/token/74` để lấy token sau đó đính kèm vào `/reset.php`:

<img width="1843" height="649" alt="image" src="https://github.com/user-attachments/assets/9c4c7396-a46b-45bb-8e37-dbdf729eefd9" />

<img width="1666" height="800" alt="image" src="https://github.com/user-attachments/assets/ec81c519-b022-4195-b4f7-51e353efa4aa" />

Trong phần `/profile.php` còn có cả `/api.php/user/<uid>` để xem thông tin của user:

<img width="926" height="898" alt="image" src="https://github.com/user-attachments/assets/100bddef-cc20-4eaf-b75d-7e1a3dbec70b" />

Gửi đi thì hiện ra 1 số thông tin cơ bản:

<img width="922" height="520" alt="image" src="https://github.com/user-attachments/assets/a8ccc683-0182-4d17-8d77-0394ae8e3058" />

Tôi đưa vào intruder và brute tất cả các uid, tìm được uid 52 là có vẻ của admin:

<img width="1265" height="810" alt="image" src="https://github.com/user-attachments/assets/dad10f44-1eb8-4a3f-8149-b37aab2356b1" />

Có thể lấy được token của uid 52:

<img width="1506" height="423" alt="image" src="https://github.com/user-attachments/assets/145c444f-71b3-4568-ac52-1b933dfe98af" />

Tôi chỉnh thử trong đổi mật khẩu, cơ mà lại không được:

<img width="1509" height="455" alt="image" src="https://github.com/user-attachments/assets/3088f68f-cada-4664-af83-88be40438244" />

Sau khi đổi method request thì được:

<img width="1507" height="428" alt="image" src="https://github.com/user-attachments/assets/4bd17000-68f3-4ad2-a9e5-53e448574772" />

Sau khi đăng nhập vào thì có tính năng là add event:

<img width="1914" height="999" alt="image" src="https://github.com/user-attachments/assets/98972809-5aea-4161-b65a-0f540a1903d6" />

Khi tạo event thì trong request gửi đi có dạng xml:

<img width="1918" height="1019" alt="image" src="https://github.com/user-attachments/assets/bf0f5283-eb58-4267-8e78-301fa2b70a48" />

<img width="1661" height="1045" alt="image" src="https://github.com/user-attachments/assets/a2ee82e1-519c-47ce-a7a9-3794950d18e5" />

Và ở đây có xảy ra xxe:

```
<!DOCTYPE file [
<!ENTITY file SYSTEM "php://filter/convert.base64-encode/resource=/etc/passwd">
]>
            <root>
            <name>&file;</name>
            <details>sss</details>
            <date>2025-11-08</date>
            </root>
            
```

<img width="1898" height="930" alt="image" src="https://github.com/user-attachments/assets/d091e45e-539a-43d0-9cc3-dbd4aa7486e1" />


<img width="1887" height="810" alt="image" src="https://github.com/user-attachments/assets/ef282fa7-e886-4eef-8284-482b24f923eb" />






















