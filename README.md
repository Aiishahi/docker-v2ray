# docker-v2ray
V2Ray in Docker 

Project V is a set of tools to help you build your own privacy network over internet. The core of Project V, named V2Ray, is responsible for network protocols and communications. It can work alone, as well as combine with other tools.

# Prerequisites
- Ubuntu 20.04+ (Recommended)

# Installation

First, update repository index and install docker.io and docker-compose

```
sudo apt update
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker ubuntu
sudo su - $USER
```

# Install via Docker-Compose (Server)

```
sudo nano docker-compose.yml
```
```
version: "3.3"
services:
  v2ray:
    image: ghcr.io/antyung88/v2ray:main
    container_name: v2ray
    restart: always
    networks:
      - v2ray
    volumes:
      - ./v2ray:/etc/v2ray
    ports:
      - 9000:9000 # Specify Port Number
      
networks:
  v2ray:      
```

As you can see - ./v2ray:/etc/v2ray specify config files at v2ray folder. Create a directory with:

```
sudo mkdir v2ray && cd v2ray
```

Create our config.json file:

```
sudo nano config.json
```

```
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
```

# v2ray (Client)

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
brew service run v2ray
brew service start v2ray
```

# Recommends using Firefox

Settings -> Network Settings -> Settings

