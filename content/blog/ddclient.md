+++
title = "How I configured ddclient v4.0.0
date = 2025-03-15
+++

# How to configure ddclient 4.0.0 in Debian 12 with Porkbun as the registrar.


This is how I installed and configured ddclient v4.0.0 on a Debian 12 Linux container running in Proxmox 8. I use porkbun for my domain resgitrar.

## Setup and install
```
wget https://github.com/ddclient/ddclient/releases/download/v4.0.0/ddclient-4.0.0.tar.gz
tar xvfa ddclient-4.0.0.tar.gz 
apt install dh-autoreconf 
cd ddclient-4.0.0/
./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var
make
make VERBOSE=1 check
sudo make install
```

## Configure ddclient
`nano /etc/ddclient/ddclient.conf`
```
pid=/var/run/ddclient.pid  # record PID in file.
use=web, web=freedns.afraid.org/dynamic/check.php, web-skip='DETECTED IP'
protocol=porkbun
apikey=***
secretapikey=***
root-domain=mydomain.net
mydomain.net
```

## Configure systemd unit
`systemctl edit --full ddclient.service`

```
[Unit]
Description=Dynamic DNS Update Client
Wants=network-online.target
After=network-online.target nss-lookup.target

[Service]
Type=exec
Environment=daemon_interval=5m
ExecStart=/usr/bin/ddclient --daemon ${daemon_interval} --foreground --verbose --syslog
Restart=on-failure

[Install]
WantedBy=multi-user.target  
```
