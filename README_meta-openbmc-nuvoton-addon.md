# meta-openbmc-nuvoton-addon
This README file contains information on the contents of the meta-openbmc-nuvoton-addon layer.

Please see the corresponding sections below for details.

- Work with [openbmc tag v2.3](https://github.com/openbmc/openbmc/tree/v2.3 "openbmc tag v2.3")
- Work with [NTIL linux 4.13.05 for Poleg](https://github.com/Nuvoton-Israel/openbmc/tree/npcm7xx_v2.1 "NTIL")

# Dependencies
![](https://cdn.rawgit.com/NTC-CCBG/icons/73898d7f/label_openbmc_ver_2.3.svg) ![](https://cdn.rawgit.com/NTC-CCBG/icons/cb59d76c/label_linux_ver_4.13.05.svg)

This layer depends on:

```
  URI: git@github.com:openbmc/openbmc
  branch: master
```

# Contacts for Patches

Please submit any patches against the meta-openbmc-nuvoton-addon layer to the maintainer of nuvoton:

* Joseph Liu, <KWLIU@nuvoton.com>
* Medad CChien, <CTCCHIEN@nuvoton.com>
* Tyrone Ting, <KFTING@nuvoton.com>
* Stanley Chu, <YSCHU@nuvoton.com>
* Ray Lin, <CHLIN59@nuvoton.com>
* Tim Li, <CHLI30@nuvoton.com>

# How to apply this layer

** Adding the meta-openbmc-nuvoton-addon layer to your build. **

In order to use this layer, you need to make the build system aware of
it.

Assuming the meta-openbmc-nuvoton-addon layer exists at the top-level of your
yocto build tree, you can add it to the build system by adding the
location of the meta-openbmc-nuvoton-addon layer to bblayers.conf, along with any
other layers needed. e.g.:
```
BBLAYERS ?= " \
    /path/to/yocto/meta \
    /path/to/yocto/meta-poky \
    /path/to/yocto/meta-yocto-bsp \
    /path/to/yocto/meta-meta-openbmc-nuvoton-addon \
    "
```

# Table of Contents

- [Dependencies](#dependencies)
- [Contacts for Patches](#contacts-for-patches)
- [How to apply this layer](#how-to-apply-this-layer)
- [Enabled Features](#enabled-features)
  * [WebUI](#webui)
    + [OBMC iKVM](#obmc-ikvm)
    + [SOL](#sol)
    + [VM](#vm)
    + [Event Log Dump](#event-log-dump)
  * [System](#system)
    + [User Management](#user-management)
    + [Time](#time)
    + [Sensor](#sensor)
  * [IPMI / DCMI](#ipmi---dcmi)
    + [SOL](#sol-1)
    + [Message Bridging](#message-bridging)
- [IPMI Comamnds Verified](#ipmi-comamnds-verified)
- [Modifications](#modifications)

# Enabled Features

## WebUI

### OBMC iKVM

This is a Virtual Network Computing (VNC) server programm using [LibVNCServer](https://github.com/Nuvoton-Israel/libvncserver).

In order to support some hardware features of Nuvoton's NPCM750, please use our modified [LibVNCServer](https://github.com/Nuvoton-Israel/libvncserver) to build this program.
1. Add Video Capture and Differentiation(VCD), compares frame by hardware.
2. Add Encoding Compression Engine(ECE), 16-bit hextile compression hardware encoding.
3. Add USB HID, support Keyboard and Mouse.

**Source URL**

* [https://github.com/Nuvoton-Israel/obmc-ikvm](https://github.com/Nuvoton-Israel/obmc-ikvm)

**How to use**

1. launch obmc-ikvm
    ```
    ./obmc-ikvm &
    ```
    > _Please skip this step if obmc-ikvm daemon is managed by systemd_

2. open VNC viewer

    ```
    IP xxx.xxx.xxx.xxx
    port 5900
    ```
    > _Please choose hextile as preferred encoding._

**Maintainer**

* Joseph Liu

### SOL

The Serial over LAN (SoL) console redirects the output of the server’s serial port to a browser window on your workstation.

This is a patch for enabling SOL in [phosphor-webui](https://github.com/openbmc/phosphor-webui) on Nuvoton's NPCM750.

The patch provides the [obmc-console](https://github.com/openbmc/obmc-console) configuration and an update to [phosphor-rest-server](https://github.com/openbmc/phosphor-rest-server).

It's verified with Nuvoton's NPCM750 solution and Supermicro MBD-X9SCL-F-0.

**Source URL**

* [https://github.com/NTC-CCBG/meta-openbmc-nuvoton-addon/tree/master/recipes-phosphor/console](https://github.com/NTC-CCBG/meta-openbmc-nuvoton-addon/tree/master/recipes-phosphor/console)

* [https://github.com/NTC-CCBG/meta-openbmc-nuvoton-addon/tree/master/recipes-phosphor/interfaces](https://github.com/NTC-CCBG/meta-openbmc-nuvoton-addon/tree/master/recipes-phosphor/interfaces)

**How to use**

1. Prepare a Poleg EVB with up-to-date boot block, Uboot and OpenBMC versions with this SOL patch applied.  Check with Nuvoton support for the most recent versions.

2. Prepare a Supermicro MBD-X9SCL-F-0 motherboard and a LPC cable.
    > _The UEFI firmware version in Supermicro MBD-X9SCL-F-0 for verification is 2.15.1234._

3. Connect pins of the **JTPM** header on **Supermicro MBD-X9SCL-F-0** to the **J10** header on **Poleg EVB** with the LPC cable:
    * Connect **pin 1-3, 5, 7-8, 10-12, 15-17** of JTPM with corresponding pins of J10, **one on one**.
  
4. Steps to copy UEFI SOL related drivers to a USB drive.  

    * Format the USB drive in FAT or FAT32.  
    * Download PolegSerialDxe.efi and TerminalDxe.efi from  https://github.com/Nuvoton-Israel/openbmc-uefi-util/tree/npcm7xx_v2.1/sol_binary and copy them to the USB drive.

5. Power up the Poleg EVB and steps to prepare a working terminal for Poleg:

    * Download and install the USB-to-UART driver from: http://www.ftdichip.com/Drivers/VCP.htm according to the host OS in your workstation.  
    * Connect a micro usb cable from your workstation to J2 header of Poleg EVB.  
    * Wait for the FTDI driver to be installed automatically. The COM port number is assigned automatically.  
    * Open a terminal (e.g., Tera Term version 4.87) and set the correct COM port number assigned by the FTDI driver (in previous step).  
    * The COM port should be configured as follows: **115200, 8 bit, 1 stop-bit, no parity, no flow control**.  
    * Press and release the **PWR-ON-RST (SW3)** button to issue a Power-On-reset.  It's expected to see messages output by Poleg on the terminal. Use the following login name/password to login into Poleg.
        * Login name: **root**  
        * Login password: **0penBmc**  

6. Steps to configure Supermicro MBD-X9SCL-F-0 UEFI setting for SOL:

    * Do not plug any bootable device into Supermicro MBD-X9SCL-F-0.  
    * Power up Supermicro MBD-X9SCL-F-0 and boot into UEFI setting.  
    * Navigate to `Super IO Concifugration` in `Advanced` menu option and enter into `Super IO Concifugration`.  
    * Configure serial port 1 to **IO=3E8h; IRQ=5**, and then disable it.  
    * Go back to the main UEFI setting.  
    * Navigate to `Boot` menu option and select `UEFI: Built-in EFI Shell` as Boot Option #1.  
      + Make sure that the rest boot options are set to **Disabled**.  
    * Navigate to `Exit` menu option and select `Save changes and Reset`.  
    * Press `Yes` in the prompt window and it will reboot then.  
    * Wait for Supermicro MBD-X9SCL-F-0 to boot into UEFI shell.  
    * Plug the USB drive prepared in step-4 into Supermicro MBD-X9SCL-F-0's usb slot.  
    * Input the following command at UEFI shell prompt, press enter key and it will route to UEFI shell again.  
    * Check the device mapping table of the USB drive in UEFI shell. It is **fs0:** here for example.  
    * Input the following command at UEFI shell prompt, press enter key and the prompt will show **fs0:\>** from now.  
    * Input the following command at UEFI shell prompt and press the enter key.  
      ```
      fs0:>load PolegSerialDxe.efi  
	  ```
    * Input the following command at UEFI shell prompt and press the enter key.  
      ```
	  fs0:>load TerminalDxe.efi  
	  ```
    * Unplug the usb drive.  
    * Input the following command at UEFI shell prompt and it will route to the UEFI setting. 

7. Prepare a PC or use a virtual pc software to install Ubuntu 14.04, 64 bit on your workstation.  
    > _Boot Ubuntu and log in as a normal user._

8. Open a terminal in Ubuntu 14.04. Steps to install and execute software for SOL:

    * Install git:  
      + Input the following command in the opened terminal and press enter key:  
        ```
        sudo apt-get install git  
	    ```
    * Install Nodejs. 
      + Input the following command in the opened terminal and press enter key:  
        ```
	    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -  
	    ```
      + Input the following command in the opened terminal and press enter key:  
        ```
	    sudo apt-get install -y nodejs  
	    ```
      + install build tools. Input the following command in the opened terminal and press enter key:  
        ```
	    sudo apt-get install -y build-essential  
	    ```
    * Install phosphor-webui:  
      + Download phosphor-webui. Input the following command in the opened terminal and press enter key:  
        ```
	    git clone https://github.com/openbmc/phosphor-webui.git  
	    ```
      + Navigate your working path to the phosphor-webui folder in the opened terminal. Then input the following command in the opened terminal and press enter key:  
        ```
	    npm install  
	    ```
      + Run phosphor-webui. Input the following command in the opened terminal and press enter key:  
        ```
	    npm run-script server  
	    ```

9. Configure the ethernet communication between Ubuntu 14.04 and Poleg EVB:

    * Connect an ethernet cable between the PC running Ubuntu 14.04 and J7 header of Poleg EVB.  
    * Configure Ubuntu 14.04 ip address to 192.168.0.1 and the netmask to 255.255.255.0 as an example here.  
    * Configure Poleg EVB ip address to 192.168.0.2 and the netmask to 255.255.255.0. For example, input the following command in the terminal connected to Poleg EVB on your workstation and press enter key:  
      ```
	  ifconfig eth0 192.168.0.2 netmask 255.255.255.0
	  ```

10. Run SOL:

    * Launch a browser in Ubuntu 14.04, open a tab window and navigate to https://192.168.0.2.  
    * By pass the secure warning. You will see a JSON response with Login required message.  
    * In the same tab window, navigate to http://localhost:8080. Enter the BMC IP (which is 192.168.0.2 as an example here, Username and Password (defaults: **root/0penBmc**).  
    * You will see the OpenBMC management screen.  
    * Click `Server control` at the left side of the OpenBMC management screen.  
    * A `Serial over LAN console` menu item prompts then and click it.  
    * A specific area will display the UEFI setting of Supermicro MBD-X9SCL-F-0.  
    * (Optional) If the area doesn't display the UEFI setting clearly, use the mouse pointer to click in the area and press the **Esc** key.  
      + It shows a prompt window named `Exit Without Saving`, choose `No` and press enter key to refresh the area for showing UEFI setting entirely.

**Maintainer**

* Tyrone Ting
* Stanley Chu

### VM

### Event Log Dump

## System

### User Management
  * LDAP

### Time
  * **SNTP**  
  	Network Time Protocol (NTP) is a networking protocol for clock synchronization between computer systems over packet-switched, variable-latency data networks.
    
    **systemd-timesyncd** is a daemon that has been added for synchronizing the system clock across the network. It implements an **SNTP (Simple NTP)** client. This daemon runs with minimal privileges, and has been hooked up with **systemd-networkd** to only operate when network connectivity is available.
        
    The modification time of this file indicates the timestamp of the last successful synchronization (or at least the **systemd build date**, in case synchronization was not possible).
    > _/var/lib/systemd/timesync/clock_
    
    **Source URL**
    * [https://github.com/systemd/systemd/tree/master/src/timesync](https://github.com/systemd/systemd/tree/master/src/timesync)
    
    **How to use**  
    * Enable NTP  
      ```
      timedatectl set-ntp true  
      ```
      >_timedatectl result will show **systemd-timesyncd.service active: yes**_ 

      If NTP is Enabled and network is Connected (Using **eth2** connect to router), we will see the item **systemd-timesyncd.service active** is **yes** and **System clock synchronized** is **yes**. Thus, system time will sync from NTP server to get current time.
    * Get NTP status  
      ```
      timedatectl  
      ```
      >_Local time: Mon 2018-08-27 09:24:51 UTC  
      >Universal time: Mon 2018-08-27 09:24:51 UTC  
      >RTC time: n/a  
      >Time zone: n/a (UTC, +0000)  
      >**System clock synchronized: yes**  
      >**systemd-timesyncd.service active: yes**  
      >RTC in local TZ: no_  
      
    * Disable NTP  
      ```
      timedatectl set-ntp false  
      ```
      >_timedatectl result will show **systemd-timesyncd.service active: no**_  
      
    * Using Local NTP server Configuration  
    When starting, systemd-timesyncd will read the configuration file from **/etc/systemd/timesyncd.conf**, which looks like as below: 
        >_[Time]  
        >\#NTP=  
        >\#FallbackNTP=time1.google.com time2.google.com time3.google.com time4.google.com_  
	
    	By default, systemd-timesyncd uses the Google Public NTP servers **time[1-4].google.com**, if no other NTP configuration is available. To add time servers or change the provided ones, **uncomment** the relevant line and list their host name or IP separated by a space. For example, we setup NB windows 10 system as NTP server with IP **192.168.1.128**  
        >_[Time]  
        >**NTP=192.168.1.128**  
    	>\#FallbackNTP=time1.google.com time2.google.com time3.google.com time4.google.com_

    * Poleg connect to local NTP server of windows 10 system  
      Connect to NB through **eth0** EMAC interface, and set static IP **192.168.1.15**  

      ```
      ifconfig eth0 up
      ifconfig eth0 192.168.1.15
      ```  
      >_Note: Before that you need to setup your NTP server (192.168.1.128) on Windows 10 system first_  
      
      Modify **/etc/systemd/timesyncd.conf** file on Poleg as we mentioned
        >_[Time]  
        >**NTP=192.168.1.128**_  
      
      Re-start NTP to make effect about our configuration change  
      ```
      systemctl restart systemd-timesyncd.service
      ```  
      Check status of NTP that show already synced to our local time server 
      ```
      systemctl status systemd-timesyncd.service -l --no-pager
      ```  
      >_Status: "Synchronized to time server 192.168.1.128:123 (192.168.1.128)."_  
      
      Verify **Web-UI** `Server overview`->`BMC time` whether sync from NTP server as same as **timedatectl**. (Note: timedatectl time zone default is UTC, thus you will find the BMC time is UTC+8)
      ```
      timedatectl  
      ```
      >_Local time: Thu 2018-09-06 07:24:16 UTC  
      >Universal time: Thu 2018-09-06 07:24:16 UTC  
      >RTC time: n/a  
      >Time zone: n/a (UTC, +0000)  
      >System clock synchronized: yes  
      >systemd-timesyncd.service active: yes  
      >RTC in local TZ: no_  

### Sensor
  * Enabled Sensor Types
  * Event Generation

## IPMI / DCMI

### SOL

### Message Bridging
  * KCS to IPMB
  * Net to IPMB

# IPMI Comamnds Verified

| Command | KCS | RMCP+ | IPMB |
| :--- | :---: | :---: | :---: |
| **IPM Device Global Commands** |  |  |  |
| Device ID | V | V | V |
| Cold Reset |  |  |  |
| Warm Reset |  |  |  |
| Get Self Test Results |  |  |  |
| Manufacturing Test On |  |  |  |
| Set ACPI Power State |  |  |  |
| Get ACPI Power State |  |  |  |
| Get Device GUID |  |  |  |
| Get NetFn Support |  |  |  |
| Get Command Support |  |  |  |
| Get Command Sub-function Support |  |  |  |
| Get Configurable Commands |  |  |  |
| Get Configurable Command Sub-functions |  |  |  |
| Set Command Enables |  |  |  |
| Get Command Enables |  |  |  |
| Set Command Sub-function Enables |  |  |  |
| Get Command Sub-function Enables |  |  |  |
| Get OEM NetFn IANA Support |  |  |  |
| **BMC Watchdog Timer Commands** |  |  |  |
| Reset Watchdog Timer |  |  |  |
| Set Watchdog Timer |  |  |  |
| Get Watchdog Timer |  |  |  |
| **BMC Device and Messaging Commands** |  |  |  |
| Set BMC Global Enables |  |  |  |
| Get BMC Global Enables |  |  |  |
| Clear Message Flags |  |  |  |
| Get Message Flags |  |  |  |
| Enable Message Channel Receive |  |  |  |
| Get Message |  |  |  |
| Send Message | V |  |  |
| Read Event Message Buffer |  |  |  |
| Get System GUID |  |  |  |
| Set System Info Parameters |  |  |  |
| Get System Info Parameters |  |  |  |
| Get Channel Authentication Capabilities |  | V |  |
| Get Session Challenge |  |  |  |
| Activate Session |  |  |  |
| Set Session Privilege Level |  |  |  |
| Close Session |  |  |  |
| Get Session Info |  |  |  |
| Get AuthCode |  |  |  |
| Set Channel Access |  |  |  |
| Get Channel Access |  |  |  |
| Get Channel Info Command |  |  |  |
| User Access Command |  |  |  |
| Get User Access Command |  |  |  |
| Set User Name |  |  |  |
| Get User Name Command |  |  |  |
| Set User Password Command |  |  |  |
| Activate Payload |  | V |  |
| Deactivate Payload |  |  |  |
| Get Payload Activation Status |  |  |  |
| Get Payload Instance Info |  |  |  |
| Set User Payload Access |  |  |  |
| Get User Payload Access |  |  |  |
| Get Channel Payload Support |  |  |  |
| Get Channel Payload Version |  |  |  |
| Get Channel OEM Payload Info |  |  |  |
| Master Write-Read |  |  |  |
| Get Channel Cipher Suites |  |  |  |
| Suspend/Resume Payload Encryption |  |  |  |
| Set Channel Security Keys |  |  |  |
| Get System Interface Capabilities |  |  |  |
| Firmware Firewall Configuration |  |  |  |
| **Chassis Device Commands** |  |  |  |
| Get Chassis Capabilities |  |  |  |
| Get Chassis Status |  |  |  |
| Chassis Control |  |  |  |
| Chassis Reset |  |  |  |
| Chassis Identify |  | V |  |
| Set Front Panel Button Enables |  |  |  |
| Set Chassis Capabilities |  |  |  |
| Set Power Restore Policy |  |  |  |
| Set Power Cycle Interval |  |  |  |
| Get System Restart Cause |  |  |  |
| Set System Boot Options |  |  |  |
| Get System Boot Options |  |  |  |
| Get POH Counter |  |  |  |
| **Event Commands** |  |  |  |
| Set Event Receiver |  |  |  |
| Get Event Receiver |  |  |  |
| Platform Event |  |  |  |
| **PEF and Alerting Commands** |  |  |  |
| Get PEF Capabilities |  |  |  |
| Arm PEF Postpone Timer |  |  |  |
| Set PEF Configuration Parameters |  |  |  |
| Get PEF Configuration Parameters |  |  |  |
| Set Last Processed Event ID |  |  |  |
| Get Last Processed Event ID |  |  |  |
| Alert Immediate |  |  |  |
| PET Acknowledge |  |  |  |
| **Sensor Device Commands** |  |  |  |
| Get Device SDR Info |  |  |  |
| Get Device SDR |  |  |  |
| Reserve Device SDR Repository |  |  |  |
| Get Sensor Reading Factors |  |  |  |
| Set Sensor Hysteresis |  |  |  |
| Get Sensor Hysteresis |  |  |  |
| Set Sensor Threshold |  |  |  |
| Get Sensor Threshold |  |  |  |
| Set Sensor Event Enable |  |  |  |
| Get Sensor Event Enable |  |  |  |
| Re-arm Sensor Events |  |  |  |
| Get Sensor Event Status |  |  |  |
| Get Sensor Reading |  |  |  |
| Set Sensor Type |  |  |  |
| Get Sensor Type |  |  |  |
| Set Sensor Reading And Event Status |  |  |  |
| **FRU Device Commands** |  |  |  |
| Get FRU Inventory Area Info |  |  |  |
| Read FRU Data |  |  |  |
| Write FRU Data |  |  |  |
| **SDR Device Commands** |  |  |  |
| Get SDR Repository Info |  |  |  |
| Get SDR Repository Allocation Info |  |  |  |
| Reserve SDR Repository |  |  |  |
| Get SDR |  |  |  |
| Add SDR |  |  |  |
| Partial Add SDR |  |  |  |
| Delete SDR |  |  |  |
| Clear SDR Repository |  |  |  |
| Get SDR Repository Time |  |  |  |
| Set SDR Repository Time |  |  |  |
| Enter SDR Repository Update Mode |  |  |  |
| Exit SDR Repository Update Mode |  |  |  |
| Run Initialization Agent |  |  |  |
| **SEL Device Commands** |  |  |  |
| Get SEL Info |  |  |  |
| Get SEL Allocation Info |  |  |  |
| Reserve SEL |  |  |  |
| Get SEL Entry |  |  |  |
| Add SEL Entry |  |  |  |
| Partial Add SEL Entry |  |  |  |
| Delete SEL Entry |  |  |  |
| Clear SEL |  |  |  |
| Get SEL Time |  |  |  |
| Set SEL Time |  |  |  |
| Get Auxiliary Log Status |  |  |  |
| Set Auxiliary Log Status |  |  |  |
| Get SEL Time UTC Offset |  |  |  |
| Set SEL Time UTC Offset |  |  |  |
| **LAN Device Commands** |  |  |  |
| Set LAN Configuration Parameters |  |  |  |
| Get LAN Configuration Parameters |  |  |  |
| Suspend BMC ARPs |  |  |  |
| Get IP/UDP/RMCP Statistics |  |  |  |
| **Serial/Modem Device Commands** |  |  |  |
| Set Serial/Modem Mux |  |  |  |
| Set Serial Routing Mux |  |  |  |
| SOL Activating |  | V |  |
| Set SOL Configuration Parameters |  |  |  |
| Get SOL Configuration Parameters |  |  |  |
| **Command Forwarding Commands** |  |  |  |
| Forwarded Command |  |  |  |
| Set Forwarded Commands |  |  |  |
| Get Forwarded Commands |  |  |  |
| Enable Forwarded Commands |  |  | . |

# Modifications

* 2018.07.23 First release Remote-KVM
* 2018.08.02 First release SOL
* 2018.08.07 Modify Readme.md for adding description about SOL How to use
* 2018.08.13 Update vcd and ece patch, rename remote-kvm to obmc-ikvm
* 2018.09.10 Update System/Time/SNTP