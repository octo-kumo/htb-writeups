---
created: 2024-09-24T22:59
updated: 2025-07-14T09:46
---

```bash
nmap -sV -p- --min-rate=10000 10.129.185.114
22/tcp   open  ssh     OpenSSH 9.2p1 Debian 2+deb12u2 (protocol 2.0)
5000/tcp open  upnp?
```

## website
http://10.129.185.114:5000/
![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727233451/2024/09/20848d9b0f6db6ffd779e67985e65367.png)

### probe
sending `<>` will trigger "Hacking attempt", but does not trigger it when entered inside name fields or phone number.

The hacking attempt page has this cookie, hrm.

```
Cookie: is_admin=InVzZXIi.uAlmXlTvm8vyihjNaPDWnvB_Zfs
```

### What is the relative url of the page on Headless that requires authorization to access?

Running gobuster I found `/dashboard`
![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727233880/2024/09/26e0520928f0e716bed6ccb6faaac74c.png)

...

```
gobuster dir -u http://10.129.76.20 -w 
```

```

## XSS
After cheating by searching online, I found the way.

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727235169/2024/09/c0bc96b19112b6781bb453b3a49c27dd.png)

I'm sending a XSS payload inside `Referer` because the request itself is being displayed as html.

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727235210/2024/09/a85cce09aa91ee16164ca82828716eec.png)

```bash
echo "aXNfYWRtaW49SW1Ga2JXbHVJZy5kbXpEa1pORW02Q0swb3lMMWZiTS1TblhwSDA=" | base64 -d
is_admin=ImFkbWluIg.dmzDkZNEm6CK0oyL1fbM-SnXpH0
```

## solve
![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727235545/2024/09/49fc01c8400568fc7074955b80bc4168.png)

Using this cookie we now have access to `/dashboard`.

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727235677/2024/09/19a66b9ba762c803b32e28b4e54fd212.png)

Wow! Bash injection!

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727235731/2024/09/50bfb5148ae7bc22b74ac2634dbc9f89.png)

![image.png](https://res.cloudinary.com/kumonochisanaka/image/upload/v1727235772/2024/09/7bfa8e594a0f7feffe0a5d3bb342a3f1.png)

And we have the flag!

```flag
cb8b46a821eea0d9fb0afd898d93fd92
```
