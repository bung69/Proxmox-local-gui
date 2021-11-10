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

Edit openbox autostart

```
nano /etc/xdg/openbox/autostart
```

replace with:

```
# Disable any form of screen saver / screen blanking / power management
#xset s off
#xset s noblank
#xset -dpms

# Allow quitting the X server with CTRL-ATL-Backspace
setxkbmap -option terminate:ctrl_alt_bksp

# Start Chromium in kiosk mode
#sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' ~/.config/chr>
#sed -i 's/"exited_cleanly":false/"exited_cleanly":true/; s/"exit_type">
chromium https://127.0.0.1:8006 --kiosk --window-size=1920,1080 --incognito --noerrdialogs --disable-translate --no-first-run --fast --fast-start --disable-infobars --disable-features=TranslateUI --disk-cache-dir=/dev/null --password-store=basic

```




# References:

https://die-antwort.eu/techblog/2017-12-setup-raspberry-pi-for-kiosk-mode/
