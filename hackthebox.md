---
created: 2024-07-18T08:33
updated: 2025-07-14T09:46
title: HackTheBox Notes
---

## Ports

| Port     | Service         | Methods                         | Function       | Command                                        |
| -------- | --------------- | ------------------------------- | -------------- | ---------------------------------------------- |
| `23`     | `telnet`        | Try `root`                      | Shell          | `telnet 127.0.0.1`                             |
| `20/21`  | `ftp`           | Try `anonymous`                 | File access    | `ftp 127.0.0.1`                                |
| `445`    | `smb`           | Try every share                 | Shell          | `smbclient -L IP`<br>`smbclient \\\\IP\\Share` |
| `3389`   | `ms-wbt-server` | Try admin                       | Remote desktop | `xfreerdp /v:IP /cert:ignore /u:Administrator` |
| `6379/?` | `redis`         | `select N` `keys *` `get [key]` | Database       | `redis-cli -h IP`                              |
|          |                 |                                 |                |                                                |

## Gobuster

```bash
gobuster dir -x php -u http://10.129.98.54/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

## Mongodb

```bash
mongosh IP
> show dbs         # get list of databases
> show collections # get collections inside the db
> db.flag.find().pretty()
```

## Rsync

```bash
rsync --list-only rsync://10.129.228.37
rsync --list-only rsync://10.129.228.37/public
rsync rsync://10.129.228.37/public/flag.txt .
```
