---
title: "Bash Script to Init Ubuntu Server"
date: 2023-06-21T09:15:49+02:00
draft: true
---

```bash

echo 'Update repositories...'
sudo apt update
sudo apt upgrade

echo 'Install curl'
sudo apt install curl
curl --version

echo 'Install git'
sudo apt-get install git
git version

echo 'Install docker using the convenience script'
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh --dry-run

echo 'Install version manger for Node.js'
curl -fsSL https://raw.githubusercontent.com/tj/n/master/bin/n | bash -s lts
npm install -g n

echo 'Install latest Node.js'
n lts

```
