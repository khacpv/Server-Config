# UBUNTU

## GIT
<details>
<summary>Install</summary>
            
```bash
sudo apt-get install git -y
```

</details>

## TIMEZONE
<details>
<summary>Set timezone</summary>
            
https://linuxize.com/post/how-to-set-or-change-timezone-on-ubuntu-18-04/
            
```bash
timedatectl
ls -l /etc/localtime
cat /etc/timezone
timedatectl list-timezones          # Asia/Ho_Chi_Minh
sudo timedatectl set-timezone Asia/Ho_Chi_Minh
timedatectl
```

</details>

## CERTBOT SSL
<details>
<summary>Install</summary>
            
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04#step-5-setting-up-server-blocks-(recommended)

https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04
            
```bash
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx
sudo certbot certonly --nginx
```

</details>

## SWAP
<details>
<summary>Install</summary>
            
https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-18-04
            
```bash
# find best size for swapfile
sudo swapon --show
free -h
df -h

# create swapfile
sudo fallocate -l 1G /swapfile
ls -lh /swapfile
sudo chmod 600 /swapfile
ls -lh /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
sudo swapon --show

# keep swapfile after reboot
sudo cp /etc/fstab /etc/fstab.bak
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

# optimise swapfile
cat /proc/sys/vm/swappiness
sudo sysctl vm.swappiness=10

cat /proc/sys/vm/vfs_cache_pressure
sudo sysctl vm.vfs_cache_pressure=50
```

### /etc/sysctl.conf

```bash
# keep at reboot
vm.swappiness=10
vm.vfs_cache_pressure=50
```

</details>

## NGINX
<details>
<summary>Install</summary>
            
https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04
            
```bash
sudo apt update
sudo apt install nginx
sudo ufw app list
sudo ufw allow 'Nginx Full'
sudo ufw status
systemctl status nginx
sudo ufw enable
```

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


## NODEJS
<details>
<summary>Install Nodejs</summary>
            
https://blog.csdn.net/cgs1999/article/details/89703649
            
```bash
sudo apt-get update

# Ubuntu 16.04
# sudo apt-get install -y python-software-properties software-properties-common

# Ubuntu 18.04
sudo apt-get install -y software-properties-common

sudo add-apt-repository ppa:chris-lea/node.js
sudo apt-get update

# Ubuntu 16.04
#sudo apt-get install nodejs
#sudo apt install nodejs-legacy
#sudo apt install npm

# Ubuntu 18.04
sudo apt-get install nodejs
sudo apt install libssl1.0-dev nodejs-dev node-gyp npm

sudo npm config set registry https://registry.npm.taobao.org
sudo npm config list

sudo npm install n -g
sudo n stable

sudo node -v
sudo npm -v
```

</details>

## OPEN PORTS
<details>
<summary>List All Open Ports</summary>
            
https://websiteforstudents.com/find-all-open-ports-listening-ports-on-ubuntu-18-04-16-04/
            
```bash
sudo netstat -tnlp | grep :22
```

</details>

<details>
<summary>Open Ports</summary>
            
https://linuxconfig.org/how-to-open-allow-incoming-firewall-port-on-ubuntu-18-04-bionic-beaver-linux
            
```bash
# Open incoming TCP port 10000 to any source IP address:
sudo ufw allow from any to any port 10000 proto tcp

# Open incoming TCP ports 20 and 21 from any source, such as when running FTP server:
sudo ufw allow from any to any port 20,21 proto tcp

# add a range of ports
sudo ufw allow 8000:10000/udp
```

</details>

## MEDIASOUP

<details>
<summary>Install</summary>

https://www.programmersought.com/article/82351046329/

```bash
sudo apt-get install git -y
sudo npm install mediasoup
sudo npm install mediasoup-client

```

</details>

<details>
<summary>Install Demo Version</summary>

https://www.programmersought.com/article/82351046329/

```bash
git clone https://github.com/versatica/mediasoup-demo.git
cd mediasoup-demo/server
npm install
cp config.example.js config.js      # edit config file
cd mediasoup-demo/app
npm install
npm install -g gulp-cli
cd mediasoup-demo/server
node server.js

# run in a separate terminal
cd mediasoup-demo/app   
gulp live
```

- Server in https://{ip}:3000/ 

</details>
