# CENTOS 8

## TIME-ZONE
<details>
<summary>https://vinasupport.com/thay-doi-timezone-tren-rhel-centos-7-linux-server/</summary>
</details>

<details>
            <summary>Change to GMT+7</summary>

```bash
timedatectl
timedatectl set-timezone Asia/Ho_Chi_Minh
timedatectl list-timezones
```
</details>

<details>
<summary>https://www.cyberciti.biz/faq/unix-linux-getting-current-date-in-bash-ksh-shell-script/</summary>
</details>

<details>
            <summary>Set Time to variable</summary>

```bash
folderBackup="backup_$(date +"%Y-%m-%d")"
echo $folderBackup
```
</details>

## NGINX

<details>
<summary>https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-7</summary>
</details>


<details>
            <summary>install</summary>

```bash
sudo yum install epel-release
sudo yum install nginx
sudo systemctl start nginx

# open ports
sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --reload
```
</details>

<details>
            <summary>config</summary>

```bash
/etc/nginx/nginx.conf
```
</details>

<details>
            <summary>start/stop</summary>

```bash
sudo systemctl enable nginx
/usr/share/nginx/html   # default server root
```
</details>

## NGINX-RTMP-MODULE
<details>
<summary>https://www.howtoforge.com/tutorial/how-to-install-nginx-with-rtmp-module-on-centos/</summary>
</details>

<details>
            <summary>Install</summary>

```bash
sudo yum -y groupinstall 'Development Tools'
sudo yum -y install epel-release
sudo yum install -y  wget git unzip perl perl-devel perl-ExtUtils-Embed libxslt libxslt-devel libxml2 libxml2-devel gd gd-devel pcre-devel GeoIP GeoIP-devel
cd /usr/local/src
wget https://nginx.org/download/nginx-1.14.0.tar.gz
tar -xzvf nginx-1.14.0.tar.gz
wget https://ftp.pcre.org/pub/pcre/pcre-8.42.zip
unzip pcre-8.42.zip
wget https://www.zlib.net/zlib-1.2.11.tar.gz
tar -xzvf zlib-1.2.11.tar.gz
wget https://www.openssl.org/source/openssl-1.1.0h.tar.gz
tar -xzvf openssl-1.1.0h.tar.gz
git clone https://github.com/sergey-dryabzhinsky/nginx-rtmp-module.git
rm -f *.tar.gz *.zip
ls -lah
cd nginx-1.14.0/
./configure --prefix=/etc/nginx \
            --sbin-path=/usr/sbin/nginx \
            --modules-path=/usr/lib64/nginx/modules \
            --conf-path=/etc/nginx/nginx.conf \
            --error-log-path=/var/log/nginx/error.log \
            --pid-path=/var/run/nginx.pid \
            --lock-path=/var/run/nginx.lock \
            --user=nginx \
            --group=nginx \
            --build=CentOS \
            --builddir=nginx-1.14.0 \
            --with-select_module \
            --with-poll_module \
            --with-threads \
            --with-file-aio \
            --with-http_ssl_module \
            --with-http_v2_module \
            --with-http_realip_module \
            --with-http_addition_module \
            --with-http_xslt_module=dynamic \
            --with-http_image_filter_module=dynamic \
            --with-http_geoip_module=dynamic \
            --with-http_sub_module \
            --with-http_dav_module \
            --with-http_flv_module \
            --with-http_mp4_module \
            --with-http_gunzip_module \
            --with-http_gzip_static_module \
            --with-http_auth_request_module \
            --with-http_random_index_module \
            --with-http_secure_link_module \
            --with-http_degradation_module \
            --with-http_slice_module \
            --with-http_stub_status_module \
            --http-log-path=/var/log/nginx/access.log \
            --http-client-body-temp-path=/var/cache/nginx/client_temp \
            --http-proxy-temp-path=/var/cache/nginx/proxy_temp \
            --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp \
            --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp \
            --http-scgi-temp-path=/var/cache/nginx/scgi_temp \
            --with-mail=dynamic \
            --with-mail_ssl_module \
            --with-stream=dynamic \
            --with-stream_ssl_module \
            --with-stream_realip_module \
            --with-stream_geoip_module=dynamic \
            --with-stream_ssl_preread_module \
            --with-compat \
            --with-pcre=../pcre-8.42 \
            --with-pcre-jit \
            --with-zlib=../zlib-1.2.11 \
            --with-openssl=../openssl-1.1.0h \
            --with-openssl-opt=no-nextprotoneg \
            --add-module=../nginx-rtmp-module \
            --with-debug
sudo make
sudo make install
# sudo make clean # if build failed
sudo ln -s /usr/lib64/nginx/modules /etc/nginx/modules
sudo useradd -r -d /var/cache/nginx/ -s /sbin/nologin -U nginx
mkdir -p /var/cache/nginx/
chown -R nginx:nginx /var/cache/nginx/
nginx -t
nginx -V
```
</details>

