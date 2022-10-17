---
title: "Connect Redis on EC2 Instance"
date: 2022-09-15T21:05:17+02:00

tags:
    - aws
    - EC2
    - Redis
keywords:
    - connection
    - redis
---

## Installl Redis CLI on EC2
`sudo apt-get install redis`

## Connect to Redis
`redis-cli -h cache-001.xxxxxx.0001.euc1.cache.amazonaws.com  -c -p 6379`


