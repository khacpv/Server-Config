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

<details>
            <summary>Fix LC_CTYPE: cannot change locale (UTF-8)</summary>

#### `/etc/environment`
```bash
LANG=en_US.utf-8
LC_ALL=en_US.utf-8
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
nginx -t
```
</details>

<details>
            <summary>start/stop</summary>

```bash
sudo systemctl enable nginx
/usr/share/nginx/html   # default server root
```
</details>

<details>
<summary>Config folder</summary>
        

```bash
sudo chown -R www-data:www-data ./

# Directories 775 (for all parent levels)
sudo chmod 755 ../      

# Files 644
sudo chmod 644 ./*
```

### Port Already in used

```bash
sudo netstat -tulpn
sudo kill [pid]
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
wget https://nginx.org/download/nginx-1.19.0.tar.gz
tar -xzvf nginx-1.14.0.tar.gz
wget https://ftp.pcre.org/pub/pcre/pcre-8.42.zip
unzip pcre-8.42.zip
wget https://www.zlib.net/zlib-1.2.11.tar.gz
tar -xzvf zlib-1.2.11.tar.gz
wget https://www.open.org/source/openssl-1.1.0h.tar.gz
tar -xzvf openssl-1.1.0h.tar.gz
git clone https://github.com/sergey-dryabzhinsky/nginx-rtmp-module.git
rm -f *.tar.gz *.zip
ls -lah
cd nginx-1.19.0/
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

## MediaSoup V3

<details>
            <summary>Documents</summary>

* [Instalation](https://mediasoup.org/documentation/v3/mediasoup/installation/)

* [Demo V3](https://github.com/versatica/mediasoup-demo)

</details>

<details>
            <summary>Install failed on Centos 7 & gcc 8.x</summary>

https://mediasoup.discourse.group/t/centos-7-8-installation-error-please-upgrade-your-gcc/1512/6

```bash
Onetime installation:
  sudo yum groupinstall "Development Tools"
  sudo yum install centos-release-scl-rh
  sudo yum install devtoolset-9-gcc
When I rebuild mediasoup I run :

source scl_source enable devtoolset-9 
npm update
```

</details>

<details>
            <summary>Upgrade GCC to 4.9.2</summary>
* https://gist.github.com/craigminihan/b23c06afd9073ec32e0c
            
```bash
sudo yum install libmpc-devel mpfr-devel gmp-devel

cd ~/Downloads
curl ftp://ftp.mirrorservice.org/sites/sourceware.org/pub/gcc/releases/gcc-4.9.2/gcc-4.9.2.tar.bz2 -O
tar xvfj gcc-4.9.2.tar.bz2

cd gcc-4.9.2
./configure --disable-multilib --enable-languages=c,c++
make -j 4
make install
```

</details>

<details>
            <summary>Install GCC 7.3.0</summary>

https://linuxhostsupport.com/blog/how-to-install-gcc-on-centos-7/

```bash
screen -U -S gcc
wget http://ftp.mirrorservice.org/sites/sourceware.org/pub/gcc/releases/gcc-7.3.0/gcc-7.3.0.tar.gz
tar zxf gcc-7.3.0.tar.gz
cd gcc-7.3.0
yum -y install bzip2
./contrib/download_prerequisites
./configure --disable-multilib --enable-languages=c,c++
make -j 4
make install
gcc --version
```

</details>

<details>
            <summary>Upgrade GCC to latest</summary>
            
https://bipulkkuri.medium.com/install-latest-gcc-on-centos-linux-release-7-6-a704a11d943d

```bash
sudo yum -y update
sudo yum -y install bzip2 wget gcc gcc-c++ gmp-devel mpfr-devel libmpc-devel make
gcc --version           # gcc4.8.5
wget http://mirrors-usa.go-parts.com/gcc/releases/gcc-8.2.0/gcc-8.2.0.tar.gz   # change the link
tar zxf gcc-8.2.0.tar.gz
mkdir gcc-8.2.0-build
cd gcc-8.2.0-build
../gcc-8.2.0/configure --enable-languages=c,c++ --disable-multilib
make -j$(nproc)
sudo make install
gcc --version           # gcc8.2.0
```

### /install.sh

```bash
#!/bin/sh
GCC_VERSION=9.2.0       # change version
sudo yum -y update
sudo yum -y install bzip2 wget gcc gcc-c++ gmp-devel mpfr-devel libmpc-devel make
gcc --version
wget http://gnu.mirror.constant.com/gcc/gcc-$GCC_VERSION/gcc-$GCC_VERSION.tar.gz
tar zxf gcc-$GCC_VERSION.tar.gz
mkdir gcc-build
cd gcc-build
../gcc-$GCC_VERSION/configure --enable-languages=c,c++ --disable-multilib
make -j$(nproc)
sudo make install
gcc --version
cd ..
rm -rf gcc-build
```

### update PATH

```bash
export PATH=/usr/local/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH
```
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

<details>
            <summary>Open port 22</summary>

```bash
sudo ufw allow 22/tcp
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

