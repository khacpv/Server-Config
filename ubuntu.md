# UBUNTU

## GIT
<details>
<summary>Install GIT</summary>
            
```bash
sudo apt-get install git -y
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
