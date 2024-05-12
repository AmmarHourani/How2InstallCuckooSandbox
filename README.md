# How2InstallCuckooSandbox
The internet is full of guides on how to install Cuckoo sandbox , all of them are faulty and require troubleshooting . this one is free from all of it.

// source is https://medium.com/@raytracer96024/a-straight-forward-guide-to-installing-cuckoo-sandbox-on-ubuntu-18-04-6a6e95bb34e5
// with a lot of troubleshooting and fixing

// ubuntu 18 from https://releases.ubuntu.com/18.04/ubuntu-18.04.6-desktop-amd64.iso

// the user during UBUNTU 18 OS installation should be cuckoo

sudo apt update -y && sudo apt upgrade -y && sudo apt-get install python python-pip python-dev libffi-dev libssl-dev -y && sudo apt-get install python-virtualenv python-setuptools -y && sudo apt-get install libjpeg-dev zlib1g-dev swig -y && sudo apt-get install mongodb -y && sudo apt-get install postgresql libpq-dev -y && sudo apt install virtualbox -y && sudo apt-get install tcpdump apparmor-utils -y

sudo groupadd pcap

//sudo adduser cuckoo

sudo usermod -a -G pcap cuckoo

sudo chgrp pcap /usr/sbin/tcpdump

sudo setcap cap_net_raw,cap_net_admin=eip /usr/sbin/tcpdump

getcap /usr/sbin/tcpdump

sudo aa-disable /usr/sbin/tcpdump

pip install typing

sudo apt-get install python-m2crypto

sudo usermod -a -G vboxusers cuckoo

touch cuckoo-setup-virtualenv.sh

sudo gedit cuckoo-setup-virtualenv.sh


---------------------cuckoo-setup-virtualenv.sh-----------------------------



#!/usr/bin/env bash

sudo apt-get update && sudo apt-get -y install virtualenv

sudo apt-get -y install virtualenvwrapper

echo "source /usr/share/virtualenvwrapper/virtualenvwrapper.sh" >> ~/.bashrc

sudo apt-get -y install python3-pip

pip3 completion --bash >> ~/.bashrc

pip3 install --user virtualenvwrapper

echo "export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3" >> ~/.bashrc

echo "source ~/.local/bin/virtualenvwrapper.sh" >> ~/.bashrc

export WORKON_HOME=~/.virtualenvs

echo "export WORKON_HOME=~/.virtualenvs" >> ~/.bashrc

echo "export PIP_VIRTUALENV_BASE=~/.virtualenvs" >> ~/.bashrc


---------------------cuckoo-setup-virtualenv.sh-----------------------------

chmod +x ./cuckoo-setup-virtualenv.sh

sudo -u cuckoo ./cuckoo-setup-virtualenv.sh

source ~/.bashrc

mkvirtualenv -p python2.7 cuckoo-test

pip install -U pip setuptools

pip install -U cuckoo

// download windows7.iso from https://drive.google.com/drive/folders/1BsRPYC0v__GiwO8k558FDdGx9IbBAWoP

sudo mkdir /mnt/win7

sudo chown cuckoo:cuckoo /mnt/win7/

cd /home/cuckoo/Downloads

sudo mount -o ro,loop win7ultimate.iso /mnt/win7

pip install -U vmcloak

vmcloak-vboxnet0

vmcloak init --verbose --win7x64 win7x64base --cpus 2 --ramsize 2048

// If you face troubles with the line above troubleshoot with ""pip install --upgrade --force-reinstall vmcloak""

// If you face troubles creating win7x64base vm , type virtualbox in the terminal , delete win7x64base vm with the mouse , and retry the above line

vmcloak clone win7x64base win7x64cuckoo

vmcloak install win7x64cuckoo ie11

mcloak snapshot --count 1 win7x64cuckoo 192.168.56.101

cuckoo community

while read -r vm ip; do cuckoo machine --add $vm $ip; done < <(vmcloak list vms)

cd /home/cuckoo/.cuckoo/conf/

sudo nano virtualbox.conf

// Scroll down to the the line starting with machines=... and remove cuckoo1 . Do not remove 192.168.56.1011 , Now take your cursor to [cuckoo1] , delete every thing from and including [cuckoo1] to the last line above [192.168.56.101] 
// ctrl+X then Y then enter to save

sudo nano reporting.conf 
//search for [mongodb] . Under it, switch enabled to yes
// ctrl+X then Y then enter to save

sudo nano routing.conf
//go to line where internet = none is written and replace it with internet = ens33 or whatever your interface name is ( find  out using "ip a" from another terminal )
// ctrl+X then Y then enter to save

cd /etc
sudo nano sysctl.conf
// scroll down to the end of the file and append "net.ipv4.conf.vboxnet0.forwarding=1" and another line "net.ipv4.conf.ens33.forwarding=1"  // replace ens33 with your interface name
// ctrl+X then Y then enter to save

sudo iptables -t nat -A POSTROUTING -o ens33 -s 192.168.56.0/24 -j MASQUERADE // replace ens33 with your interface name

sudo iptables -P FORWARD DROP

sudo iptables -A FORWARD -m state --state RELATED,ESTABLISHED -j ACCEPT

sudo iptables -A FORWARD -s 192.168.56.0/24 -j ACCEPT

// open a new terminal and type into it "workon cuckoo-test" then "cuckoo rooter --sudo --group cuckoo"

cuckoo web --host 127.0.0.1 --port 8080

// go to your firefox web browser and type 127.0.0.1:8080 and your cuckoo is ready to go

enjooooooooooooyyyyyyyyyyyyy
