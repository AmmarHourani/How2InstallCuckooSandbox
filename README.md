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


--------------------------------------------------
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
-------------------------------------------------

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

vmcloak clone win7x64base win7x64cuckoo

vmcloak install win7x64cuckoo ie11

mcloak snapshot --count 1 win7x64cuckoo 192.168.56.101

cuckoo init

cuckoo community
