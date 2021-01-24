# Docker for Pi-hole With dns crypt proxy

## Installation
### Copy Raspbian to you Pi


Add empty file with name `ssh` to boot disk

---

### SSH into device

*Find your pi's IP...*

```sh
ssh pi@10.0.1.12
```

---

### Install Docker Compose
```sh
# install Docker
sudo apt-get update && sudo apt-get upgrade
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker pi

# install python3, git and dnsutils
sudo apt-get install -y libffi-dev libssl-dev python3 python3-pip git dnsutils
sudo apt-get remove python-configparser

# install docker-compose
sudo pip3 -v install docker-compose´
```

---

## Download and run Pi-Hole
```sh
# Download this repo
sudo git clone https://github.com/jeppesens/pi-hole.git /pi-hole

# create and start the service
sudo cp /pi-hole/pi-hole.service /etc/systemd/system/pi-hole.service
sudo systemctl enable pi-hole
sudo systemctl start pi-hole

# check that the service is working
sudo systemctl status pi-hole
```

---

### Change DNS of host

```sh
sudo nano /etc/resolv.conf
# change so that you only have one nameserver row
nameserver 127.0.0.1
```

---

### Check that it works

```sh
nslookup gp.se
# output:
##########################
# Server:		127.0.0.1
# Address:	127.0.0.1#53

# Non-authoritative answer:
# Name:	gp.se
# Address: 193.14.90.203
##########################
```

---

### Setup another client

```sh
# get IP for host
ifconfig
# check output
####################################################################
# ...
# eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
#         inet 10.0.1.12  netmask 255.255.255.0  broadcast 10.0.1.255
# ...
####################################################################
```

Use the above IP (in this case 10.0.1.12) for DNS

---

# PS

Remember to look after your safety, consider changing password for pi user and disable ssh by password