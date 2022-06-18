# docker-v2ray
V2Ray in Docker 

Project V is a set of tools to help you build your own privacy network over internet. The core of Project V, named V2Ray, is responsible for network protocols and communications. It can work alone, as well as combine with other tools.

# Prerequisites
- Ubuntu 20.04+ (Recommended)

First, update repository index and install docker.io and docker-compose

```
sudo apt update
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker ubuntu
sudo su - $USER
```

# V2ray Server Installation

Git clone repository
```
git clone https://github.com/antyung88/docker-v2ray.git && cd docker-v2ray
```

Create V2ray Config File
```
mkdir ./v2ray && \
cat << EOF > ./v2ray/config.json
{
  "inbounds": [{
    "port": 9000, // Port of the server. Must be the same as above.
    "protocol": "vmess",
    "settings": {
      "clients": [{ "id": "b831381d-6324-4d53-ad4f-8cda48b30811" }] // Your Unique Client id to match
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  }]
}
EOF
```

Deploy service:
```
docker-compose up -d
```

# V2ray Client Installation

macOS (Example)
```
brew install v2ray
```

Edit your config.json file

```
sudo nano /usr/local/etc/v2ray/config.json
```

```
{
  "inbounds": [{
    "port": 1080,  // Port of socks5 proxy. Point your browser to use this port.
    "listen": "127.0.0.1", // 
    "protocol": "socks",
    "settings": {
      "udp": true
    }
  }],
  "outbounds": [{
    "protocol": "vmess",
    "settings": {
      "vnext": [{
        "address": "server", // Address of your V2Ray server. May be IP or domain address.
        "port": 9000,  // Port of your V2Ray server.
        "users": [{ "id": "b831381d-6324-4d53-ad4f-8cda48b30811" }] // Your Unique Client id to match
      }]
    }
  },{
    "protocol": "freedom",
    "tag": "direct",
    "settings": {}
  }],
  "routing": {
    "domainStrategy": "IPOnDemand",
    "rules": [{
      "type": "field",
      "ip": ["geoip:private"],
      "outboundTag": "direct"
    }]
  }
}
```

```
brew services run v2ray
brew services start v2ray
```

# Recommends using Firefox

Settings -> Network Settings -> Settings

![socks5](https://user-images.githubusercontent.com/99248117/174446054-6c43bf08-fcde-4c6e-8e83-14c2ede81a76.png)

