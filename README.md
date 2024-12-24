# Nginx-reverse-proxy-with-ssl-certificate

- NODE PROJECT SITE LINK: **https://www.youtube.com/watch?v=ofBFl4M4BFk**
- TROUBLE SHOOTING SITE LINK: **https://www.youtube.com/watch?v=hgLFNSelxAs**
- Node project site plus reverse proxy plus SSL: **https://webdock.io/en/docs/how-guides/app-installation-and-setup/how-use-nginx-reverse-proxy-and-secure-connections-ssl-certificates?srsltid=AfmBOoo1ZLnvQEZ8MwLwM5UmHg3jVb9nPY40fP2_dqO5nBJXg0vYtCl4**


# Node.js Deployment

> Steps to deploy a Node.js app to DigitalOcean using PM2, NGINX as a reverse proxy and an SSL from LetsEncrypt

## 1. Create Free AWS Account
Create free AWS Account at https://aws.amazon.com/

## 2. Create and Lauch an EC2 instance and SSH into machine
I would be creating a t2.medium ubuntu machine for this demo.


## UPDATE THE REQUIRE PACKAGES

- sudo apt-get update

## INSTALL NODE JS PACKAGE ON SERVER, IT WILL HELP YOU ON INSTALLING DEPENDENCIES FOR NODE JS CODE.

curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs

node --version

## Clone your project from Github

git clone https://github.com/piyushgargdev-01/short-url-nodejs

cd cloned-folder

## Now install dependencies for nodejs code.

npm install

cd .. 

## Install pm2 with npm and start the process with pm2

sudo npm i pm2 -g
cd "code folder"
pm2 start index.js
pm2 logs ~all
pm2 restart <application name> or all(incase of all application)  --> use this incase of issue
pm2 logs ~all


now you will see that your application is running... if you are running application on ec2 install then open the port on SG on which the code is running. then traffic like <ec2 instance public-ip>:<code-port> 

remember hmra code directly server per run horha ha..

```

## 6. Setup Firewall
```
sudo ufw enable
sudo ufw status
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
```

## 7. Install NGINX and do nginx reverse proxy configuration
```
sudo apt install nginx

sudo nano /etc/nginx/sites-available/default  
```
Add the following to the location part of the server block             **ab because traffic 80 or 443 sa instance per arhi hogi tu mujy in ports ko instance k SG sa open krna hoga. **
```
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8001; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```
```
# Check NGINX config
sudo nginx -t

# Restart NGINX
sudo nginx -s reload
```

## 8. Add SSL with LetsEncrypt
```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Only valid for 90 days, test the renewal process with
certbot renew --dry-run
```



Details about nginx proxy
-------------------------

- In computer networking, a proxy server is a server application that acts an intermediary b/w a client requesting a resource and the server providing that resource.

- A proxy service which takes a client request, passes it on to one or more servers.

- Proxying is typically used to distribute the load among several servers, 

- Seamlessly show content from different websites.

- or pass requests for processing to application servers over protocols other than HTTP.


Changes in the conf
-------------------

        Config Location: /etc/nginx/nginx.conf
        
        location /some/path/ {
        
            proxy_pass http://www.example.com/link/;
        
        }
        
        like:
        
        
        location / {
        
            proxy_pass http://127.0.0.1:8080/;   or proxy_pass http://localhost:8080/; 
         
        }

Troubleshooting
---------------

You need to check logs in case of troubleshooting..

like nginx k logs.. **/var/log/nginx/** ma hoty hn.. to see logs use below command..

**sudo cat /var/log/audit/audit.log | grep nginx | grep denied**

or 

**sudo /var/log/nginx/**

here you can see logs under **access.log and error.log**


- **sudo tail -f access.log**  --> use -f with command for checking the live logs. mean browser sa jo b hit arhi hogi upstream(nginx reverse) usky live logs ap yha dekh sakhty hn..

- **sudo tail -f error.log**  --> use -f with command for checking the live error logs. mean browser sa jo b hit arhi hogi upstream(nginx reverse) usky live error logs ap yha dekh sakhty hn..



Example for SSL:
---------------

Nginx proxy file path: /etc/nginx/conf.d/here youcan use .conf file   or  /etc/nginx/site-enabled/here youcan use .conf file

**Nginx reverse proxy example for static files. hosting on server on /var/www/web directory**
    
    server {
        listen 80;
        server_name examplewebsite.code-graphers.com;
    
        # Redirect all HTTP requests to HTTPS
        return 301 https://$host$request_uri;
    }
    
    server {
        listen 443 ssl; # Managed by Certbot
        server_name examplewebsite.code-graphers.com;
    
        root /var/www/web;  # Directory where your HTML and CSS files are located
        index index.html;  # Default file to serve
    
        location / {
            try_files $uri $uri/ =404 /index.html;
        }
    
        ssl_certificate /etc/letsencrypt/live/examplewebsite.code-graphers.com/fullchain.pem; # Managed by Certbot
        ssl_certificate_key /etc/letsencrypt/live/examplewebsite.code-graphers.com/privkey.pem; # Managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # Managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # Managed by Certbot
    }


