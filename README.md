# AIYCube


## SD Card preparation

write last rasbian image on SD Card

create a file '/boot/wpa_supplicant.conf' to enable wifi connection
with content

    country=FR
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    network={
        ssid="ssid"
        psk="password"
    }

create empty file '/boot/ssh' to enable ssh session

edit file '/boot/cmdline' to disable console on serial0
remove

    console=serial0,115200

Boot on SD Card , connect by SSH or console, and Update rasbian

    sudo apt-get update
    sudo apt-get upgrade
    
configure rasbian

    sudo raspi-config
change password    
* 1 Change User Password

change hostname  
* 2 Network Options
* N1 Hostname
  
change langue 
* 4 Localisation Options
*I1 Change Locale
select your language and UTF8 

change time zone
* 4 Localisation Options
* I2 Change Timezone

change Keyboard layout
* 4 Localisation Options
* I3 Change Keyboard Layout
select your keyboard layout

change wifi country code
* 4 Localisation Options
* I4 Change Wi-fi Country
select your country code

enable camera
* 5 Interfacing Options
* P1 Camera

enable serial0 without console
* 5 Interfacing Options
* P6 Serial
* No
* Yes
  
expande file system
* 7 Advanced Options
* A1 Expand Filesystem
  
select finish and reboot

## Configure TFT

edit file '/boot/config.txt', add line

    #enable TFT ILI9341 320x240
    dtparam=spi=on
    dtoverlay=pitft22,rotate=90,speed=64000000,fps=25
    
    #640x480 60hz for framebuffer copy /2
    hdmi_force_hotplug=1
    hdmi_cvt=640 480 60 1 0 0 0
    hdmi_group=2
    hdmi_mode=87
    
    #320x240 60hz  for frame buffer copy
    #hdmi_force_hotplug=1
    #hdmi_cvt=320 240 60 1 0 0 0
    #hdmi_group=2
    #hdmi_mode=87

### Use TFT as second screen

### Use Frame Buffer copy
install fbcp

    sudo apt-get install cmake
    git clone https://github.com/tasanakorn/rpi-fbcp
    cd rpi-fbcp/
    mkdir build
    cd build/
    cmake ..
    make
    sudo install fbcp /usr/local/bin/fbcp
    
    cd ..
    cd ..
    rm -rf rpi-fbcp

load fbcp

    fbcp &
    
to run fbcp at startup, edit file '/etc/rc.local' , add before 'exit 0'

    until "/usr/local/bin/fbcp"; do sleep 1; done &

## Configure Touchscreen

edit file '/boot/config.txt', add line

    #enable TFT Touch
    dtoverlay=ads7846,penirq=24,swapxy=1,pmax=255,xohms=60
    
install 'xinput-calibrator' and 'xserver-xorg-input-evdev' 
    
    sudo apt-get install xinput-calibrator
    sudo apt-get install xserver-xorg-input-evdev
    sudo cp -rf /usr/share/X11/xorg.conf.d/10-evdev.conf /usr/share/X11/xorg.conf.d/45-evdev.conf

launch xinput_calibrator

    DISPLAY=:0 xinput_calibrator
    
after calibration, xinput_calibrator diplay information

        Setting calibration data: 0, 4095, 0, 4095
    Calibrating EVDEV driver for "ADS7846 Touchscreen" id=7
            current calibration values (from XInput): min_x=0, max_x=4095 and min_y=0, max_y=4095

    Doing dynamic recalibration:
            Setting calibration data: 335, 3927, 203, 3905
            --> Making the calibration permanent <--
      copy the snippet below into '/etc/X11/xorg.conf.d/99-calibration.conf' (/usr/share/X11/xorg.conf.d/ in some distro's)
    Section "InputClass"
            Identifier      "calibration"
            MatchProduct    "ADS7846 Touchscreen"
            Option  "Calibration"   "335 3927 203 3905"
            Option  "SwapAxes"      "0"
    EndSection

create file '/usr/share/X11/xorg.conf.d/99-calibration.conf' with content display by xinput_calibrator

    Section "InputClass"
            Identifier      "calibration"
            MatchProduct    "ADS7846 Touchscreen"
            Option  "Calibration"   "335 3927 203 3905"
            Option  "SwapAxes"      "0"
    EndSection

add a virtual keyboard

    sudo apt-get install matchbox-keyboard
 
## Configure audio on Raspberry Pi Zéro

edit file '/boot/config.txt', add line

    # Enable audio on PI Zero (loads snd_bcm2835)
    dtoverlay=pwm-2chan,pin=18,func=2,pin2=19,func2=2
    dtparam=audio=on

## Configure On/Off button

edit file '/boot/config.txt', add line
    
    #enable on/off button
    dtoverlay=gpio-shutdown

## Configure arcade button

edit file '/boot/config.txt', add line
    
    #map keyboard key on button on GPIO
    #find keycode in https://github.com/torvalds/linux/blob/v4.12/include/uapi/linux/input-event-codes.h
    dtoverlay=gpio-key,gpio=16,keycode=16,label="KEY_Q"

## Configure thermal printer ZJ-58

install cups 

    sudo apt-get install libcups2-dev libcupsimage2-dev git build-essential cups system-config-printer

get driver, make and install

    git clone https://github.com/adafruit/zj-58
    cd zj-58/
    make
    sudo ./install
    
Add printer 

    sudo lpadmin -p ZJ-58 -E -v serial:/dev/serial0?baud=9600 -m zjiang/ZJ-58.ppd
    sudo lpoptions –d ZJ–58



    
    