<details>
            <summary>Config nginx as a service</summary>

#### **`/lib/systemd/system/nginx.service`**
```bash
[Unit]
Description=nginx - high performance web server
Documentation=https://nginx.org/en/docs/
After=network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -c /etc/nginx/nginx.conf
ExecStart=/usr/sbin/nginx -c /etc/nginx/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

[Install]
WantedBy=multi-user.target
```

```bash
systemctl daemon-reload
systemctl start nginx
systemctl enable nginx
cd /etc/nginx/
mv nginx.conf nginx.conf.asli
```

#### **`/etc/nginx/nginx.conf`**
```bash
worker_processes  auto;
events {
    worker_connections  1024;
}

# RTMP configuration
rtmp {
    server {
        listen 1935; # Listen on standard RTMP port
        chunk_size 4000;

        # Define the Application
        application show {
            live on;
            # Turn on HLS
            hls on;
            hls_path /mnt/hls/;
            hls_fragment 3;
            hls_playlist_length 60;
            # disable consuming the stream from nginx as rtmp
            deny play all;
        }
    }
}

http {
    sendfile off;
    tcp_nopush on;
    aio on;
    directio 512;
    default_type application/octet-stream;

    server {
        listen 8080;

        location / {
            # Disable cache
            add_header 'Cache-Control' 'no-cache';

            # CORS setup
            add_header 'Access-Control-Allow-Origin' '*' always;
            add_header 'Access-Control-Expose-Headers' 'Content-Length';

            # allow CORS preflight requests
            if ($request_method = 'OPTIONS') {
                add_header 'Access-Control-Allow-Origin' '*';
                add_header 'Access-Control-Max-Age' 1728000;
                add_header 'Content-Type' 'text/plain charset=UTF-8';
                add_header 'Content-Length' 0;
                return 204;
            }

            types {
                application/dash+xml mpd;
                application/vnd.apple.mpegurl m3u8;
                video/mp2t ts;
            }

            root /mnt/;
        }
    }
}
```

```bash
mkdir -p /mnt/hls
chown -R nginx:nginx /mnt/hls
nginx -t
systemctl restart nginx
```

#### **`/etc/nginx/nginx.conf`**
```bash
rtmp {
    # RTMP video on demand for mp4 files
    application vod {
        play /mnt/mp4s;
    }

    # RTMP stream using OBS
    application stream {
        live on;
    }
}
```

```bash
mkdir -p /mnt/mp4s
chown -R nginx:nginx /mnt/mp4s
nginx -t
systemctl restart nginx
```
</details>

<details>
            <summary>VLC</summary>

> rtmp://192.168.1.10:1935/vod/file.mp4
</details>

<details>
            <summary>OBS</summary>

> rtmp://192.168.1.10:1935/stream/

</details>

## FIREWALLD
<details>
            <summary>List Ports</summary>

```bash
sudo firewall-cmd --zone=public --permanent --list-ports
```
</details>

<details>
            <summary>Open Ports</summary>

