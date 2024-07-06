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

3. **Now you can access the OpenMediaVault web GUI on a browser by entering the IP address of your Raspberry Pi in the search box.**

    The default login:
    - username: admin
    - password: openmediavault

      ![LoginPage](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/9e41e422-725e-4426-8466-d1285b1f1dbe)

    You can now plug in your USB storage device to the Pi.

4. **Mounting your storage device:**
      - Go to Storage -> File Systems -> Select the Mount button -> Select your storage device -> Save
     
        ![MountUSB1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/04e2dbe5-cf28-4f90-b49f-2a531cfd07f0)

      You should now be able to see your device.

5. **Shared Folders:**
      - Go to Storage -> Shared Folder -> Create ("+" sign)

        ![SharedFolders1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/86d24f6a-b416-4f20-9686-e11e1d64b87c)

      - Give a name to the shared folder, select your storage device and click on save.
      - Click on Privileges and give Read/Write permissions for both user accounts

6. **Enable SMB Service:**
      - Go to Services -> SMB/CIFS -> Settings and enable SMB

         ![SMBEnable1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/d0200955-f33b-413a-b02c-1fabb8e0bbfb)

      - Go to Services -> SMB/CIFS -> Shares -> Create ("+" sign) -> Select your shared folder and hit save.

        ![SMBShare1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/1302126d-df60-469c-beb3-c84da35334eb)


7. **Editing User:**
      - Go to Users -> Users -> select your user -> Click on edit (Pen icon) -> Give password and confirm password -> Save

After these steps, you can click on apply on the top right corner to apply all these changes.

![Apply1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/1234db91-378c-44bb-a6ac-85b79e781d88)

8. **Accessing the NAS on Windows:**
      - Go to This PC -> right-click -> Add a network location -> Next -> Next -> in the text box fill "\\\\[Pi ip address]\\[name of your share]" and click enter.
      - You should see a login dialog box, enter your username and password, click on remember my credentials, and click OK.

         ![WindowsAccess1](https://github.com/S-Vighnesh/Raspberry-Pi-Print-Server-and-NAS/assets/137196908/0d577d55-7e97-4077-a8b0-6aa6db707d44)

      Give a name to this network location if you want and click on next and finish. Now you should be able to see the network location on This PC.

### Configuring USB/IP

We can use a program called "CUPS" for Linux that shares the printer across the devices on the network but the problem with that software is that, we can't use the printer's application for printing; in my case it is Epson and it uses windows' default printing software which lacks many features that your printer's software offers. Because of this you can't utilize the full potential of your printer. Therefore, I have decided to use a program called USB/IP that shares USB devices between computers with their full functionality.
To achieve the setup where your Raspberry Pi acts as a wireless USB port for your desktop, making the printer available as if it were directly connected to your PC, you can use USB over IP. This method involves using a USB/IP server on your Raspberry Pi and a USB/IP client on your desktop. Here’s how you can do it:

1. **Install USB/IP on Raspberry Pi**
    
    1. SSH into Your Raspberry Pi:
       
       ```bash
       ssh pi@<raspberry_pi_ip>
       ```
    
    2. Update and Install Required Packages:
       
       ```bash
       sudo apt update
       sudo apt install usbip usbip-utils
       ```
    
    3. Load Kernel Modules:
       
       ```bash
       sudo modprobe usbip-core
       sudo modprobe usbip-host
       ```
    
    4. Ensure Modules Load at Boot:
       
       ```bash
       echo 'usbip-core' | sudo tee -a /etc/modules
       echo 'usbip-host' | sudo tee -a /etc/modules
       ```
    
    5. Start and Enable the USB/IP Daemon:
       
       ```bash
       sudo systemctl start usbipd
       sudo systemctl enable usbipd
       ```
    
    6. Find and Bind the Printer:

       - List USB devices to find your printer’s bus ID:
       
         ```bash
         usbip list -l
         ```

       - Bind the printer to USB/IP (replace `<busid>` with the actual bus ID of your printer, e.g., `1-1.2`):
       
         ```bash
         sudo usbip bind -b <busid>
         ```

2. **Install USB/IP Client on Windows PC**
    
    1. Download USB/IP for Windows:
       - Download the latest USB/IP Windows driver and client from the [official USB/IP Windows repository](https://github.com/cezanne/usbip-win).
    
    2. Extract the zip file
    
    3. Run the "usbip_test.pfx" file:
       - Select Local Maching -> Next -> Yes -> Next -> Password: usbip -> Next -> Next -> Okay
    
    4. Connect to the Raspberry Pi USB/IP Server:
       - Open a command prompt with administrator privileges.
       - Change directory to the folder which we unzipped and where the "usbip.exe" file is available

         ```bash
         cd <"path">
         ```
       
       - Install usbip.exe
       
         ```bash
         ./usbip.exe install
         ```
       
       - List available USB devices on the Raspberry Pi (replace `<raspberry_pi_ip>` with your Raspberry Pi's IP address):
       
         ```bash
         ./usbip.exe list -r <raspberry_pi_ip>
         ```
       
       - Attach the printer to your Windows machine (replace `<busid>` with the bus ID from the previous step):
       
         ```bash
         ./usbip.exe attach -r <raspberry_pi_ip> -b <busid>
         ```

3. **Connect and Disconnect the Printer**

    - Connecting the Printer
    
    1. On the PC, open a command prompt with administrator privileges.
    2. Attach the printer to your PC:

       ```bash
       ./usbip.exe attach -r <raspberry_pi_ip> -b <busid>
       ```
    
    - Disconnecting the Printer
    
    1. On the PC, open a command prompt with administrator privileges.
    2. Detach the printer from your PC:

       ```bash
       ./usbip.exe detach -p <port number (usually 0)>
       ```

4. **Automating Connections with Scripts**

    To simplify connecting and disconnecting, you can create scripts on each PC.
    
    - **Connection Script**
 
   Create a script to connect the printer to the PC.
        
   For Windows:
   
   ```bat
          @echo off
        
          "[Location of the usbip.exe file]\usbip.exe" attach -r <raspberry_pi_ip> -b <busid>
            
          pause
   ```
    
    - **Disconnection Script**
    
    Create a script to disconnect the printer from the PC.
    
   For Windows:
   
   ```bat
          @echo off
        
          "<Location of the usbip.exe file>\usbip.exe" detach -p <port number (usually 0)>
        
          pause
        
   ```
Save this text files separately as ".bat" files. For ease, I had saved the first script as "attach_printer.bat" and the second script as "detach_printer.bat".
Now, running the attach script automatically attaches your printer to the PC and you can start printing like you would do normally when the printer is attached directly to your PC. 
You can run the detach script to disconnect the printer from the PC.
You can follow these steps on all your PCs and keep these files handy on those PCs.
