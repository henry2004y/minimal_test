---
title: Data Transfer
tags:
  - computer
categories:
  - Blog
author: Hongyang Zhou
---

* `scp`: fast, straightforward, speed shown by default.

* `rsync`: good for avoiding duplicate file transfer, or in other words, transferring files on-the-go.
  * `-z`: compress/uncompress file data before/after transferring.
  * `-a`: archive mode, which allows copying files recursively and it also preserves symbolic links, file permissions, user & group ownerships, and timestamps. This is important when you try to synchronize the data, as timestamp difference is also taken into account.
  * `-v`: verbose

* `rclone`: good for cloud service, or transfer data between storage and compute nodes.
