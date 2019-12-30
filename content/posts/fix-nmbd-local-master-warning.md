---
title: "How to fix prevent repeating 'local master browser' log"
date: 2019-12-30T21:20:32+09:00
categories: [howtolog]
tags: [smbd, samba,howto,tip,nmbd]
draft: false
---

How to fix repeating following log on syslog?

<!--more-->

```
Dec 31 06:05:34 handmade-omv5 nmbd[9739]: [2019/12/31 06:05:34.797089,  0] ../source3/nmbd/nmbd_become_lmb.c:397(become_local_master_stage2)
Dec 31 06:05:34 handmade-omv5 nmbd[9739]:   *****
Dec 31 06:05:34 handmade-omv5 nmbd[9739]:   
Dec 31 06:05:34 handmade-omv5 nmbd[9739]:   Samba name server HANDMADE-OMV5 is now a local master browser for workgroup WORKGROUP on subnet 169.254.45.94
Dec 31 06:05:34 handmade-omv5 nmbd[9739]:   
Dec 31 06:05:34 handmade-omv5 nmbd[9739]:   *****
```

Add following line to global on /etc/samba/smb.conf

```
local master = no
```

The configuration file should following.

```
$ head /etc/samba/smb.conf
#======================= Global Settings =======================
[global]
workgroup = WORKGROUP
server string = %h server
# Extra options
local master = no
```
