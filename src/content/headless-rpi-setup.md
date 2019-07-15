---
title: Raspberry Pi Headless Setup
date: "2019-07-14"
draft: false
path: "/blog/headless-rpi-setup"
---

Earlier this year at a hackathon, my team and I worked on a pretty cool project involving a Raspberry Pi. Having little hardware experience, one of our main obstacles was actually setting up the device without a keyboard, mouse, or display - this is what's referred to as a "headless" install. This post will go over a bare-minimum headless install process. Countless guides like this exist already, so I will also write about some of the unique challenges our team faced with the setup to make it more useful to those using a Raspberry Pi as part of their Hackathon project.

### 1. Set Up Rapsbian OS

The first thing you want to do is prepare your SD card by wiping its contents. When working on a hackathon project, you want to make sure your process is clean and all the steps are as reproduceable as possible from start to finish, in case things go wrong and you need to start over. You certainly want to do this if you're loaning your Raspberry Pi from the hackathon, since you don't know what might be on the SD card from the previous user.

- Download and install the [SD Memory Card Formatter](https://www.sdcard.org/downloads/formatter/eula_mac/index.html).
- Insert your SD card and open the application.
- Select the SD card from the drop down, under formatting options select **Overwrite format**, then hit **Format**.
- Download the [latest version of Raspbian](https://www.raspberrypi.org/downloads/raspbian/). Any of the three options are fine, it's up to your own preference. I usually go with the standard _Raspbian with Desktop_.
- Download and install [Balena Etcher](https://www.balena.io/etcher/).
- Open Etcher, select the Raspbian .zip file as the image, and the SD card as the image, then click **Flash**.

At this point, your Raspberry Pi will be able to boot Raspbian OS with this SD card. The problem is, if you're lacking a keyboard,mouse, or display, you can't really do much. This next section will guide you through getting the Raspberry Pi connected to the wifi automatically so you can SSH into it directly from your machine.

### 2. Connect to a network and enable SSH on boot

- In the terminal, navigate to the SD card's boot directory and create a file called `ssh`
  ```shell
  $ cd /Volumes/boot
  $ sudo touch ssh
  ```
  The presence of this file will enable SSH automatically on boot, after which it will be deleted.
- Create a `wpa_supplicant.conf` file in the same directory.
  ```shell
  $ sudo vim wpa_supplicant.conf
  ```
- Copy and paste the following, replacing _networkname_ and _password_ with the appropriate values:

  ```shell
  country=US
  ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
  update_config=1

  network={
  ssid="networkname"
  scan_ssid=1
  psk="password"
  key_mgmt=WPA-PSK
  }
  ```

This file will tell the Raspberry Pi which network to connect to on boot. Keep in mind that Raspberry Pi models 3 and below can only connect to 2.4G networks. To connect to 5G you'll either need a wifi dongle or a Raspberry Pi 4.

### 3. Boot and SSH to Raspberry Pi

You can now insert the SD card into the Raspberry Pi and power it on, and it should connect to the wifi network right away.

- To SSH into the Pi:
  ```shell
  $ ssh pi@raspberrypi.local
  ```