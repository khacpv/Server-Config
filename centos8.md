###################### TIME-ZONE +7:00 ###################### 
0. links:
https://vinasupport.com/thay-doi-timezone-tren-rhel-centos-7-linux-server/

1. install
timedatectl set-timezone Asia/Ho_Chi_Minh


# NGINX
0. links:
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-7

1. install
sudo yum install epel-release
sudo yum install nginx
sudo systemctl start nginx

sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload

2 config
/etc/nginx/nginx.conf

3. start/stop
sudo systemctl enable nginx
/usr/share/nginx/html   # default server root


###################### FIREWALLD ###################### 
sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --zone=public --permanent --add-port=3000/tcp
sudo firewall-cmd --zone=public --permanent --add-port=4990-4999/udp
sudo firewall-cmd --reload
sudo firewall-cmd --zone=public --permanent --list-ports


###################### SSL ###################### 
1. links:
https://linuxize.com/post/secure-nginx-with-let-s-encrypt-on-centos-7/

2. install
sudo yum install certbot
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
sudo mkdir -p /var/lib/letsencrypt/.well-known
sudo chgrp nginx /var/lib/letsencrypt
sudo chmod g+s /var/lib/letsencrypt
sudo mkdir /etc/nginx/snippets

```/etc/nginx/snippets/letsencrypt.conf
location ^~ /.well-known/acme-challenge/ {
  allow all;
  root /var/lib/letsencrypt/;
  default_type "text/plain";
  try_files $uri =404;
}
```

```/etc/nginx/snippets/ssl.conf
ssl_dhparam /etc/ssl/certs/dhparam.pem;

ssl_session_timeout 1d;
ssl_session_cache shared:SSL:50m;
ssl_session_tickets off;

ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
ssl_ciphers 'ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA:ECDHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES256-SHA:ECDHE-ECDSA-DES-CBC3-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:DES-CBC3-SHA:!DSS';
ssl_prefer_server_ciphers on;

ssl_stapling on;
ssl_stapling_verify on;
resolver 8.8.8.8 8.8.4.4 valid=300s;
resolver_timeout 30s;

add_header Strict-Transport-Security "max-age=15768000; includeSubdomains; preload";
add_header X-Frame-Options SAMEORIGIN;
add_header X-Content-Type-Options nosniff;
```

```/etc/nginx/conf.d/example.com.conf
server {
  listen 80;
  server_name example.com www.example.com;

  include snippets/letsencrypt.conf;
}
```

sudo systemctl reload nginx
sudo certbot certonly --agree-tos --email admin@example.com --webroot -w /var/lib/letsencrypt/ -d example.com -d www.example.com

```/etc/nginx/conf.d/example.com.conf
server {
    listen 80;
    server_name www.example.com example.com;

    include snippets/letsencrypt.conf;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl http2;
    server_name www.example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
    include snippets/ssl.conf;
    include snippets/letsencrypt.conf;

    return 301 https://example.com$request_uri;
}

server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;
    ssl_trusted_certificate /etc/letsencrypt/live/example.com/chain.pem;
    include snippets/ssl.conf;
    include snippets/letsencrypt.conf;

    # . . . other code
}
```

sudo systemctl reload nginx

sudo crontab -e
0 */12 * * * root test -x /usr/bin/certbot -a \! -d /run/systemd/system && perl -e 'sleep int(rand(3600))' && certbot -q renew --renew-hook "systemctl reload nginx"
sudo certbot renew --dry-run


###################### GIT ###################### 
sudo yum install git
git --version

###################### SSH ###################### 
1. links:
https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-centos7

2. install:
ssh-keygen
ssh-copy-id username@remote_host
cat ~/.ssh/id_rsa.pub

###################### NODEJS - NPM - YARN - NVM ###################### 
0. links:
https://www.e2enetworks.com/help/knowledge-base/how-to-install-node-js-and-npm-on-centos/

1. install
curl https://raw.githubusercontent.com/creationix/nvm/v0.13.1/install.sh | bash
source ~/.bash_profile
nvm list-remote
nvm install v0.10.30
nvm list
nvm use v0.10.30
nvm alias default v0.10.30
node --version
npm --version


###################### PM2 ###################### 
sudo npm i -g pm2 
sudo pm2 start server.js
sudo pm2 logs               #view logs for all processes 
sudo pm2 startup            #enable PM2 to start at system boot
sudo pm2 startup systemd    #or explicitly specify systemd as startup system 
sudo pm2 save               #save current process list on reboot
sudo pm2 unstartup          #disable PM2 from starting at system boot
sudo pm2 update	            #update PM2 package

###################### MYSQL ###################### 
1. version 8.0
sudo yum localinstall https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
sudo yum install mysql-community-server


2. version 5.7
sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo yum install mysql-community-server

3. manage
sudo systemctl enable mysqld
sudo systemctl start mysqld
sudo systemctl status mysqld

4. change password
sudo grep 'temporary password' /var/log/mysqld.log     # print temp password
sudo mysql_secure_installation                         # enter temp password, new password

5. connect
mysql -u root -p
CREATE DATABASE test_db;
use test_db;
```SQL
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(30),
  email VARCHAR(30)
);
```

###################### DISK-SPACE ###################### 
df -h
mem -s

###################### SWAP ###################### 
0. links:
https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-12-04?comment=551

1. increase
swapoff -a
sudo dd if=/dev/zero of=/swapfile bs=1024 count=1024k


###################### NETWORK ###################### 

1. find IP
ip addr

