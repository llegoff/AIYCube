# AIYCube


Rasbian Configuration
=====================
SD Card preparation
-------------------

write last rasbian image on SD Card

create a file '/boot/wpa_supplicant.conf' to enable wifi connection

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
    
edit file '/boot/config.txt', add line
    
    #enable on/off button
    dtoverlay=gpio-shutdown
    
    #map keyboard key on button on GPIO
    #find keycode in https://github.com/torvalds/linux/blob/v4.12/include/uapi/linux/input-event-codes.h
    dtoverlay=gpio-key,gpio=16,keycode=16
    
    #enable TFT ILI9341 320x240
    dtparam=spi=on
    dtoverlay=pitft22,rotate=90,speed=64000000,fps=25
    #HDMI DTM mode
    hdmi_group=2
    #640x480 60hz
    hdmi_mode=4
    
    #enable TFT Touch
    dtoverlay=ads7846,xohms=80,pmax=255,penirq=24,swapxy
    
    # Enable audio on PI Zero (loads snd_bcm2835)
    dtoverlay=pwm-2chan,pin=18,func=2,pin2=19,func2=2
    dtparam=audio=on

Boot on SD Card , connect by SSH or console, and Update rasbian

    sudo apt-get update
    sudo apt-get upgrade
    
configure rasbian

    sudo raspi-config
change password    
  1 Change User Password

change hostname  
  2 Network Options
  N1 Hostname
  
change langue 
  4 Localisation Options
  I1 Change Locale
select your language and UTF8 

change time zone
  4 Localisation Options
  I2 Change Timezone

change Keyboard layout
  4 Localisation Options
  I3 Change Keyboard Layout
select your keyboard layout

change wifi country code
  4 Localisation Options
  I4 Change Wi-fi Country
select your country code

enable camera
  5 Interfacing Options
  P1 Camera

enable serial0 without console
  5 Interfacing Options
  P6 Serial
  No
  Yes
  
expande file system
  7 Advanced Options
  A1 Expand Filesystem
  
select finish and reboot



  
  
    
    