```bash
sudo firewall-cmd --permanent --zone=public --add-service=http 
sudo firewall-cmd --permanent --zone=public --add-service=https
sudo firewall-cmd --zone=public --permanent --add-port=3000/tcp
sudo firewall-cmd --zone=public --permanent --add-port=4990-4999/udp
sudo firewall-cmd --reload
sudo firewall-cmd --zone=public --permanent --list-ports
```
</details>
<details>
            <summary>Close Ports</summary>

```bash
firewall-cmd --zone=public --remove-port=12345/tcp --permanent
firewall-cmd --reload
```
</details>

## SSL

<details>
            <summary>https://linuxize.com/post/secure-nginx-with-let-s-encrypt-on-centos-7/</summary>
</details>

<details>
            <summary>install</summary>

```bash
sudo yum install certbot
sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
sudo mkdir -p /var/lib/letsencrypt/.well-known
sudo chgrp nginx /var/lib/letsencrypt
sudo chmod g+s /var/lib/letsencrypt
sudo mkdir /etc/nginx/snippets
```

#### **`/etc/nginx/snippets/letsencrypt.conf`**
```shell
location ^~ /.well-known/acme-challenge/ {
  allow all;
  root /var/lib/letsencrypt/;
  default_type "text/plain";
  try_files $uri =404;
}
```

#### **`/etc/nginx/snippets/ssl.conf`**
```shell
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

#### **`/etc/nginx/conf.d/example.com.conf`**
```shell
server {
  listen 80;
  server_name example.com www.example.com;

  include snippets/letsencrypt.conf;
}
```

sudo systemctl reload nginx
sudo certbot certonly --agree-tos --email admin@example.com --webroot -w /var/lib/letsencrypt/ -d example.com -d www.example.com

#### **`/etc/nginx/conf.d/example.com.conf`**
```shell
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
</details>

<details>
            <summary>auto renew-keys</summary>

```shell
sudo systemctl reload nginx
sudo crontab -e
0 */12 * * * root test -x /usr/bin/certbot -a \! -d /run/systemd/system && perl -e 'sleep int(rand(3600))' && certbot -q renew --renew-hook "systemctl reload nginx"
sudo certbot renew --dry-run
```
</details>

## GIT
<details>
            <summary>install</summary>

```bash
sudo yum install git
git config --global core.editor "nano"
git --version
```
</details>

<details>
            <summary>reset local branch to remote</summary>

```bash
git fetch origin
git reset --hard origin/master
```
* note: save current changes to another branch (just in case)
</details>

## SSH

<details>
            <summary>https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-centos7</summary>
</details>

<details>
            <summary>install</summary>

```bash
ssh-keygen
ssh-copy-id username@remote_host
cat ~/.ssh/id_rsa.pub
```
</details>

<details>
            <summary>run command/bash on remote</summary>

```bash
ssh root@ip "shell command or bash script"
```
</details>

<details>
            <summary>copy with scp</summary>

```bash
scp root@ip:/file-path ./                                   # copy from remote to local
scp ./file-to-copy root@ip:/path-to-save                    # copy from local to remote
```
</details>

## NODEJS - NPM - YARN - NVM
<details>
            <summary>https://www.e2enetworks.com/help/knowledge-base/how-to-install-node-js-and-npm-on-centos/</summary>
</details>

<details>
            <summary>install</summary>

```bash
curl https://raw.githubusercontent.com/creationix/nvm/v0.13.1/install.sh | bash
source ~/.bash_profile
nvm list-remote
nvm install v0.10.30
nvm list
nvm use v0.10.30
nvm alias default v0.10.30
node --version
npm --version
```
</details>

## PM2
<details>
            <summary>Install</summary>

```shell
sudo npm i -g pm2 
```
</details>

<details>
            <summary>Manage</summary>

```shell
sudo pm2 start server.js
sudo pm2 logs               #view logs for all processes 
sudo pm2 startup            #enable PM2 to start at system boot
sudo pm2 startup systemd    #or explicitly specify systemd as startup system 
sudo pm2 save               #save current process list on reboot
sudo pm2 unstartup          #disable PM2 from starting at system boot
sudo pm2 update	            #update PM2 package
```
</details>