```
sudo systemctl reload nginx
sudo certbot certonly --agree-tos --email khacpv@gmail.com --webroot -w /var/lib/letsencrypt/ -d example.com -d www.example.com
```

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
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
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

<details>
            <summary>Remove a domain</summary>
            
```shell
certbot certificates    # show all Domain Name
certbot delete --cert-name Domain_Name
```
</details>

<details>
            <summary>renew only domain</summary>

```shell
certbot certificates                     # list all domains
certbot renew --cert-name domain.com     # copy domain name from above
sudo systemctl reload nginx              # restart server to reload certificates
```

</details>

## GIT
<details>
            <summary>install</summary>

```bash
yum install http://opensource.wandisco.com/centos/7/git/x86_64/wandisco-git-release-7-2.noarch.rpm
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
            <summary>https://www.liquidweb.com/kb/how-to-install-node-js-via-nvm-node-version-manager-on-centos-7/</summary>
</details>

<details>
            <summary>install</summary>

```bash
curl https://raw.githubusercontent.com/creationix/nvm/v0.37.2/install.sh | bash
source ~/.bashrc
source ~/.bash_profile
nvm list-remote
nvm install v12.20.0
nvm list
nvm use v12.20.0
nvm current
#nvm alias default v12.20.0
node --version
npm --version

# yarn
curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | tee /etc/yum.repos.d/yarn.repo
rpm --import https://dl.yarnpkg.com/rpm/pubkey.gpg
yum install yarn
yarn --version
```
</details>

## PM2
<details>
            <summary>Install</summary>

```shell
npm i -g pm2 
```
</details>

<details>
            <summary>Retain Memory</summary>

```shell
pm2 flush
pm2 install pm2-logrotate
pm2 set pm2-logrotate:max_size 50M
pm2 set pm2-logrotate:retain 10
pm2 set pm2-logrotate:compress true
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
sudo pm2 resurrect          #restore from 'pm2 save'
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
mysql -u root -p[password] -Ae\"FLUSH TABLES WITH READ LOCK; SELECT SLEEP(5)\" &
mysqldump -u root -p[password] --single-transaction --routines --triggers database-name > /file-path.sql
```
</details>

<details>
            <summary>Graph Tree & Hierachy</summary>

Adjacency List Model vs Nested Set Model: http://mikehillyer.com/articles/managing-hierarchical-data-in-mysql/

Ebook: http://mikehillyer.com/articles/managing-hierarchical-data-in-mysql/

</details>

<details>
            <summary>Remove MySQL logs</summary>

```
PURGE BINARY LOGS TO 'mysql-bin.000019';        # latest file

# add /etc/my.cnf
disable_log_bin 

ALTER TABLE tablename ENGINE=InnoDB;            # recreate table with smaller size
OPTIMIZE tablename
```

</details>

<details>
            <summary>Forward Port & Tunning</summary>

#### **`/etc/ssh/sshd_config`**
```shell
AllowTcpForwarding yes  # enable TCP forwarding: yes
GatewayPorts yes        # remote port forwarding
```

```shell
sudo systemctl restart sshd         # or 'ssh' instead of 'sshd'
sudo service sshd restart
```

```shell
ssh -L 4000:127.0.0.1:3306 root@remote-server # port forwarding from 3306 to 4000   # keep-running
```

```shell
ssh -M -S my-socket-name -fNT -L 8888:localhost:8888 user@hostname      # run in background
ssh -S my-socket-name -O check root@hostname

# close port-forwarding
ssh -S my-socket-name -O exit root@hostname
```

</details>

<details>
            <summary>Change database location</summary>

https://www.digitalocean.com/community/tutorials/how-to-change-a-mysql-data-directory-to-a-new-location-on-centos-7

```
# show current datadir
mysql -u root -p
mysql> select @@datadir;
mysql> exit

# stop mysqld
sudo systemctl stop mysqld
sudo systemctl status mysqld

# 
sudo rsync -av /var/lib/mysql /mnt/blockstorage/mysql8/oicshop    # no trailing slash at end
```

### /etc/my.cnf

```
datadir=/mnt/blockstorage/mysql8/oicshop/mysql
socket=/mnt/blockstorage/mysql8/oicshop/mysql/mysql.sock

...

[client]
port=3306
socket=/mnt/volume-nyc1-01/mysql/mysql.sock
```

### completed

```
sudo systemctl start mysqld
sudo systemctl status mysqld

mysql -u root -p
select @@datadir;
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
<details>
           <summary>Find a biggest files in /</summary>
           
