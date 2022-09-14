---
title: "Upload Files to Aws S3"
date: 2022-09-13T21:49:16+02:00
tags:
    - aws
    - S3
---

## Upload single file to S3 bucket
```sh
aws s3 cp sample2.txt s3://test-s3-bucket/data/
```

## Upload all files from a folder to S3 bucket recursively
```sh
aws s3 cp tmp/ s3://test-s3-bucket/data/tmp/ --recursive
```

## Using Sync
Sync is a command used to synchronize source and target directories. Sync is by default recursive which means all the files and subdirectories in the source will be copied to target recursively.

Use the below command to Sync your local directory to your S3 bucket. 
```sh
aws s3 sync your_local_directory s3://full_s3_bucket_name/ --region "ap-southeast-2"
```

_[Source](https://www.stackvidhya.com/upload-files-to-aws-s3/)_