## MYSQL
<details>
            <summary>https://linuxize.com/post/install-mysql-on-centos-7/</summary>
</details>

<details>
            <summary>install version 8.0</summary>

```bash
sudo yum localinstall https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
sudo yum install mysql-community-server
```
</details>

<details>
            <summary>install version 5.7</summary>

```bash
sudo yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
sudo yum install mysql-community-server
```
</details>

<details>
            <summary>Start, Stop</summary>

```bash
sudo systemctl enable mysqld
sudo systemctl start mysqld
sudo systemctl status mysqld
```
</details>

<details>
            <summary>Change password</summary>

```bash
sudo grep 'temporary password' /var/log/mysqld.log     # print temp password
sudo mysql_secure_installation                         # enter temp password, new password
```
</details>

<details>
            <summary>connect</summary>

```bash
mysql -u root -p
CREATE DATABASE test_db;
use test_db;
```

```SQL
CREATE TABLE users (
  id INT PRIMARY KEY,
  name VARCHAR(30),
  email VARCHAR(30)
);
```
</details>

<details>
            <summary>dump database</summary>

```bash
mysqldump -u root -p[password] database-name > /file-path.sql
```
</details>

## DISK-SPACE
<details>
            <summary>list disk-spaces</summary>

```bash
df -h
mem -s
```
</details>

## SWAP

<details>
            <summary>https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-12-04?comment=551</summary>
</details>

<details>
            <summary>increase</summary>

```bash
swapoff -a
sudo dd if=/dev/zero of=/swapfile bs=1024 count=1024k
```
</details>

## NETWORK

<details>
            <summary>Find IP</summary>

```bash
ip addr
```
</details>

## SENDMAIL
<details>
            <summary>https://tecadmin.net/install-sendmail-server-on-centos-rhel-server/</summary>
</details>
<details>
            <summary>Notes</summary>

**access** — Allow/Deny other systems to use Sendmail for outbound emails.

**domaintable** — Used for domain name mapping for Sendmail.

**local-host-names** — Used to define aliases for the host.

**mailertable** — Defined the instructions that override routing for particular domains.

**virtusertable** — Specifies a domain-specific form of aliasing, allowing multiple virtual domains to be hosted on one machine.
</details>

<details>
            <summary>Install</summary>

```bash
yum install sendmail sendmail-cf m4
```
</details>

<details>
            <summary>Config</summary>

#### **`/etc/mail/sendmail.mc`**
```bash
dnl DAEMON_OPTIONS(`Port=smtp,Addr=127.0.0.1, Name=MTA')dnl    # comment this line to allow receiving from anywhere
FEATURE(`relay_hosts_only')dnl    # Add this line above ‘MAILER’ option
```

```bash
hostname >> /etc/mail/relay-domains     # replace hostname with PC's Full hostname
```

#### recompile
````bash
m4 /etc/mail/sendmail.mc > /etc/mail/sendmail.cf
````

#### restart
```bash
/etc/init.d/sendmail restart
```
</details>

<details>
            <summary>fix sendmail slow</summary>

#### **`/etc/hosts`**
```bash
127.0.0.1 localhost
::1       localhost
127.0.0.1 localhost.localdomain localhost my-website.com
```
</details>

## REDIS

## MQTT

## DOCKER

## MONGO-DB

## WORDPRESS - PHP-FPM

## CRONTAB

<details>
            <summary>run daily task</summary>

```bash
env EDITOR=nano crontab -e          # open crontab with NANO

# min  hour  day_of_month  month  day_of_week  your_command
0  3  *  *  *  sh /path/to/your/file            # run daily task at 3:00am

crontab -l  # show crontab list
crontab -r  # remove crontab

```
</details>

## OTHERS

<details>
            <summary>zip a folder</summary>

```bash
zip -9 -r file.zip /path-to-compress -x \*node_modules\*    # ignore node_modules folder
```
</details>

<details>
            <summary>display folder size</summary>

```bash
dh -h folder-path       # sub-directories
dh -sh folder-path      # only path
```
</details>
