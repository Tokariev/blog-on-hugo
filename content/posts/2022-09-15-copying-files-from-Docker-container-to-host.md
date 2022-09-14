---
title: "Copying Files From Docker Container to Host"
date: 2022-09-14T22:55:16+02:00
tags:
    - docker
keywords:
    - copy
    - docker
    - host
---

## Option 1

In order to copy a file from a container to the host, you can use the command

```sh
docker cp <containerId>:/path/within/container /host/path/target
```
