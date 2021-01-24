# Docker for Pi-hole With dns crypt proxy

## Installation
### Copy Raspbian to you Pi


Add empty file with name `ssh` to disk

---

### SSH into device

---



### Install Docker Compose
```sh
# install Docker
sudo apt-get update && sudo apt-get upgrade
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker pi

# install python3 and git
sudo apt-get install -y libffi-dev libssl-dev python3 python3-pip git
sudo apt-get remove python-configparser

# install docker-compose
sudo pip3 -v install docker-compose´
```

---

