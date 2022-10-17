---
title: "How to add SSL certificate to EC2 Instance"
date: 2022-09-17T12:10:30+02:00
tags:
    - aws
    - EC2
    - certificate
keywords:
    - aws
    - certificate
    - SSL
    - webdev
    - javascript
    - node

---

This is what we want to achive.
![HTTPS](/HTTPS.svg)
NestJS app lives on AWS EC2 Instance and listening on port 3000.
Wenn the browser sends a HTTP request we want automaticaly forwarding to port 3000.
Also we want to use secure connection (HTTPS or SSL) 


# 0. In this tutorial we will:
1. Install [NGNIX](https://www.nginx.com/) [^1] on EC2 Instanse
   - Change configuration of the NGNIX to forward all requests from port 80 to NestJS app on port 3000
2. Install [Cerbot](https://certbot.eff.org) [^2]
   - Configure Cerbot  
   - Get a Let’s Encrypt [^3] certificate
3. Enjoying website with SSL 

[^1]: **Nginx** is a web server that can also be used as a reverse proxy, load balancer, mail proxy and HTTP cache.
[^2]: **Certbot** is a free, open source software tool for automatically using Let’s Encrypt certificates on manually-administrated websites to enable HTTPS.
[^3]: **Let’s Encrypt** is a free, automated, and open certificate authority (CA), run for the public’s benefit. 



## 1. Step
```sh 
sudo apt install nginx
```
Open NGNIX configuration file
```sh
sudo nano ~/etc/nginx/sites-available/default
```

And add this settings. 

```js
        server_name dev.api.skimpel.de www.dev.api.slimpel.de; #Your domain name

        location / {
                proxy_pass http://localhost:3000; # NestJS API port
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }
```

**Important!** Don't forget to add ports 443 and 3000 in your EC2 security group -> inbound rules
![Inbound rules](/inbound_rules.png)

Save file and restart NGNIX
```sh
sudo service nginx restart
```



## 2. Step 
Install Cerbot. [Here](https://snapcraft.io/docs/installing-snapd) is very nice site with instructions.

```sh
#Installing snapd proccess depends on your OS. Please visit the link above.
#Further instruction is relevant for Ubuntu 22.04 LTS

#Ensure that your version of snapd is up to date 
sudo snap install core; sudo snap refresh core

#Remove certbot-auto and any Certbot OS packages 
sudo apt-get remove certbot

#Install Certbot 
sudo snap install --classic certbot

#Prepare the Certbot command
sudo ln -s /snap/bin/certbot /usr/bin/certbot

#Choose how you'd like to run Certbot
sudo certbot --nginx 

```

After successfully received certificate you should get the following message:

...
Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/dev.api.skimpel.de/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/dev.api.skimpel.de/privkey.pem
This certificate expires on 2022-12-17.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Deploying certificate
Successfully deployed certificate for dev.api.skimpel.de to /etc/nginx/sites-enabled/default
Successfully deployed certificate for www.dev.api.skimpel.de to /etc/nginx/sites-enabled/default
Congratulations! You have successfully enabled HTTPS on https://dev.api.skimpel.de and https://www.dev.api.skimpel.de
...


```sh
#Test automatic renewal
#The Certbot packages on your system come with a cron job or systemd timer that will renew your certificates automatically before they expire. 
#You will not need to run Certbot again, unless you change your configuration. You can test automatic renewal for your certificates by running this command:
sudo certbot renew --dry-run

```

## 3. Step 😁

![Api with SSL](/api_with_ssl.png)


If you need more info about EC2 configuration please contact me at bonaparteit@gmail.com

Usefull links:
[How To Deploy Your Node.js App On AWS With NGINX And SSL](https://dev.to/shadid12/how-to-deploy-your-node-js-app-on-aws-with-nginx-and-ssl-3p5l)

