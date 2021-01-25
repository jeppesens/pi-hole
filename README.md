# Docker for Pi-hole With dns crypt proxy

---

### Prerequisites for this guide
- Very basic terminal knowledge
- Some `nano` knowledge e.g save with ctrl+x, y
- Very basic `ssh` knowledge
- How to change dns on your router (Google is your friend)
- A Raspberry Pi *(but if you're using something else, just replace username and password to something appropriate)*
-

---

## Installation
### Copy `Raspberry Pi OS` to you Pi

- Copy Raspberry Pi OS to an SD card see [here](https://www.raspberrypi.org/documentation/installation/installing-images/) I recommend `Raspberry Pi OS Lite` because we're not gonna use the UI.
- Add empty file with name `ssh` to boot image see [here](https://www.raspberrypi.org/documentation/remote-access/ssh/)

---

### Connect your Raspberry Pi

Connect the device to your network and power

---

### SSH into device

*Find your Pi's IP. In this example 10.0.1.12*

```sh
ssh pi@10.0.1.12
```

---

### Give Pi a static IP

Give your Raspberry Pi a static IP, either in router or on the Raspberry Pi itself.
You need a static ip of the Pi or else you cannot know where to point your dns in case anything restarts.
**In router:**
I think this is the easiest way, because the config will remain even if you reinstall OS.
```sh
# Find MAC-address
ifconfig
# output:
"""
...
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.1.12  netmask 255.255.255.0  broadcast 10.0.1.255
        inet6 ---  prefixlen 64  scopeid 0x20<link>
        ether 1b:4f:41:bd:bf:cd  txqueuelen 1000  (Ethernet)
        RX packets 570147  bytes 591129294 (563.7 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 168053  bytes 25073010 (23.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
...
"""
```
Copy the the address behind ether in the eth0 paragraph. In this example it would be `1b:4f:41:bd:bf:cd`.
Set it static in your router

**On machine:**

Find a guide elsewhere and edit `/etc/dhcpcd.conf`

**If you changed IP, you may reboot and reconnect with ssh**

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
sudo pip3 -v install docker-compose
```

---

## Download and run Pi-Hole
```sh
# Download this repo
sudo git clone https://github.com/jeppesens/pi-hole.git /pi-hole

# Add Pi's IP to config
cd /pi-hole
sudo nano .env
# add this line with your Pi's IP
ServerIp=10.0.1.12

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
"""
Server:		127.0.0.1
Address:	127.0.0.1#53

Non-authoritative answer:
Name:	gp.se
Address: 193.14.90.203
"""
```

---

### Setup another client

```sh
# get IP for host
ifconfig
# check output
"""
...
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.1.12  netmask 255.255.255.0  broadcast 10.0.1.255
...
"""
```

Use the above IP (in this case 10.0.1.12) for DNS

---

# PS

Remember to look after your safety, consider changing password for pi user and disable ssh by password

---

### Prerequisites
- You need to have a ssh key on your machine, see `ssh-keygen` there's tons of guides


### Enable secure passwordless ssh login
```sh
# Enable ssh key authentication fromt he machine you're using now
# On your computer
ssh-copy-id pi@10.0.1.12
# enter password (raspberry)
# output:
"""
Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'pi@10.0.1.12'"
and check to make sure that only the key(s) you wanted were added.
"""

# Disable ssh password login
# On Pi Hole
sudo nano /etc/ssh/sshd_config
# Find and replace or add
PasswordAuthentication no
```

---

**Good luck!**