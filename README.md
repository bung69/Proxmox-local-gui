# Proxmox-local-gui


### Install xorg, Chromium and Openbox

From a proxmox terminal as root:
```
apt update

apt install --no-install-recommends -y xorg  chromium openbox
```

### Add new local user (pam)  with minimum privalages to launch chromium with xinit
```
useradd kiosk
mkhomedir_helper kiosk
```

### Openbox Configuration

```
nano /etc/xdg/openbox/autostart
```
```
# Disable any form of screen saver / screen blanking / power management
xset s off
xset s noblank
xset -dpms

# Allow quitting the X server with CTRL-ATL-Backspace
setxkbmap -option terminate:ctrl_alt_bksp

# Start Chromium in kiosk mode
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' ~/.config/chr>
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/; s/"exit_type">
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



#references:

https://die-antwort.eu/techblog/2017-12-setup-raspberry-pi-for-kiosk-mode/
