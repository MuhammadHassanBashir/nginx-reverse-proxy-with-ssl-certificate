# Nginx-reverse-proxy-with-ssl-certificate

SITE LINK: **https://www.youtube.com/watch?v=ofBFl4M4BFk**
SITE LINK: **https://www.youtube.com/watch?v=hgLFNSelxAs**


# Node.js Deployment

> Steps to deploy a Node.js app to DigitalOcean using PM2, NGINX as a reverse proxy and an SSL from LetsEncrypt

## 1. Create Free AWS Account
Create free AWS Account at https://aws.amazon.com/

## 2. Create and Lauch an EC2 instance and SSH into machine
I would be creating a t2.medium ubuntu machine for this demo.

## 3. Install Node and NPM
```
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs

node --version
```

## 4. Clone your project from Github
```
git clone https://github.com/piyushgargdev-01/short-url-nodejs
```

## 5. Install dependencies and test app
```
sudo npm i pm2 -g
pm2 start index

# Other pm2 commands
pm2 show app
pm2 status
pm2 restart app
pm2 stop app
pm2 logs (Show log stream)
pm2 flush (Clear logs)

# To make sure app starts when reboot
pm2 startup ubuntu
```

## 6. Setup Firewall
```
sudo ufw enable
sudo ufw status
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
```

## 7. Install NGINX and configure
```
sudo apt install nginx

sudo nano /etc/nginx/sites-available/default
```
Add the following to the location part of the server block
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

like nginx k logs.. /var/log/nginx/ ma hoty hn.. to see logs use below command..

**sudo cat /var/log/audit/audit.log | grep nginx | grep denied**

or 

sudo /var/log/nginx/

here you can see logs under **access.log and error.log**

- **sudo tail -f access.log**  --> use -f with command for checking the live logs. mean browser sa jo b hit arhi hogi upstream(nginx reverse) usky live logs ap yha dekh sakhty hn..

sudo tail -f error.log  --> use -f with command for checking the live error logs. mean browser sa jo b hit arhi hogi upstream(nginx reverse) usky live error logs ap yha dekh sakhty hn..
