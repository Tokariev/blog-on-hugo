---
title: "CloudFront 403 Error"
date: 2022-09-14T12:59:42+02:00
tags:
    - aws
    - certificate
keywords:
    - aws
    - http
---

After call URL http://dev.admin.skimpel.de/ I got the 403 error from AWS CloudFront.
![403 Error](/403_error.png)

After add dev.admin.skimpel.de to alternate domain name section the problem was solved.
![Altername domain name](/alternate_domain_name.png)

