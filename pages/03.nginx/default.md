---
title: 'Nginx Proxy Pass using SSL'
slug: nginx-ssl-proxy
---

# Nginx Reverse Proxy With SSL

Nginx is a powerful tool. It allows you to serve multiple apps, websites, load-balance applications and much more. This flexibility is all powered by a relatively simple configuration system that uses nearly-human-readable configuration files. This guide will demonstrate how to utilize Nginx to serve a web app, such as a NodeJS App, using SSL Encryption. 

## Prerequisites

This guide will assume a general understanding of using a linux-based system via command line, and will further assume the following prerequisites:

* Ubuntu 18.04
* Non-Root User
* App Running on Custom Port (this guide assumes port 3000)
* DNS A Name Record for Domain Used
* SSL Certificate For the Domain

## Nginx Configuration

The default configuration for Nginx on Ubuntu 18.04 when installed using the nginx-full package option is to look for available sites at the following location: 

```/etc/nginx/sites-available/```

This location will have a *default* file with an example nginx virtual host configuration. Instead, we will be creating a new site using an empty file that we can utilize. Once logged in as your non-root user, issue the following command to create the new configuration file:

    sudo touch /etc/nginx/sites-available/domain.tld
    
Be sure to replace _domain.tld_ with your domain you plan to associate with your app. 

Next, we will modify the file so that it does what we need it to. I will be using **vim** in this guide, but feel free to use whatever text editor you're most comfortable with: 

    sudo vim /etc/nginx/sites-available/domain.tld
    
Add the following, adjusting where indicated so that it functions as desired: 

    ###
    # This Section listens on port 80 for your domain and rewrites the request to HTTPS for us
    ###

    server {
    listen 80;
    server_name domain.tld www.domain.tld; # Edit this to your domain name
    rewrite ^ https://$host$request_uri permanent;
    }

    ###
    # This is all the configuration declarations that help SSL Function.
    ###

    server {
    listen 443 ssl;
    server_name domain.tld;                                               # Edit this to your domain name
    ssl_certificate /etc/letsencrypt/live/domain.tld/fullchain.pem;       # If you use Lets Encrypt, you should just need to change the domain. 
    ssl_certificate_key /etc/letsencrypt/live/domain.tld/privkey.pem;     # If you use Let's Encrypt, you should just need to change the domain.
    ssl_session_cache builtin:1000 shared:SSL:10m;                        # Defining option to share SSL Connection with Passed Proxy
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;                                  # Defining used protocol versions. 
    ssl_ciphers HIGH:!aNULL:!eNULL:!EXPORT:!CAMELLIA:!DES:!MD5:!PSK:!RC4; # Defining ciphers to use. 
    ssl_prefer_server_ciphers on;                                         # Enabling ciphers
    access_log /var/log/nginx/access.log;                                 # Log Location. Can be anywhere. Make sure the nginx user defined in /etc/nginx/nginx.conf has r/w permissions
    
    ###
    # This is the juicey part of the config file, handing off relevant data to our back-end app running on port 3000
    # Nothing should need to be changed here, unless port 3000 is not the port you're using. 
    # Furthermore, if you're using a socket to serve your app (PHP comes to mind), you can define a unix:.sock location here as well
    ###
    
    location / {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass http://localhost:3000;
    proxy_read_timeout 90;
    }

    }
    
Save and exit. 

Let's now test the configuration file. 

    sudo nginx -t
    
Upon successful test, you will see this output: 

    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful
    
Now that we know it's going to work as expected, issue the command to restart the nginx service

    sudo systemctl restart nginx
    
    # OR #
    
    sudo service nginx restart

Both commands perform the same task, simply preference decides your method here. I can safely say I use both and in no specific priority. 

You should now be able to launch your app if it was not running already and visit **domain.tld** in a browser, assuming the DNS is correct and be amazed as your app is showing to the world with HTTPS enabled. 

Let me know in the comments if you have a preferred way to serve your apps to the internet.
