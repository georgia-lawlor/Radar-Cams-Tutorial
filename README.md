# Radar-Cams-Tutorial
Raspberry Pi MotioneyeOS Continuous monitoring system with a Wifi-forwarding Pi to provide internet to an isolated router with no ethernet access


# Setting Up MotionEyeOS Cameras
Adapted from [CCTV Raspberry Pi Based System with Storage using MotionEyeOS](https://randomnerdtutorials.com/cctv-raspberry-pi-based-system-storage-motioneyeos/) by Julian Silver. I recommend looking at his instructions for additional information related to other Raspberry Pi Versions and camera types. 
## Required Parts
###### Hub
- 1x [Raspberry Pi 3B+](https://www.raspberrypi.com/products/raspberry-pi-3-model-b-plus/) (other models (4B/3B+/3B/2B) should work but this is what I used)
- 1x [MicroSD Card](https://www.amazon.com/Sandisk-Ultra-Micro-UHS-I-Adapter/dp/B073K14CVB/ref=asc_df_B073K14CVB/?tag=&linkCode=df0&hvadid=309779531175&hvpos=&hvnetw=g&hvrand=10334283790332508896&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9007908&hvtargid=pla-374265929034&region_id=373786&ref=&adgrpid=62412137260&th=1) - at least 8GB
- 1x Memory Storage Option - I recommend at least 1TB for video, if using physical memory, need a cable from the drive to USB 2.0
  - SATA Drive and SATA to USB 2.0 cable (I had [WD Elements Portable External 2TB Hard Drive USB 3.0](https://www.westerndigital.com/products/portable-drives/wd-elements-portable-usb-3-0-hdd#WDBU6Y0020BBK-WESN))
  - SSD
  - Cloud Account (list)
- 1x 5V power supply capable of powering both the Raspberry Pi and the SATA drive separately

###### Cameras
- 4x [Raspberry Pi 3B+](https://www.raspberrypi.com/products/raspberry-pi-3-model-b-plus/) (other models (Zero W/4B/3B+/3B/2B) should work but this is what I used)
- 4x [MicroSD Card](https://www.amazon.com/Sandisk-Ultra-Micro-UHS-I-Adapter/dp/B073K14CVB/ref=asc_df_B073K14CVB/?tag=&linkCode=df0&hvadid=309779531175&hvpos=&hvnetw=g&hvrand=10334283790332508896&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9007908&hvtargid=pla-374265929034&region_id=373786&ref=&adgrpid=62412137260&th=1) - at least 8GB
- 4x MakerFocus Fisheye Camera 5mp IR-CUT Night-vision Camera for Raspberry Pi4B/3B+/3B/2B
[MakerFocus](https://www.makerfocus.com/collections/rpi-cameras/products/fisheye-camera-5mp-ir-cut-night-vision-compatible-for-raspberry-pi4b-3b-3b-2b) or [Amazon](https://www.amazon.com/gp/product/B07DPNDP6R/ref=ppx_yo_dt_b_asin_title_o08_s00?ie=UTF8&psc=1)
- 4x Micro USB to USB 2.0 cable

## Installing MotionEyeOS on the Pis

### MotionEyeOS Image
1. Download the right image for your device: [MotionEyeOS Release Page](https://github.com/motioneye-project/motioneyeos/releases)
  - I recommend using the `Latest` build. Click `Assets > Show all X assets` for all the options. 
  - I used `motioneyeos-raspberrypi3-xxxxxxxx.img.gz`.
2. Extract the image from the compressed `.gz` folder - extension should be `.img`
3. Use [BalenaEtcher](https://www.balena.io/etcher#download-etcher) or equivalent to write the image onto the 5 SD cards. 



# Setting Up Wifi Forwarding Pi
https://gist.github.com/Konamiman/110adcc485b372f1aff000b4180e2e10
## Setup Pi
1. Download the Raspbian image (Raspbian Lite is fine)
2. Extract the image from the compressed `.gz` folder - extension should be `.img`
3. Flash the image file in the SD card using [BalenaEtcher](https://www.balena.io/etcher#download-etcher) or equivalent. 

Then you have two equivalent options to finish setting up the pi
### Option 1
1. Reinsert the SD card in your computer and navigate to the SD card
2. Create an empty file named ssh (withot any extension) to enable SSH.
3. Create a file named wpa_supplicant.conf with the details of your WiFi access point as follows:
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=<ISO code of your country>
# Simple network connection
network={
  scan_ssid=1
  ssid="<the name of your WiFi network>"
  psk="<the password for your WiFi network>"
}
# Complex network connection when a username and password are required to log in
network={
    ssid="XXXXX"
    scan_ssid=1
    key_mgmt=WPA-EAP
    group=CCMP TKIP
    eap=PEAP
    identity="XXXXX@XXX.edu"
    password="XXXXX"
    phase1="peapver=0"
    phase2="MSCHAPV2"
}
```
You only need 1 of the 2 `network{...}` options above. Furthermore you can include multiple if you have multiple networks. 
4. Insert the SD card in your Pi and let it boot. Wait one minute or so.

### Option 2
1. Insert the SD card into the pi and connect a monitor and keyboard.
2. Let the pi boot up for a minute. 
3. When prompted, enter location, keyboard configuration, pi username and password.
4. Once booted and see raspberrypi login: login using previously set credentials
5. Open a file to write the details of your WiFi access point as follows:
```
cd /etc/wpa_supplicant/wpa_supplicant.conf
```
and fill it with
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=<ISO code of your country>

network={
  scan_ssid=1
  ssid="<the name of your WiFi network>"
  psk="<the password for your WiFi network>"
}
  
network={
    ssid="XXXXX"
    scan_ssid=1
    key_mgmt=WPA-EAP
    group=CCMP TKIP
    eap=PEAP
    identity="XXXXX@XXX.edu"
    password="XXXXX"
    phase1="peapver=0"
    phase2="MSCHAPV2"
}
```
You only need 1 of the 2 `network{...}` options above. Furthermore you can include multiple if you have multiple networks. 
6. Restart the pi
```
sudo reboot -h now
```
7. login again
8. Setup the system settings
```
sudo raspi-config
```
This will open a user interface. Go through the following instructions to set up.
```
System options
  > S1 Wireless LAN
    - Set country
    - If wifi is simple (only ssid and password), enter information, otherwise select cancel
  > Hostname - On motioneye interface, this device will show up as `raspberrypi` unless changed. I set it up the same as the pi login username.
Interface Options
  > SSH - enable SSH by selecting <Yes>
Localization Options
  > Timezone - select correct time zone
Update
```
Once finished, navigate to <Finish>.
9. Find the ip address
```
ifconfig
```
Which will produce an output that looks like this:
```
hostname@username:~ $ ifconfig
eth0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        ether b8:27:eb:98:e1:d8  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 10  bytes 1588 (1.5 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 10  bytes 1588 (1.5 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlan0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.203.12.122  netmask 255.255.0.0  broadcast 10.203.255.255
        inet6 fe80::700f:e355:f9f6:12f1  prefixlen 64  scopeid 0x20<link>
        ether b8:27:eb:cd:b4:8d  txqueuelen 1000  (Ethernet)
        RX packets 174356  bytes 224511540 (214.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 62741  bytes 5941341 (5.6 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
10. If everthing went well, you should see an `inet  XXX.XXX.XXX.XXX  netmask 255.255.0.0  broadcast XXX.XXX.255.255` line. This means you have wifi internet and you should be able to SSH into your pi. 

## Update System
1. Update system (this might take a while)
```
sudo apt-get update -y
sudo apt-get upgrade -y
``` 
## Set up wifi routing
1. Reboot pi (`sudo reboot -h now`) and plug its ethenet cable the "input" spot of a router. Power router on. 
2. `sudo nano /etc/dhcpcd.conf` find, uncomment, and edit
```
interface eth0
static ip_address=192.168.87.1/24
```
3. 
```
sudo apt-get install isc-dhcp-server
```
4. `sudo nano /etc/dhcp/dhcpd.conf'
Find and uncomment
```
authoritative;
```
Add
```  
subnet 192.168.87.0 netmask 255.255.255.0 {
 range 192.168.87.10 192.168.87.250;
 option broadcast-address 192.168.87.255;
 option routers 192.168.87.1;
 default-lease-time 600;
 max-lease-time 7200;
 option domain-name "local-network";
 option domain-name-servers 8.8.8.8, 8.8.4.4;
}
```
5. `/etc/default/isc-dhcp-server` find and edit
```
INTERFACESv4="eth0"
```
6. sudo nano ~/router
```
echo Starting DHCP server
sudo service isc-dhcp-server start

echo Setting NAT routing
sudo iptables -t nat -A POSTROUTING -o wlan0 -j MASQUERADE
sudo iptables -A FORWARD -i wlan0 -o eth0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i eth0 -o wlan0 -j ACCEPT

DEFAULT_IFACE=`route -n | grep -E "^0.0.0.0 .+UG" | awk '{print $8}'`
if [ "$DEFAULT_IFACE" != "wlan0" ]
then
  GW=`route -n | grep -E "^0.0.0.0 .+UG .+wlan0$" | awk '{print $2}'`
  echo Setting default route to wlan0 via $GW
  route del default $DEFAULT_IFACE
  route add default gw $GW wlan0
fi
```
7. `sudo chmod +x ~/router`
8. `crontab -e` and add
```
@reboot sudo /home/<hostname>/router
```
9. `./router` You should only see this as output.
```
Starting DHCP server
Setting NAT routing
```
#### Debugging
- All these files you are adding code to (other than ~/router) should have text in them upon opening. If you get a blank file, check the filepath/name you typed in. 
- If you get a message that the isc_dhcp_server failed, run `sudo systemctl status isc-dhcp-server.service`
You will get an output that has `Main PID: ####`. There will also be timestamped messages that end with [####]. If any of those numbers are different from the Main PID and not 1, you can check those too.  
run `journalctl _PID=####` to debug error messages

# Install MotionEye for Bullseye

Follow instructions on [motioneye-project](https://github.com/motioneye-project/motioneye/wiki/Install-on-Raspbian-Bullseye)
EXCEPT USE THESE COMMANDS
sudo wget https://github.com/Motion-Project/motion/releases/download/release-4.5.0/pi_bullseye_motion_4.5.0-1_armhf.deb
sudo dpkg -i pi_bullseye_motion_4.5.0-1_armhf.deb
 
after finishing, go to browser on computer connected to the same wifi and type in <ipaddress>:8765
Log in with admin, no password
Add an admin password and create surveilance username and password. Hit the orange apply button. 
Log in with new credentials. 


sudo apt-get install samba-common smbclient samba-common-bin smbclient cifs-utils
sudo ifconfig eth0 down
