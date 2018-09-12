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
    + [SOL IPMI](#sol-ipmi)
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

The patch provides the [obmc-console](https://github.com/openbmc/obmc-console) configuration.

It's verified with Nuvoton's NPCM750 solution (which is referred as Poleg here) and Supermicro MBD-X9SCL-F-0.

**Source URL**

* [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-phosphor/console](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-phosphor/console)

* [https://github.com/Nuvoton-Israel/openbmc-uefi-util/tree/npcm7xx_v2.1/sol_binary](https://github.com/Nuvoton-Israel/openbmc-uefi-util/tree/npcm7xx_v2.1/sol_binary)

**How to use**

1. Prepare a Poleg EVB with up-to-date boot block, Uboot and OpenBMC versions with this SOL patch applied.  Check with Nuvoton support for the most recent versions.

2. Prepare a Supermicro MBD-X9SCL-F-0 motherboard and a LPC cable.
    > _The UEFI firmware version in Supermicro MBD-X9SCL-F-0 for verification is 2.15.1234._

3. Connect pins of the **JTPM** header on **Supermicro MBD-X9SCL-F-0** to the **J10** header on **Poleg EVB** with the LPC cable:
    * Connect **pin 1-3, 5, 7-8, 10-12, 15-17** of JTPM with corresponding pins of J10, **one on one**.
  
4. Steps to copy UEFI SOL related drivers to a USB drive.  

    * Format the USB drive in FAT or FAT32.  
    * Download PolegSerialDxe.efi and TerminalDxe.efi from  [https://github.com/Nuvoton-Israel/openbmc-uefi-util/tree/npcm7xx_v2.1/sol_binary](https://github.com/Nuvoton-Israel/openbmc-uefi-util/tree/npcm7xx_v2.1/sol_binary) and copy them to the USB drive.

5. Power up the Poleg EVB and steps to prepare a working terminal for Poleg:

    * Download and install the USB-to-UART driver from: [http://www.ftdichip.com/Drivers/VCP.htm](http://www.ftdichip.com/Drivers/VCP.htm) according to the host OS in your workstation.  
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
      ```
      exit  
      ```
    * Check the device mapping table of the USB drive in UEFI shell. It is **fs0:** here for example.  
    * Input the following command at UEFI shell prompt, press enter key and the prompt will show **fs0:\>** from now.  
      ```
      fs0:  
	    ```
    * Input the following command at UEFI shell prompt and press the enter key.  
      ```
      load PolegSerialDxe.efi  
      ```
    * Input the following command at UEFI shell prompt and press the enter key.  
      ```
      load TerminalDxe.efi  
	    ```
    * Unplug the usb drive.  
    * Input the following command at UEFI shell prompt, press the enter key and it will route to the UEFI setting. 
      ```
      exit  
      ```

7. Configure the ethernet communication between your workstation and Poleg EVB:

    * Connect an ethernet cable between your workstation and J7 header of Poleg EVB.  
    * Configure your workstation's ip address to 192.168.0.1 and the netmask to 255.255.255.0 as an example here.  
    * Configure Poleg EVB ip address to 192.168.0.2 and the netmask to 255.255.255.0. For example, input the following command in the terminal connected to Poleg EVB on your workstation and press enter key.  
      ```
	  ifconfig eth0 192.168.0.2 netmask 255.255.255.0
	  ```

8. Run SOL:

    * Please disable the proxy setting for this test if it's configured.
    * Launch a browser in your workstation and navigate to https://192.168.0.2.  
    * By pass the secure warning and continue to the website.  
    * Enter the BMC Username and Password (defaults: **root/0penBmc**).  
    * You will see the OpenBMC management screen.  
    * Click `Server control` at the left side of the OpenBMC management screen.  
    * A `Serial over LAN console` menu item prompts then and click it.  
    * A specific area will display the UEFI setting of Supermicro MBD-X9SCL-F-0.  
    * (Optional) If the area doesn't display the UEFI setting clearly, use the mouse pointer to click in the area and press the **Esc** key.  
      + It shows a prompt window named `Exit Without Saving`, choose `No` and press enter key to refresh the area for showing UEFI setting entirely.  
    * Please enable the proxy setting if it's just disabled for the test.

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

### SOL IPMI

The Serial over LAN (SoL) via IPMI redirects the output of the server’s serial port to a command/terminal window on your workstation.

The user uses the ipmi tool like [ipmiutil](http://ipmiutil.sourceforge.net/) to interact with SOL via IPMI. Here the [ipmiutil](http://ipmiutil.sourceforge.net/) is used as an example.

This is a patch for enabling SOL via IPMI using [phosphor-net-ipmid
](https://github.com/openbmc/phosphor-net-ipmid) on Nuvoton's NPCM750.

The patch integrates [phosphor-net-ipmid](https://github.com/openbmc/phosphor-net-ipmid) into Nuvoton's NPCM750 solution for OpenBMC.

It's verified with Nuvoton's NPCM750 solution (which is referred as Poleg here) and Supermicro MBD-X9SCL-F-0.

**Source URL**

* [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-phosphor/image](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-phosphor/image)

**How to use**

1. Please follow instructions from step-1 to step-7 in [SOL](#sol) **How to use** section to configure your workstation, NPCM750 solution and Supermicro MBD-X9SCL-F-0.

2. Download the [ipmiutil](http://ipmiutil.sourceforge.net/) according to the host OS in your workstation.
   > _Here it's assumed that the host OS is Windows 7 and ipmiutil for Windows is downloaded and used accordingly._

3. Run SOL:

    * Extract or install the ipmiutil package to a folder in your workstation in advance.  
    * Launch a command window and navigate to that folder.  
    * Input the following command in the command window.  
      ```
      ipmiutil sol -N 192.168.0.2 -U admin -P 0penBmc -J 3 -V 4 -a  
      ```
    * (Optional) If the area doesn't display the UEFI setting clearly, the user could press the **Esc** key once.  
      + It shows a prompt window named `Exit Without Saving`, choose `No` and press enter key to refresh the area for showing UEFI setting entirely.  
    * (Optional) Configure the `Properties` of the command window  to see the entire output of SOL.  
      > _Screen Buffer Size Width: 200_  
        _Screen Buffer Size Height: 400_  
        _Window Size Width: 100_  
        _Window Size Height: 40_

4. End SOL session:

    * Press the "`" key (using the shift key) and "." key at the same time in the command window.  
    * Input the following command in the command window.  
      ```
      ipmiutil sol -N 192.168.0.2 -U admin -P 0penBmc -J 3 -V 4 -d  
      ```

**Maintainer**

* Tyrone Ting
* Stanley Chu

### Message Bridging

BMC Message Bridging provides a mechanism for routing IPMI Messages between different media.

Please refer to [IPMI Website](https://www.intel.com/content/www/us/en/servers/ipmi/ipmi-home.html) for details about Message Bridging.

  * KCS to IPMB

The command "Send Message" is used to routing IPMI messages from KCS to IPMB via System Interface.

Later, the response to the bridged request is received by the BMC and routed into the Receive Message Queue and it is retrieved using a Get Message command.

The patch integrates the [kcsbridge](https://github.com/openbmc/kcsbridge), [ipmid](https://github.com/openbmc/phosphor-host-ipmid) and [ipmbbridge](https://gerrit.openbmc-project.xyz/#/c/openbmc/ipmbbridge/+/11130/) projects.

It's verified with Nuvoton's NPCM750 solution (which is referred as Poleg here) and Supermicro MBD-X9SCL-F-0.

**Source URL**

* [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-phosphor/image](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-phosphor/image)
* [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-phosphor/ipmi](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-phosphor/ipmi)
* [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-kernel/linux](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/tree/openbmc-master/recipes-kernel/linux)

**How to use**

1. The user is expected to know how to follow the instructions in the section **Setting up your OpenBMC project** in [Nuvoton-Israel/openbmc](https://github.com/Nuvoton-Israel/openbmc) to build and program an OpenBMC image into Poleg EVBs.  
    * Prepare a PC (which is referred as a build machine here) for building and programming the OpenBMC image.  
      > _The user is also expected to have general knowledge of ACPI/UEFI and know how to update the DSDT table in linux and build/update a linux kernel/driver._  

2. Prepare two Nuvoton Poleg EVBs. One is named Poleg EVB A and the other is Poleg EVB B.

    * Connect **pin 3-4** of J4 on Poleg EVB A with corresponding pins of J4 on Poleg EVB B, **one on one**.  
    * Connect **pin 12** of J3 on Poleg EVB A with corresponding pin of J3 on Poleg EVB B, **one on one**.  
    * The connection needs a **1k** resistor and a **3.3v** supply from Poleg EVB A.  
      > _The component name of 3.3v supply is P4._

3. Follow instructions from step-1, step-2, step-3 and step-5 in [SOL](#sol) **How to use** section to set up your workstation, Poleg EVB A and Supermicro MBD-X9SCL-F-0.  
    > _Follow instructions from step-1 and step-5 in [SOL](#sol) **How to use** section to set up Poleg EVB B_.  

4. Install Ubuntu 14.04 64bit on Supermicro MBD-X9SCL-F-0 for the verification and login as a normal user.  
    > _The user is required to own root privileges on Ubuntu._

5. Poleg EVB A is configured to have its own slave address **0x10**. Poleg EVB B is configured to have its own slave address **0x58**.

    > _Poleg EVB A treats Poleg EVB B as its attached device on SMBUS/I2C bus and vice versa._

6. In the build machine, download [Nuvoton-Israel/openbmc](https://github.com/Nuvoton-Israel/openbmc) git repository.  

    * Download [Nuvoton-Israel/meta-openbmc-nuvoton-addon](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon) under the just-retrieved openbmc directory and follow the instructions in the section **[Usage](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon#usage)**.  
    * Follow the instructions in the section **Setting up your OpenBMC project** of of [Nuvoton-Israel/openbmc](https://github.com/Nuvoton-Israel/openbmc) to build and program an OpenBMC image for Poleg EVB A.  

7. Modify the following files corresponding to those in the downloaded openbmc directory of the build machine in step-6 for Poleg EVB B to meet the requirement of step-5.

    * [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/blob/openbmc-master/recipes-kernel/linux/files/0001-i2c-npcm750-enable-I2C-slave-support.patch](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/blob/openbmc-master/recipes-kernel/linux/files/0001-i2c-npcm750-enable-I2C-slave-support.patch)  

      | Line number  | Before modification  | After modification  |
      | :---         | :---:                | :---:               |
      | line 26      | ipmb\@40000010 \{ | ipmb\@40000058 \{  |

    * [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/blob/openbmc-master/recipes-kernel/linux/files/0001-i2c-npcm750-enable-I2C-slave-support.patch](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/blob/openbmc-master/recipes-kernel/linux/files/0001-i2c-npcm750-enable-I2C-slave-support.patch)  

      | Line number  | Before modification  | After modification  |
      | :--- | :---: | :---: |
      | line 28      | reg = <0x40000010>; | reg = <0x40000058>;  |

    * [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/blob/openbmc-master/recipes-phosphor/ipmi/phosphor-ipmi-ipmb/ipmbbridged.hpp](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/blob/openbmc-master/recipes-phosphor/ipmi/phosphor-ipmi-ipmb/ipmbbridged.hpp)  

      | Line number  | Before modification  | After modification  |
      | :--- | :--- | :--- |
      | line 30      | constexpr uint8_t ipmbRqSlaveAddress = 0x58; | constexpr uint8_t ipmbRqSlaveAddress = 0x10;  |

    * [https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/blob/openbmc-master/recipes-phosphor/ipmi/phosphor-ipmi-ipmb/ipmbbridged.cpp](https://github.com/Nuvoton-Israel/meta-openbmc-nuvoton-addon/blob/openbmc-master/recipes-phosphor/ipmi/phosphor-ipmi-ipmb/ipmbbridged.cpp)

      | Line number  | Before modification  | After modification  |
      | :--- | :---: | :---: |
      | line 619      |  const char *ipmbI2cSlave = "/sys/bus/i2c/devices/4-1010/slave-mqueue"; |  const char *ipmbI2cSlave = "/sys/bus/i2c/devices/4-1058/slave-mqueue";  |

    * In the build machine, rebuild the linux kernel for OpenBMC. As an example, enter the following command in a terminal window (build environment is configured in advance):  
      ```
      bitbake -C compile virtual/kernel
      ```

    * In the build machine, rebuild the OpenBmc image. As an example, enter the following two commands in a terminal window (build environment is configured in advance):  
      ```
      bitbake obmc-phosphor-image -c cleansstate  
      bitbake obmc-phosphor-image
      ```
    * Follow the section **Programming the images** of [Nuvoton-Israel/openbmc](https://github.com/Nuvoton-Israel/openbmc) to program the updated image into Poleg EVB B.

8. Modify the system interface driver in Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0 to communicate with Poleg EVB A.

    * Download the kernel source code of Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0 and locate the system interface driver source code.  
    * Locate the code in the function **init_ipmi_si** of ipmi_si_intf.c.
      ```
      enum ipmi_addr_src type = SI_INVALID;
      ```

    * Add the code next to the sentence "enum ipmi_addr_src type = SI_INVALID".
      ```
      return -1;
      ```

    * Rebuild the system interface driver and replace ipmi_si.ko of Ubuntu 14.04 with the one just rebuilt on Supermicro MBD-X9SCL-F-0.  
      > _The original ipmi_si.ko is located at /lib/modules/\`$(uname -r)\`/kernel/drivvers/char/ipmi_

    * Undo the "return -1" modification in the function **init_ipmi_si** of ipmi_si_intf.c.  
      + Rebuild the system interface driver again and leave the regenerated ipmi_si.ko in the kernel source code ipmi directory for system interface driver.

    * Reboot Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0.

9. Update the DSDT table in Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0.  

    * Study the section **How to build a custom DSDT into an initrd** of [overriding-dsdt](https://01.org/zh/linux-acpi/documentation/overriding-dsdt) and [initrd_table_override.txt](https://www.kernel.org/doc/Documentation/acpi/initrd_table_override.txt) to override DSDT in the initrd image of Ubuntu 14.04 and rebuild the Ubuntu kernel on Supermicro MBD-X9SCL-F-0.
    * In the DSDT table, update the OEMRevision field in DefinitionBlock.  
    * In the DSDT table, create two objects used for accessing Poleg EVB A KCS devices via 0x4E, 0x4F.  
      ```
      Name (IDTP, 0x0CA4)  
      Name (ICDP, 0x0CA5)  
      ```

    * Locate the code section like below.  
      ```
      Device (SPMI)
      {
          ...
          Name (_STR, Unicode ("IPMI_KCS"))  
          Name (_UID, Zero)
      ```
    * Add the codes below following the sentence "Name (_UID, Zero)".  
      ```
      OperationRegion (IPST, SystemIO, ICDP, One)
      Field (IPST, ByteAcc, NoLock, Preserve)
      {
          STAS,   8
      }
      ```

    * Locate the code section like below in the same SPMI code section just mentioned.  
      ```
      Method (_STA, 0, NotSerialized)
      ...
      If (LEqual (Local0, 0xFF))
      {
      ...
      ```
    * Add the codes below inside the "If" sentence scope.
      ```
      Store (0x11, LDN)
      Store (0x1,  ACTR)
      Store (0x0C, IOAH)
      Store (0xA4, IOAL)
      Store (0x0C, IOH2)
      Store (0xA5, IOL2)
      ```

    * Rebuild the modified DSDT table and regenerate the initrd image of Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0.  
    * Reboot Supermicro MBD-X9SCL-F-0 to load the overriden DSDT.

10. (Optional)Create shell scripts in Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0.

    * The scripts here are just for convenience and for reference.  
    * Download and build [ioport-1.2.tar.gz](https://people.redhat.com/rjones/ioport/files/ioport-1.2.tar.gz).  
      + Locate the generated **outb** executive.
    * Create a script named "kcs_switch.sh" for example to configure the access to the kcs device of Poleg EVB A from Supermicro MBD-X9SCL-F-0.  
    * The user needs to modify the path to the outb executive in the script (kcs_switch.sh) below.  
      ```
      #!/bin/sh
      outb 0x4e 0x07
      outb 0x4f 0x11

      outb 0x4e 0x30
      outb 0x4f 0x1

      outb 0x4e 0x60
      outb 0x4f 0x0C
      outb 0x4e 0x61
      outb 0x4f 0xA4
      outb 0x4e 0x62
      outb 0x4f 0x0C
      outb 0x4e 0x63
      outb 0x4f 0xA5
      ```

     * Create a script name "insert_ipmi_mod.sh" for example to use the regenerated KCS driver in the kernel source code ipmi directory metioned in step-8.  
     * The user needs to modify the path to the KCS driver in insert_ipmi_mod.sh below.  

       ```
       #!/bin/sh
       sudo insmod ./ipmi_devintf.ko
       sudo insmod ./ipmi_si.ko
       ```

    * Make sure that two scripts above are executable.

11. Install the ipmiutil in Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0.

    * Download, extract, build and install [ipmiutil-3.1.2.tar.gz](http://sourceforge.net/projects/ipmiutil/files/ipmiutil-3.1.2.tar.gz).  
    * Open a terminal window and navigate to the extracted folder of ipmiutil-3.1.2.tar.gz.  
    * Input the following command in the terminal window.
      ```
      sudo ./scripts/ipmi_if.sh
      ```
    * This generates /var/lib/ipmiutil/ipmi_if.txt.  
    * Edit /var/lib/ipmiutil/ipmi_if.txt with the root privilege. 
    * The value for "Base Address:" is **0x0000000000000CA2 (I/O)** and modify it to **0x0000000000000CA4 (I/O)**.

12. Test message bridging.

    * Power up or reboot Poleg EVB A and Poleg EVB B. Make sure that login screens of Poleg EVBs are displayed on the terminal window (e.g. Tera Term) on your workstation.
    * Power up or reboot Supermicro MBD-X9SCL-F-0 and login into Ubuntu 14.04 as a normal user.  
      + Open a terminal window and execute **kcs_switch.sh** and **insert_ipmi_mod.sh** created in step-10 with the root privilege.
      + If the scripts are not created, input the contents of **kcs_switch.sh** and **insert_ipmi_mod.sh** except the #!/bin/sh line manually.
    * Enter the following command in a terminal window as a normal user of Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0.  
      ```
      sudo ipmiutil cmd 18 34 02 10 18 d8 20 0e 01 d1 -x -s -j -F kcs
      ```
      > _The example command in the data field of "Send Message" command is "Get Device ID"._

    * Enter the following command in a terminal window as a normal user of Ubuntu 14.04 on Supermicro MBD-X9SCL-F-0.  
      ```
      sudo ipmiutil cmd 18 33 -x -s -j -F kcs
      ```
      > _The response to "Get Device ID" command might be "respData[len=26]: 1c 33 00 02 1e c2 58 00 01 00 00 00 02 03 02 00 00 00 00 00 00 00 00 00 00 a0"._


  * Net to IPMB

**Maintainer**

* Stanley Chu
* Tyrone Ting

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
* 2018.09.07 Update SOL for WebUI and IPMI
* 2018.09.10 Update System/Time/SNTP
* 2018.09.11 Update KCS to IPMB part of Message Bridging