```bash
sudo du -a / 2>/dev/null | sort -n -r | head -n 20  # find a biggest files in /
```
</details>
<details>
           <summary>print folder size in GB or MB</summary>
           
```bash
sudo du -sh /var/log/nginx/         # print folder size in GB or MB
```
</details>

<details>
            <summary>Block Storage</summary>
            
https://www.vultr.com/docs/block-storage/

```
lsblk    # see /dev/vdb storage
parted -s /dev/vdb mklabel gpt    # create new disk label
parted -s /dev/vdb unit mib mkpart primary 0% 100%    # make primary partition
mkfs.ext4 /dev/vdb1    # create EXT4 on primary partition
mkdir /mnt/blockstorage   # make a mount point

# mount first time
echo >> /etc/fstab    # add a blank line
echo /dev/vdb1 /mnt/blockstorage ext4 defaults,noatime,nofail 0 0 >> /etc/fstab    # auto mount on restart

# or mount without reboot
mount /mnt/blockstorage
```

### remount existed Block Storage (ex: /dev/vdb1)

```
mkdir /mnt/blockstorage

echo >> /etc/fstab
echo /dev/vdb1 /mnt/blockstorage ext4 defaults,noatime,nofail 0 0 >> /etc/fstab

mount /mnt/blockstorage
```
          
</details>

## SWAP

<details>
            <summary>How to add Swap space</summary>

https://linuxize.com/post/how-to-add-swap-space-on-centos-7/

```bash
sudo swapon --show      # know has Swap or not
sudo fallocate -l 2G /swapfile      # create 2G of swap
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

#### **`/etc/ssh/sshd_config`**
```bash
/swapfile swap swap defaults 0 0
```

```bash
sudo swapon --show      #verify
```

</details>

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

<details>
            <summary>https://www.digitalocean.com/community/tutorials/how-to-install-secure-redis-centos-7</summary>
            
</details>

<details>
            <summary>Install</summary>

```bash
sudo yum install epel-release
sudo yum install redis -y
sudo systemctl start redis.service  # start
sudo systemctl enable redis  # auto start on boot
sudo systemctl status redis.service  # check status
```

</details>

<details>
            <summary>Config</summary>

```bash
sudo systemctl status redis.service   # check status
redis-cli ping                        # send ping
sudo systemctl restart redis.service  # restart service
redis-cli                             # send commands
```

</details>

<details>
            <summary>Password</summary>

### /etc/redis.conf

```bash
# SECURITY
requirepass a_strong_password
```

</details>

<details>
            <summary>Commands</summary>

> Full commands [here](https://www.tutorialspoint.com/redis/redis_quick_guide.htm)

```bash
redis-cli
SET key value
SET keyNumber 1
GET key
GETRANGE key start end
GETSET key value
APPEND key value
DEL key
EXISTS key
EXPIRE key seconds     # set expiry key after the specified time
TYPE key
redis-cli FLUSHALL
config set stop-writes-on-bgsave-error no   # disable backup on storage
```

</details>

<details>
            <summary>Bugs</summary>

[MISCONF Redis is configured to save RDB snapshots](https://stackoverflow.com/questions/19581059/misconf-redis-is-configured-to-save-rdb-snapshots)

```bash
config set stop-writes-on-bgsave-error no
```

</details>

## MQTT

## DOCKER

## MONGO-DB

## WORDPRESS - PHP-FPM

## S3 AMAZON

1. Copy From Bucket to another https://aws.amazon.com/premiumsupport/knowledge-center/move-objects-s3-bucket/

2. Change CLI user: `$aws configure`

3. Add IMA user and restrict to a bucket: https://aws.amazon.com/premiumsupport/knowledge-center/s3-console-access-certain-bucket/

4. Custom domain for a bucket

5. Add SSL for cloudFront

6. Upload file using npm

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

<details>
            <summary>Tutorial</summary>
            
https://finalstyle.com/danh-cho-ky-thuat-vien/huong-dan-cai-dat-va-cau-hinh-cronjob-tren-centos.html

https://crontab.cronhub.io/

```bash
sudo rpm -q cronie                     # check version
sudo yum install cronie                # install
sudo systemctl status crond.service    # check status
```

> sudo cat /etc/crontab

```
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root

# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
37 * * * * root run-parts /etc/cron.hourly
23 5 * * * root run-parts /etc/cron.daily
19 3 * * 0 root run-parts /etc/cron.weekly
23 0 6 * * root run-parts /etc/cron.monthly
```

Example:

```bash
0 0 * * * root /sample_command >/dev/null 2>&1
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
df -h folder-path      # size of sub-directory
du -h folder-path      # size of sub-directory
dh -h folder-path       # sub-directories
dh -sh folder-path      # only path
```
</details>
