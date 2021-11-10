# Proxmox-local-gui


### From a proxmox terminal as root:

```
apt update
apt install -y xorg  chromium
```

### Add new local user (pam)  with minimum privalages to launch chromium with xinit
```
useradd kiosk
mkhomedir_helper kiosk
```

### Create bash script to launch chromium with xinit
```
nano /home/kiosk/launch-kiosk-gui.sh
 ```
 ```
#!/bin/sh

# launch chromium directly with xinit
xinit \
/usr/bin/chromium \
https://127.0.0.1:8006 \
--kiosk \
--no-sandbox \
--window-size=1920,1080 \
--incognito \
--noerrdialogs \
--disable-translate \
--no-first-run \
--fast \
--fast-start \
--disable-infobars \
--disable-features=TranslateUI \
--disk-cache-dir=/dev/null \
--password-store=basic
```
### Change owner, group and restrict permissions

```
chown kiosk /home/kiosk/launch-kiosk-gui.sh
chgrp kiosk /home/kiosk/launch-kiosk-gui.sh
chmod a-rwx /home/kiosk/launch-kiosk-gui.sh
chmod u+x /home/kiosk/launch-kiosk-gui.sh
```

### Create service to run script at startup

```
nano /etc/systemd/system/launch-kiosk-gui.service                
```
```
[Unit]
Description=Launch Browser Kiosk mode for Local Proxmox GUI access
After=network.target

[Service]
Type=simple

User=kiosk
Group=kiosk
WorkingDirectory=/home/kiosk
ExecStart=/home/nanodano/launch-kiosk-gui.sh
Restart=on-failure


[Install]
WantedBy=multi-user.target
```
