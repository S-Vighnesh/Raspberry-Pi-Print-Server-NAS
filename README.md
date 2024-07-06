# Raspberry-Pi-Print-Server-and-NAS

This project sets up a Raspberry Pi to serve as both a print server and a NAS (Network Attached Storage), making a wired printer accessible wirelessly and sharing files across the network.

## Table of Contents
1. [Introduction](#introduction)
2. [Prerequisites](#prerequisites)
3. [Installation](#installation)
    - [Setting up the Raspberry Pi](#setting-up-the-raspberry-pi)
    - [Setting up OpenMediaVault for NAS](#Setting-up-OpenMediaVault-for-NAS)
    - [Configuring USB/IP](#configuring-usbip)
    - [Setting up Samba for NAS](#setting-up-samba-for-nas)
4. [Usage](#usage)
    - [Connecting to the Printer](#connecting-to-the-printer)
    - [Disconnecting from the Printer](#disconnecting-from-the-printer)
    - [Accessing NAS](#accessing-nas)
5. [Automation](#automation)
6. [Troubleshooting](#troubleshooting)
7. [Contributing](#contributing)
8. [License](#license)

## Introduction

This project transforms a Raspberry Pi into a multifunctional device, allowing you to:
- Wirelessly print from any computer on the network.
- Share files via a NAS setup.

## Prerequisites

- Raspberry Pi with Raspberry Pi OS installed (I have installed Raspberry Pi OS Lite 32-bit version on my Raspberry Pi Model B v1.2)
- Wireless router
- USB printer (e.g., Epson L380)
- USB storage device (e.g., a pendrive, External HDD or SSD)

## Installation

### Setting up the Raspberry Pi

Firstly, prep your Pi by installing the OS for it on an SD Card. You can use any Raspberry Pi, but if you want this device to work wirelessly, make sure your Pi supports WiFi or else you can connect it to the router directly using an Ethernet cable. You can use Raspberry Pi OS of your choice; I have used Raspberry Pi OS Lite 32-bit version for my Raspberry Pi Model B v1.2.

While installing the OS on the SD card, configure the SSH settings as well as the WiFi settings, or else you can connect a Pi to a monitor, keyboard, and mouse directly. Once we have got all this done and the Pi running, we can install the necessary programs required for this project with the help of the terminal.

1. **Update and upgrade the system:**
    ```sh
    sudo apt update && sudo apt upgrade -y
    ```
    This command ensures your system has the latest information on available packages and their versions before installing or upgrading packages.

### Setting up OpenMediaVault for NAS

1. **Install OpenMediaVault:**
    ```sh
    wget -O - https://raw.githubusercontent.com/OpenMediaVault-Plugin-Developers/installScript/master/install | sudo bash
    ```
    This command will install OpenMediaVault on your Pi. Using this software, you can easily configure your storage devices (e.g., a pendrive, External HDD, or SSD) to this NAS via a web graphical web interface. After the execution of this command, your Pi will reboot so you have to log in to your Pi again.

2. **Find IP of the Pi:**
    ```sh
    ifconfig
    ```
    
    **Now you can access the OpenMediaVault web GUI on a browser by entering the IP address of your Raspberry Pi in the search box.**

    The default login:
    - username: admin
    - password: openmediavault

      ![LoginPage](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/9e41e422-725e-4426-8466-d1285b1f1dbe)

    You can now plug in your USB storage device to the Pi.

    - **Mounting your storage device:**
      - Go to Storage -> File Systems -> Select the Mount button -> Select your storage device -> Save
     
        ![MountUSB1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/04e2dbe5-cf28-4f90-b49f-2a531cfd07f0)

      You should now be able to see your device.

    - **Shared Folders:**
      - Go to Storage -> Shared Folder -> Create ("+" sign)
        ![SharedFolders1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/86d24f6a-b416-4f20-9686-e11e1d64b87c)

      - Give a name to the shared folder, select your storage device and click on save.
      - Click on Privileges and give Read/Write permissions for both user accounts

    - **Enable SMB Service:**
      - Go to Services -> SMB/CIFS -> Settings and enable SMB
        ![SMBEnable1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/d0200955-f33b-413a-b02c-1fabb8e0bbfb)

      - Go to Services -> SMB/CIFS -> Shares -> Create ("+" sign) -> Select your shared folder and hit save.
        ![SMBShare1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/1302126d-df60-469c-beb3-c84da35334eb)


    - **Editing User:**
      - Go to Users -> Users -> select your user -> Click on edit (Pen icon) -> Give password and confirm password -> Save

    After these steps, you can click on apply on the top right corner to apply all these changes.
   ![Apply1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/1234db91-378c-44bb-a6ac-85b79e781d88)

    - **Accessing the NAS on Windows:**
      - Go to This PC -> right-click -> Add a network location -> Next -> Next -> in the text box fill "\\\\[Pi ip address]\\[name of your share]" and click enter.
      - You should see a login dialog box, enter your username and password, click on remember my credentials, and click OK.
        ![WindowsAccess1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/0d577d55-7e97-4077-a8b0-6aa6db707d44)

      Give a name to this network location if you want and click on next and finish. Now you should be able to see the network location on This PC.
