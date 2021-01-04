# UBUNTU

## GIT
<details>
<summary>Install GIT</summary>
            
```bash
sudo apt-get install git -y
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
