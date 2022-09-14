---
title: "AWS Cloud Front - access denied to S3 bucket"
date: 2022-09-13T20:43:37+02:00
tags:
    - aws
keywords:
    - git
    - actions
    - access
    - cloud_front
---

Original explanation [hier](https://www.youtube.com/watch?v=mls8tiiI3uc)

After creating cloud front distribution with mapping to my S3 bucket I got this access error:  
![Cloud frond - access denied](/cloud_front_access_denied.png)

The reason was wrong auto coplete value in this section:
![Cloud frond - wrong auto complete](/cloud_front_wrong_auro_complete.png)

What you should to to it's go to the S3 bucket and grab the URL in the property tab on the down of the tab
![Bucket URL](/bucket_url.png)
and put it in the origin domain input of the cloud front.

Enjoy!
