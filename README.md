## Setup a Server

- Ubuntu Settings

```bash
apt update
apt upgrade

adduser [username]
usermod -a -G sudo [username]

vim /etc/ssh/sshd_config    # PermitRootLogin prohibit-password

hostnamectl set-hostname qizhong.land
```

- BBR

```bash
uname -r
cp /etc/sysctl.conf /etc/sysctl.conf.old
echo 'net.core.default_qdisc=fq' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_congestion_control=bbr' | sudo tee -a /etc/sysctl.conf
sysctl -p
reboot

sudo sysctl net.ipv4.tcp_available_congestion_control
sudo sysctl -n net.ipv4.tcp_congestion_control
```

- UFW

```bash
sudo apt install ufw
sudo ufw default allow outgoing
sudo ufw allow ssh
sudo ufw enable
sudo ufw status
```

## Deployment - Method 1

### Install Git and Clone Repos

- Git

```bash
sudo apt install git
git config --global user.name [username]
git config --global user.email [email]
ssh-keygen -t rsa -C [email]

# SSH
cat ~/.ssh/id_rsa.pub

git clone git@github.com:tane-rs/v2ray_server_config.git
git clone git@github.com:chzionland/chzionland.github.io.git
```

### Install Evironment using Script

```bash
cd v2ray_server_config
sudo bash install.sh

sudo nano /etc/nginx/nginx.conf
"""
...
http {
    ...
    server_names_hash_bucket_size 64;
    ...
}
...
"""
sudo systemctl restart nginx

# Alias
sudo nano ~/.bash_aliases
"""
function v2restart {
    sudo systemctl restart v2ray
    sudo systemctl status v2ray
}

alias v2check="sudo /usr/bin/v2ray/v2ray -test -config /etc/v2ray/config.json"
alias v2traffic="sudo /etc/v2ray/traffic.sh"
"""

# Test
sudo crontab -l
ls /etc/v2ray/      # traffic.sh
ls /var/log/v2ray/
```

## Install Website

```bash
cd chzionland.github.io
cp . /var/www/qizhong.land/html/
```
