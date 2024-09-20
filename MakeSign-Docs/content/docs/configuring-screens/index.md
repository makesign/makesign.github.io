
---
title: 'Configuring-screens'
date: 2024-09-20
---

## Steps

1. First, decide if you want to run the digital signage screen via Wi-Fi (Eduroam/Easyroam) or Ethernet. If you choose to run it via Wi-Fi, the setup will be a little more difficult.
2. If you want to set up the screen using Wi-Fi, follow [Setup Using Wi-Fi (Eduroam/Easyroam)](#setup-using-eduroam-easyroam).
3. If you want to set up the screen using Ethernet, follow this guide.

If you can connect the screen via Ethernet, you save yourself the hassle of installing a custom GUI and managing the Easyroam certificates. To display the MakeSign URL, configure and use FullPage OS.

## Setup using FullPage OS {#setup-using-fullpage-os}

1. Download and install the Raspberry Pi Imager from [Raspberry Pi website](https://www.raspberrypi.com/software/).
    1. Insert the SD card into your computer.
    2. Open the Imager.
    3. Choose your device.
    4. For the OS, choose:
        Other general-purpose OS
        -> FullPage OS
    5. Choose your SD card.
    6. Click Next. A popup will appear; discard all edit configurations and flash the image to your card.
2. To set the MakeSign URL as the URL to be displayed, open `/boot/fullpageos.txt` in a text editor. It should show up in the boot volume when you attach the SD card to Windows, macOS, or Linux.
3. Connect the Pi to Ethernet and a screen. Your digital signage board should boot up as expected.

Congratulations! Your screen is set up :D

## Setup Using Eduroam/Easyroam {#setup-using-eduroam-easyroam}

### Setup Raspberry OS on Raspberry Pi

1. Download and install the Raspberry Pi Imager from [Raspberry Pi website](https://www.raspberrypi.com/software/).
    1. Insert the SD card into your computer.
    2. Open the Imager.
    3. Choose your device.
    4. For the OS, choose:	
        - Raspberry Pi OS (other)
        -> Raspberry Pi OS (Legacy, 64-Bit) Lite
    5. Choose your SD card.
    6. Click Next. A popup will appear; click Edit.
    7. Enter a hostname, password, and if you want to set up the Raspberry Pi remotely, also a Wi-Fi network. We used our phone's hotspot for this (temporary until Eduroam is set up). You can also skip this step, but setting up the Pi remotely is easier than on the hardware itself.
    8. Save and write the image to the SD card.

### Setup Eduroam/Easyroam

1. Generate a `.p12` file on the Easyroam website: [Easyroam Authentication](https://www.easyroam.de/Auth/Wayf?entityID=https://www.easyroam.de/shibboleth&return=https://www.easyroam.de/Shibboleth.sso/Login).
2. This file needs to be loaded onto the Raspberry Pi's SD card (you can load files on the Raspberry Pi via the SD card or wirelessly via a [wireless sftp connection to the Raspberry Pi from your Laptop](#connecting-to-raspberry-pi-via-sftp).
3. Execute these commands on the Raspberry Pi (replacing `my_easyroam_cert.p12` with the name of your `.p12` file):

    *Extract Client Certificate*
    ```bash
    openssl pkcs12 -in my_easyroam_cert.p12 -nokeys | openssl x509 > easyroam_client_cert.pem
    ```

    *Extract CN (Common Name)*
    ```bash
    openssl x509 -noout -subject -in easyroam_client_cert.pem | sed 's/.*CN=\(.*\), C.*/\1/' > CN
    ```

    *Extract Private Key (with password protection)*
    ```bash
    openssl pkcs12 -in my_easyroam_cert.p12 -nodes -nocerts | openssl rsa -aes256 -out easyroam_client_key.pem
    ```

    *Extract RootCA Certificate*
    ```bash
    openssl pkcs12 -in my_easyroam_cert.p12 -cacerts -nokeys > easyroam_root_ca.pem
    ```

    *View the .p12 File Details*
    ```bash
    openssl pkcs12 -info -in my_easyroam_cert.p12 -nodes
    ```

    *Create a Directory for Easyroam Certificates*
    ```bash
    sudo mkdir /etc/easyroam-certs
    sudo mv easyroam_client_cert.pem CN easyroam_client_key.pem easyroam_root_ca.pem /etc/easyroam-certs/.
    ```

    *Create `wpa_supplicant.conf` in `/etc/wpa_supplicant`*
    ```bash
    cat <<EOT | sudo tee /etc/wpa_supplicant/wpa_supplicant.conf > /dev/null
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    country=DE

    network={
       ssid="eduroam"
       scan_ssid=1
       key_mgmt=WPA-EAP
       proto=WPA2
       eap=TLS
       pairwise=CCMP
       group=CCMP
       identity="12345678910111213abcd@easyroam-pca.dfn.de"  # <---- Load the CN file with an editor like vim or vi
       altsubject_match="DNS:easyroam.eduroam.de"
       ca_cert="/etc/easyroam-certs/easyroam_root_ca.pem"
       client_cert="/etc/easyroam-certs/easyroam_client_cert.pem"
       private_key="/etc/easyroam-certs/easyroam_client_key.pem"
       private_key_passwd="secretphrase"
    }
    EOT
    ```

    *Reboot the System to Apply Changes*
    ```bash
    sudo reboot
    ```

### Connecting to Raspberry Pi Remotely {#connecting-to-raspberry-pi-via-sftp}

If you have connected the Raspberry Pi to the same network as your computer, you can access its terminal remotely via an SSH connection and copy files over via an SFTP connection.

#### Connecting to Raspberry Pi via SSH

(Your Pi and Computer need to be in the same Network)

1. Open Powershell (Windows) or Terminal (macOS, Linux).
2. Type:
    ```bash
    ssh username@Hostname
    ```
3. Enter the password. You are now connected to the Raspberry Pi's terminal with full access.
4. Type `exit` to disconnect.

#### Copying Files via SFTP

(Your Pi and Computer need to be in the same Network)

1. Open Powershell (Windows) or Terminal (macOS, Linux).
2. Type:
    ```bash
    sftp username@Hostname
    ```
3. Enter the password. You are now connected to the Raspberry Pi's SFTP with full access.
4. To upload a file from your computer:
    ```bash
    sftp> put _route_to_my_certificate.p12
    ```
5. Type `exit` to disconnect.

### Setup Custom GUI

1. Run `raspi-config`:
    ```bash
    sudo raspi-config
    ```
    Expand the file system (to have room for additional installations) and exit raspi-config.
2. Ensure you have a network connection and reboot:
    ```bash
    sudo reboot
    ```
3. Run the following commands:
    ```bash
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

4. Install X server and window manager:
    ```bash
    sudo apt-get install xserver-xorg
    sudo apt-get install xinit
    sudo apt-get install lxde-core lxterminal lxappearance
    sudo apt-get install lightdm
    sudo reboot
    ```

5. Install Chromium:
    ```bash
    sudo apt-get install chromium-browser
    sudo apt-get install -f
    ```

6. Install unclutter to remove the cursor:
    ```bash
    sudo apt-get install unclutter
    ```

7. Edit `/home/signage/.config/lxsession/LXDE/autostart`:
    ```bash
    sudo nano /home/signage/.config/lxsession/LXDE/autostart
    ```
    Remove the line:
    ```
    @xscreensaver –no-splash
    ```
    Add the following lines:
    ```
    @xset -dpms
    @xset s noblank
    unclutter –display :0 –noevents –grab
    chromium-browser https://MakeSign/url_to_your_screen --kiosk --disable-infobars --disable-session-crashed-bubble
    ```

8. Set Raspberry Pi to auto-start into GUI environment:
    ```bash
    sudo raspi-config
    ```
    Select System Options > Boot / Auto Login > Desktop Autologin: Desktop GUI, automatically logged in as 'your_username'.

9. Reboot:
    ```bash
    sudo reboot now
    ```

Now, when connecting the Pi to Ethernet and a screen, your digital signage board should boot up as expected.

Congratulations! Your screen is set up :D
