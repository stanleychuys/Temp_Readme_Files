# NPCM750 RunBMC Olympus
This is the Nuvoton RunBMC Olympus System layer.
The NPCM750 is an ARM based SoC with external DDR RAM and
supports a large set of peripherals made by Nuvoton.
More information about the NPCM7XX can be found
[here](http://www.nuvoton.com/hq/products/cloud-computing/ibmc/?__locale=en).

- Working with [openbmc master branch](https://github.com/openbmc/openbmc/tree/master "openbmc master branch")

# Dependencies
![](https://cdn.rawgit.com/maxdog988/icons/61485d57/label_openbmc_ver_master.svg)

This layer depends on:

```
  URI: git@github.com:Nuvoton-Israel/openbmc
  branch: master
```

# Contacts for Patches

Please submit any patches against the meta-runbmc-nuvoton layer to the maintainer of nuvoton:

* Joseph Liu, <KWLIU@nuvoton.com>
* Medad CChien, <CTCCHIEN@nuvoton.com>
* Tyrone Ting, <KFTING@nuvoton.com>
* Stanley Chu, <YSCHU@nuvoton.com>
* Tim Lee, <CHLI30@nuvoton.com>
* Brian Ma, <CHMA0@nuvoton.com>
* Jim Liu, <JJLIU0@nuvoton.com>

# Table of Contents

- [Dependencies](#dependencies)
- [Contacts for Patches](#contacts-for-patches)
- [Features of NPCM750 RunBMC Olympus](#features-of-npcm750-runbmc-olympus)
  * [WebUI](#webui)
    + [Remote KVM](#remote-kvm)
    + [Virtual Media](#virtual-media)
  * [LDAP for User Management](#ldap-for-user-management)
    + [LDAP Server Setup](#ldap-server-setup)
  * [JTAG Master](#jtag-master)
    + [ASD](#asd)
    + [CPLD Programming](#cpld-programming)
  * [System Event Policy](#system-event-policy)
  * [In-Band Firmware Update](#in-band-firmware-update)
    + [HOST Tool](#host-tool)
    + [IPMI Library](#ipmi-library)
- [Features In Progressing](#features-in-progressing)
- [Features Planned](#features-planned)
- [IPMI Commands Verified](#ipmi-commands-verified)
- [DCMI Commands Verified](#dcmi-commands-verified)
- [Image Size](#image-size)
- [Modifications](#modifications)

# Features of NPCM750 RunBMC Olympus

## WebUI

### Remote KVM
<img align="right" width="30%" src="https://cdn.rawgit.com/NTC-CCBG/snapshots/e8178eef/openbmc/kvm.png">

This is a Virtual Network Computing (VNC) server programm using [LibVNCServer](https://github.com/LibVNC/libvncserver).
1. Support Video Capture and Differentiation(VCD), compares frame by hardware.
2. Support Encoding Compression Engine(ECE), 16-bit hextile compression hardware encoding.
3. Support USB HID, support Keyboard and Mouse.

**Source URL**

* [https://github.com/Nuvoton-Israel/obmc-ikvm](https://github.com/Nuvoton-Israel/obmc-ikvm)

**How to use**

1. Power on the Nuvoton RunBMC Olympus
2. Make sure the network is connected with your workstation.
3. Launch a browser in your workstation and you will see the entry page.
    ```
    /* BMCWeb Server */
    https://<poelg ip>
    ```
4. Login to OpenBMC home page
    ```
    Username: root
    Password: 0penBmc
    ```
5. Navigate to OpenBMC WebUI viewer
    ```
    Server control -> KVM
    ```
**Performance**

* Host OS: Windows Server 2012 R2

|Playing video: [AQUAMAN](https://www.youtube.com/watch?v=2wcj6SrX4zw)|[Real VNC viewer](https://www.realvnc.com/en/connect/download/viewer/) | noVNC viewer
:-------------|:--------|:-----------|
Host Resolution    | FPS    | FPS |
1024x768  |  25    | 8 |
1280x1024   |  20  | 4 |
1600x1200   |  14   | 3 |

|Scrolling bar: [Demo video](https://drive.google.com/file/d/1H71_H6yjO8NU4Qu_ZL4F59FQ0PQmEo2n/view)|[Real VNC viewer](https://www.realvnc.com/en/connect/download/viewer/) | noVNC viewer
:-------------|:--------|:-----------|
Host Resolution    | FPS    | FPS |
1024x768  |  31    | 15 |
1280x1024   |  24  | 12 |
1600x1200   |  20   | 7 |

**The preferred settings of RealVNC Viewer**
```
Picture quality: Custom
ColorLevel: rgb565
PreferredEncoding: Hextile
```

**Maintainer**

* Joseph Liu

# LDAP for User Management
<img align="right" width="30%" src="https://cdn.rawgit.com/NTC-CCBG/snapshots/b6fdec0d/openbmc/ldap-login-via-ssh.png">
<img align="right" width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/master/openbmc/access_ldap_via_poleg.PNG">

The Lightweight Directory Access Protocol (LDAP) is an open, vendor-neutral, industry standard application protocol for accessing and maintaining distributed directory information services over an Internet Protocol (IP) network.

LDAP is specified in a series of Internet Engineering Task Force (IETF) Standard Track publications called Request for Comments (RFCs), using the description language ASN.1.

A common use of LDAP is to provide a central place to store usernames and passwords. This allows many different applications and services to connect to the LDAP server to validate users.

**Source URL**

* [https://github.com/Nuvoton-Israel/openbmc/tree/runbmc/meta-phosphor/nuvoton-layer/dlc/ldap-support-user-management](https://github.com/Nuvoton-Israel/openbmc/tree/runbmc/meta-phosphor/nuvoton-layer/dlc/ldap-support-user-management)
* [https://github.com/Nuvoton-Israel/openbmc-util/tree/master/ldap_server](https://github.com/Nuvoton-Israel/openbmc-util/tree/master/ldap_server)

### LDAP Server Setup

**How to use**

1. The user is expected to know how to follow the instructions in the section **Setting up your OpenBMC project** in [Nuvoton-Israel/openbmc](https://github.com/Nuvoton-Israel/openbmc) to build and program an OpenBMC image into Poleg platforms (The term **Poleg** is used hereafter). 
    > _Prepare a PC which builds OpenBMC. (called the build machine hereafter)_  
    > _The user is also expected to have knowledge of LDAP and its operations._

2. Install Ubuntu 16.04 64 bit (called Ubuntu hereafter) on a PC which is used as a LDAP server and log in it with an account with root privilege.

3. Set up the LDAP server and its configurations in Ubuntu. 

    * Open a terminal and input the following commands to install required software packages in advance.

      ```
      sudo apt-get install git
      sudo apt-get install libsasl2-dev
      sudo apt-get install g++
      wget http://download.oracle.com/berkeley-db/db-4.8.30.zip
      unzip db-4.8.30.zip
      cd db-4.8.30
      cd build_unix/
      ../dist/configure --prefix=/usr/local --enable-cxxmake
      sudo make install
      ```
    * Install OpenSSL
      + Download [openssl-1.0.2j.tar.gz](https://ftp.openssl.org/source/old/1.0.2/openssl-1.0.2j.tar.gz).
      + Extract openssl-1.0.2j.tar.gz.
      + Open a terminal, navigate to the extracted folder and input the following commands to install OpenSSL.
        ```
        ./config shared --prefix=/usr/local
        make
        make test
        sudo make install
        ```
    * Install OpenLDAP
      + Download OpenLDAP from [https://github.com/openldap/openldap](https://github.com/openldap/openldap)

        > _git clone https://github.com/openldap/openldap_

      + Open a terminal and input the following command to build and install OpenLDAP.
        ```
        ./configure CPPFLAGS="-I/usr/local/include -I/usr/local/include/openssl" LDFLAGS="-L/usr/local/lib -Wl,-rpath,/usr/local/lib" --prefix=/usr/local  --enable-syncprov=yes --enable-crypt=yes --enable-accesslog=yes --enable-auditlog=yes --enable-constraint=yes --enable-ppolicy=yes --enable-modules --enable-mdb --enable-spasswd --enable-debug=yes --enable-syslog --enable-slapd --enable-cleartext --enable-monitor --enable-overlays -with-threads --enable-rewrite --enable-syncprov=yes --with-tls=openssl 
        ```
          > _The description above is one line only._

        ```
        make depend 
        make
        sudo make install
        ```
    * Execute LDAP server
      + Open a terminal and input the following command.

        ```
        sudo /usr/local/libexec/slapd -d 1 -h 'ldaps:/// ldap:/// ldapi:///'
        ```
          > _To stop LDAP server execution, press Ctrl key and C key at the same time in the terminal._  
          > _Now please stop the LDAP server execution._

    * Generate security configurations for the LDAP server running in Ubuntu.
      > _Here a two-stage signing process is applied._  
      > _You could also use the self-signed CA and cert for the configuration if your company uses them._

      + Generate the CA key and cert. Open a terminal and input the following commands.
        ```
        openssl genrsa -out ca_server.key 2048  
        openssl req -x509 -new -key ca_server.key -days 3650 -out ca_server.pem -subj '/C=OO/ST=OO/L=OO/O= OO/OU= OO /CN= OO'
        ```

        > _Define these **OO** for the arguments **C**, **ST**, etc. according to your configurations._  
        > _Please refer to the following link for explanations of the arguments **C**, **ST**, etc._  
        > _[https://www.shellhacks.com/create-csr-openssl-without-prompt-non-interactive/](https://www.shellhacks.com/create-csr-openssl-without-prompt-non-interactive/)._

      + Generate the LDAP key and CSR. In the same terminal, input the following commands.
        ```
        openssl genrsa -out ldap_server.key 2048  
        openssl req -new -key ldap_server.key -out ldap_server.csr -subj '/C=OO /ST=OO /L=OO/O=OO/OU=OO/CN=ldap.example.com'
        ```

        > _Define these **OO** for the arguments **C**, **ST**, etc. according to your configurations._  
        > _Note that the field **CN** in ldap_server.csr must be set to the fully qualified domain name of the LDAP server._

      + Generate ldap cert signed with CA cert. In the same terminal, input the following command.
        ```
        openssl x509 -req -days 365 -CA ca_server.pem -CAkey ca_server.key -CAcreateserial -CAserial serial -in ldap_server.csr -out ldap_server.pem
        ```

    * Store and specify locations of keys and certs.
      + Edit /usr/local/etc/openldap/slapd.conf in Ubuntu with root privilege to update fields as examples shown below.
        > _TLSCACertificateFile /etc/ldap/ca_certs.pem_  
        > _TLSCertificateFile /etc/ssl/certs/ldap_server.pem_  
        > _TLSCertificateKeyFile /etc/ssl/private/ldap_server.key_  
        > _TLSCACertificatePath /etc/ldap_
      
      + Copy ca_certs.pem, ldap_server.pem and ldap_server.key into locations specified above with root privilege.

    * Add LDAP schema and LDIF.
      + Download [user_exp.schema](https://github.com/Nuvoton-Israel/openbmc-util/blob/master/ldap_server/schema/user_exp.schema) and save it at /usr/local/etc/openldap/schema with root privilege in Ubuntu.
      + Modify /usr/local/etc/openldap/slapd.conf in Ubuntu with root privilege to specify the schema just saved.
        > _include /usr/local/etc/openldap/schema/user_exp.schema_

      + Download [bdn.ldif](https://github.com/Nuvoton-Israel/openbmc-util/blob/master/ldap_server/ldif/bdn.ldif), [ap_group.ldif](https://github.com/Nuvoton-Israel/openbmc-util/blob/master/ldap_server/ldif/ap_group.ldif), [bmc.ldif](https://github.com/Nuvoton-Israel/openbmc-util/blob/master/ldap_server/ldif/bmc.ldif), [group.ldif](https://github.com/Nuvoton-Israel/openbmc-util/blob/master/ldap_server/ldif/group.ldif), [people.ldif](https://github.com/Nuvoton-Israel/openbmc-util/blob/master/ldap_server/ldif/people.ldif) and [privRole.ldif](https://github.com/Nuvoton-Israel/openbmc-util/blob/master/ldap_server/ldif/privRole.ldif) to a temporary folder in Ubuntu.
      + Open a terminal, navigate to the temporary folder for storing LDIF and input the following commands to add these LDIF into the LDAD server in Ubuntu.
        ```
        sudo slapadd -l ./bdn.ldif
        sudo slapadd -l ./ap_group.ldif
        sudo slapadd -l ./bmc.ldif
        sudo slapadd -l ./group.ldif
        sudo slapadd -l ./people.ldif
        sudo slapadd -l ./privRole.ldif
        ```

    * Execute LDAP server.
      + Open a terminal and input the following command in the terminal.
      ```
      sudo /usr/local/libexec/slapd -d 1 -h 'ldaps:/// ldap:/// ldapi:///'
      ```

4. Setup LDAP client configuration on Poleg.

    * Open a terminal in the build machine and navigate to the directory which contains OpenBMC source codes. The directory is called **OPENBMCDIR** hereafter.
      + Copy all directories and their containing files from [https://github.com/Nuvoton-Israel/openbmc/tree/runbmc/meta-phosphor/nuvoton-layer/dlc/ldap-support-user-management](https://github.com/Nuvoton-Israel/openbmc/tree/runbmc/meta-phosphor/nuvoton-layer/dlc/ldap-support-user-management) under OPENBMCDIR/meta-quanta/meta-runbmc-nuvoton directory according to their default hierarchy.
        > _Nuvoton RunBMC Olympus is used for the LDAP demonstration so the corresponding runbmc software layer is applied here._

    * Update OPENBMCDIR/meta-quanta/meta-runbmc-nuvoton/recipes-support/nss-pam-ldapd/files/nslcd.conf. (optional)
      + The IP address for the LDAP server in Ubuntu is configured as **10.103.152.11**. Modify the field **uri ldap** in nslcd.conf according to your network configuration.
        > _uri ldap://10.103.152.11/_

      + The modification above is done in OpenBmc build time. If you would like to modify **uri** in OpenBmc run time, follow the instructions below after logging into Poleg in the console program (like Tera Term) with the root account (root/0penBmc).
        > _The console program is used to display a debug console provided by Poleg._

        ```
        vi /etc/nslcd.conf
        ```

        > _Locate the line **uri ldap://10.103.152.11/**. Modify the field **uri ldap** according to your network configuration._

        ```
        systemctl stop nslcd
        systemctl start nslcd
        ```

    * In the build machine, open a terminal window (build environment is configured in advance and the working directory is at OPENBMCDIR/build) to input the following commands to build the OpenBMC image.
      ```
      bitbake -C fetch nss-pam-ldapd
      bitbake -C fetch dropbear
      bitbake -C fetch libpam
      bitbake -C fetch bmcweb
      bitbake -C fetch phosphor-ipmi-net
      bitbake -C fetch openldap
      bitbake obmc-phosphor-image
      ```
    * Program the updated image into Poleg.

5. Test LDAP server.

    * Connect Poleg to the PC running Ubuntu with an ethernet cable and power on it.
    * Log in Poleg from the console program (like Tera Term) with the root account (root/0penBmc).
      > _The console program is used to display a debug console provided by Poleg._

    * The IP address for the LDAP server is 192.168.0.101 for now.
    * Set up IP addresses for Poleg and Ubuntu so that they can ping each other.
      + For example, set Poleg's IP address to 192.168.0.2. Input the following command in the console program.
        ```
        ifconfig eth2 192.168.0.2
        ```

        > _Please replace **192.168.0.2** with your IP configuration for Poleg._

    * Execute the following command in the console program.
      ```
      ldapsearch -ZZ -h 192.168.0.101 -D "cn=admin,dc=ldap,dc=example,dc=com" -b "dc=ldap,dc=example,dc=com" -w secret
      ```  
      > _Please replace **192.168.0.101** with your IP configuration for Ubuntu._  
      > _The ldapsearch example is to display all the data stored in the LDAP server using a TLS connection._

    * You could use the account **user1** stored in the LDAP server to log in WebUI running on Poleg.

      + Some descriptions about the LDIF used by the LDAP server and authentication process are provided here. Please refer to the six snapshots in the following description.
        > _To login using an account, the authentication logic has to check the following criteria._  
        > _**bmc-uid**: It stands for the BMC machine that the account is used to login. The BMC machines are grouped by DN **ou=ap_group,dc=ldap,dc=example,dc=com**. One BMC machine can be in multiple groups at the same time. (see **ap_group** below)_  
        <img width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/c8c60b29/openbmc/bmc1_info.png">
        <img width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/c8c60b29/openbmc/bmc2_info.png">  
        
        > _**ap_group**: Applications like web server, email, ftp and so on are deployed on the servers attched by BMC machines. Therefore, grouping by applications is taken into the authentication process. The authentication refuses an account to log in some BMC machine if that machine is not deployed under the certain **ap_group** the account also joins._  
        <img width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/c8c60b29/openbmc/email_info.png">
        <img width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/c8c60b29/openbmc/webserver_info.png">  
        
        > _**people**: It contains the account information (login/privileges) stored in the LDAP server. An account can join multiple **ap_group** simutaneously._  
        <img width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/c8c60b29/openbmc/user1_logininfo.png">
        <img width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/c8c60b29/openbmc/user2_logininfo.png">  
        
        > _**user-login-disabled**: While this attribute's value is 1, it is not allowed to login with the account's membership of the specific **ap_group**_.  
        > _**user-login-interface**: It's used as a channel via that the account logins for an **ap_group**. For example, **web** stands for logging in a BMC machine via WebUI. If **web** does not exist in any **user-login-interface** attributes an account owns under a certain ap_group, it means that the user cannot use this account to login as a member of the preferred ap_group via WebUI._

      + Use an LDAP tool to modify the field **macAddress** of the DN **bmc-uid=bmc1,ou=bmc,dc=ldap,dc=example,dc=com** stored in the LDAP server.
        > _The modification is to use the mac address of the ethernet module on Poleg you currently test with._

      + To get the mac address desired, input the following command in the console program.
        ```
        ifconfig eth0
        ```
        > _Locate the keyword **HWaddr** displayed in the console program._  
        > _Copy the value next to HWaddr to override the value of the field **macAddress** of the DN **bmc-uid=bmc1,ou=bmc,dc=ldap,dc=example,dc=com**._

      + Launch a browser and navigate to the Poleg's IP address.
        > _Bypass the secure warning and continue to the website._

      + Use user1/123 to log in WebUI.
        > _user1 is the login ID._  
        > _123 is the login password._  
        > _The **bmc-uid** for the BMC machine used for this test is bmc1. According to the LDIF provided, the BMC machine bmc1 is deployed under the **ap_group** email and the the BMC machine bmc2 is deployed under **ap_group** webserver. Also one can tell from the snapshots, user1 and user2 have different **user-login-interface** settings for the **ap_group** email and **ap_group** webserver respectively._  
        > _User1 is able to log on bmc1 via WebUI since the following conditions are met: the BMC machine bmc1 is deployed under **ap_group** email.; user1 is a member of the **ap_group** email.; user1 has an **user-login-interface** setting as **web** for that group and value of user1's **user-login-disabled** attribute is not set._  
        > _Although user2 is also a member of the **ap_group** email, it does not have an **user-login-interface** setting as **web** for that group. Under such conditions, user2 is not allowed to log on bmc1. User2 does have an **user-login-interface** setting as **web** for the **ap_group** webserver but bmc1 is not deployed under the **ap_group** webserver._  
        > _The description above explains why user1 is used for this test._

    * Password modification is also available to LDAP accounts via WebUI.
      + Log in WebUI using user1/123 as mentioned in previous phrase.
      + Navigate to `Access control` menu item on the left panel and select it.
      + A sub menu item `Local users` pops up and select it.
      + Modify the password value for user1 by selecting the icon at just the right side of the text area "Account status".

      + Input the same new password twice.
        > _The input locations are right below **USER PASSWORD** and **CONFIRM USER PASSWORD** text area._

      + Press the `Save` button.
        > _A message **Success! User has been updated successfully.** is expected to show then._

      + Log out WebUI and login again with the new password for user1.

    * Log in Poleg via SSH using an LDAP account.
      + Make sure that configurations stated in Step 5 for Poleg and Ubuntu are set accordingly and ping between Ubuntu and Poleg is okay.
      + Install **ssh** in Ubuntu with root privilege if ssh client is not available. Open a terminal and input the following command.
        ```
        sudo apt-get install ssh
        ```

      + Open a terminal in Ubuntu to log in Poleg using the LDAP account **user1** and its password via SSH. Input the following command in the terminal.
        ```
        ssh user1@192.168.0.2
        ```
        > _Please replace **192.168.0.2** with your IP configuration for Poleg._  
        > _It requires the account to be in the priv-admin group in the LDAP group database for accessing SSH._  
        > _Please refer to [group.ldif](https://github.com/Nuvoton-Israel/openbmc-util/blob/master/ldap_server/ldif/group.ldif) for more details._

    * Execute ipmi commands using an LDAP account.
      + Make sure that configurations stated in Step 5 for Poleg and Ubuntu are set accordingly and ping between Ubuntu and Poleg is okay.
      + Install **ipmitool** in Ubuntu with root privilege for the demonstration purpose. Open a terminal and input the following command.
        ```
        sudo apt-get install ipmitool
        ```

      + Open a terminal in Ubuntu to execute ipmi commands to Poleg using the LDAP account **user2** and its password **123** via ipmitool. Input the following command in the terminal.
        ```
        sudo ipmitool -H 192.168.0.2 -U user2 -P 123 -I lanplus user list
        ```
        > _Please replace **192.168.0.2** with your IP configuration for Poleg._
        > _Only limited ipmi commands are supported._

**Maintainer**

* Tyrone Ting

## JTAG Master
JTAG master is implemented on BMC to facilitate debugging host CPU or programming CPLD device.  

**Source URL**

* [https://github.com/Intel-BMC/asd](https://github.com/Intel-BMC/asd)
* [https://github.com/Nuvoton-Israel/openbmc-util/tree/master/loadsvf](https://github.com/Nuvoton-Israel/openbmc-util/tree/master/loadsvf)

### ASD
<img align="right" width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/870d09e/openbmc/asd.png">
The Intel® At-Scale Debug feature allows for using any host system to run the Debug tool stack while connecting to the target system across the network. The target system must have a BMC which has physical connectivity to the JTAG pins as a minimum requirement of functionality.    

**How to use**
1. switch JPC1 jumper to 2-3
2. configure GPIO40(BMC_XDP_JTAG_SEL_N) to connect BMC JTAG to Host CPU
```
echo 40 > /sys/class/gpio/export
echo 0 > /sys/class/gpio/gpio40/value 
```
3. configure BMC_TCK_MUX_SEL pin to CPU TCK
```
echo 22 > /sys/class/gpio/export
echo 1 > /sys/class/gpio/gpio22/value
```
3. Run ASD daemon on BMC
```
asd -u -n eth1 --log-level=warning -p 5123
```
4. Launch CScripts on debug host
```
Assume CScripts source folder = $CS, OpenIPC source folder = $OIPC
Edit $OIPC/openipc/Config/SKX/SKX_ASD_RC-Pins.xml
- Change ip address to BMC ip address
Edit $OIPC/openipc/Config/OpenIpcConfig.xml
- Change DefaultIpcConfig tag as <DefaultIpcConfig Name="SKX_ASD_RC-Pins"/>
export IPC_PATH=$OIPC/openipc/Bin
export LD_LIBRARY_PATH=$IPC_PATH
Go to $CS/cscripts, execute "python startCscripts.py -a ipc"
```
5. Execute OpenIPC idcode operation in CScripts command prompt. It will show the TAP device's idcode.
```
>>> import ipccli
>>> ipc = ipccli.baseaccess()
>>> ipc.idcode(0)
```


### CPLD Programming
The motherboard on server have a CPLD device that can be upgraded firmware on it. BMC can load svf file to program CPLD via JTAG.  

**How to use**  
run loadsvf on Runbmc to program CPLD. Specify the svf file name with -s.
```
loadsvf -d /dev/jtag0 -s firmware.svf
```

**Maintainer**
* Stanley Chu

## System Event Policy

phosphor-dbus-monitor service can watch on specific dbus objects/properties and perform predefined event callbacks when the property state has changed or meets the condition defined in config file.  

**Source URL**
* [phosphor-dbus-monitor](https://github.com/openbmc/phosphor-dbus-monitor)
* [phosphor-snmp](https://github.com/openbmc/phosphor-snmp)


### Event Callbacks
The event callbacks can be the following actions. Logging to journal or elog, calling d-bus method, or sending snmp trap. Here is an example that log to journal and send d-bus message to shutdown host when the specific temperature sensor reaches the critical high threshold.  

* [Example config](https://github.com/Nuvoton-Israel/openbmc/blob/runbmc/meta-quanta/meta-runbmc-nuvoton/recipes-phosphor/configuration/runbmc-nuvoton-yaml-config/runbmc-nuvoton-dbus-monitor-config.yaml)

### SNMP trap
**How to use** 
1. Install snmp trap receiver on the management server. Refer to the [link](https://blog.cadena-it.com/linux-tips-how-to/snmp-trap-receiver-with-ubuntu/) for the installation.
    * Copy [BMC notification MIB](https://github.com/openbmc/phosphor-snmp/blob/master/mibs/NotificationMIB.txt) to /usr/share/snmp/mibs.
    * Run snmptrapd with -m option to load custom MIB
       * snmptrapd -m OPENBMC-NOTIFICATION-MIB -Lf /var/log/snmptrap.log -f
2. Specify snmp manager ip/port in openbmc WebUI
   ```
    Server configuration
     -> SNMP settings
       -> Add Managers
          -> enter the snmp manger ip and port(default is 162)
    ```
3. Check snmp trap log in snmp manager.
   * /var/log/snmptrap.log
   * the OIDs in log file will be translated to human readable string defined in MIB
   * Example:
   ```
   TRAP2, SNMP v2c, community publicDU Attribute/Value Pair Array:
   SNMPv2-SMI::snmpModules.1.1.4.1.0 = OID: OPENBMC-NOTIFICATION-MIB::obmcErrorNotification
   OPENBMC-NOTIFICATION-MIB::obmcErrorID = Gauge32: 135
   OPENBMC-NOTIFICATION-MIB::obmcErrorTimestamp = Opaque: UInt64: 780329535445925888
   OPENBMC-NOTIFICATION-MIB::obmcErrorSeverity = INTEGER: 3
   OPENBMC-NOTIFICATION-MIB::obmcErrorMessage = STRING:   "xyz.openbmc_project.Sensor.Threshold.Error.WarningLow"
   ```

**Maintainer**
* Stanley Chu

## In-Band Firmware Update
This is a secure flash update mechanism to update HOST/BMC firmware via LPC/PCI.

**Source URL**

* [https://github.com/Nuvoton-Israel/phosphor-ipmi-flash](https://github.com/Nuvoton-Israel/phosphor-ipmi-flash)
* [https://github.com/Nuvoton-Israel/openbmc/blob/runbmc/meta-phosphor/nuvoton-layer/recipes-phosphor/ipmi/phosphor-ipmi-flash_%25.bbappend](https://github.com/Nuvoton-Israel/openbmc/blob/runbmc/meta-phosphor/nuvoton-layer/recipes-phosphor/ipmi/phosphor-ipmi-flash_%25.bbappend)

### HOST Tool
<img align="right" width="30%" src="https://raw.githubusercontent.com/NTC-CCBG/snapshots/322d0d3/openbmc/in-band-fu.png">The host-tool depends on ipmi-blob-tool and pciutils.

#### Building pciutils
Check out the [pciutils source](https://github.com/pciutils/pciutils).

Then run these commands in the source directory.
```
make SHARED=yes
make SHARED=yes install
make install-lib
```

#### Building ipmi-blob-tool
Check out the [ipmi-blob-tool source](https://github.com/openbmc/ipmi-blob-tool).
Then run these commands in the source directory.

```
./bootstrap.sh
./configure
make
make install
```

#### Building burn_my_bmc (the host-tool)
Check out the [phosphor-ipmi-flash source](https://github.com/Nuvoton-Israel/phosphor-ipmi-flash).
Then run these commands in the source directory.
If you choose "enable-nuvoton-p2a-vga", then the tool will support LPC and PCI-VGA.
If you choose "enable-nuvoton-p2a-mbox", then the tool will support LPC and PCI-MailBox

```
./bootstrap.sh
./configure --disable-build-bmc-blob-handler --enable-nuvoton-p2a-vga
make
make install
```

**How to use**
1. If you want to do firmware update over LPC, then you need to check the memory address which BIOS allocates for LPC.
You could use "ioport" tool and following commands to get the address.

```
sudo outb 0x4e 0x07
sudo outb 0x4f 0x0f

# Host address a7-a0
sudo outb 0x4e 0xF4
sudo inb 0x4f

# Host address a15-a8
sudo outb 0x4e 0xF5
sudo inb 0x4f

# Host address a23-a16
sudo outb 0x4e 0xF6
sudo inb 0x4f

# Host address a32-a24
sudo outb 0x4e 0xF7
sudo inb 0x4f

# shm active?
sudo outb 0x4e 0x30
sudo inb 0x4f

```
2. Here is an example for upadting over LPC,and --length is fixed.

```
sudo ./burn_my_bmc --command update --interface ipmilpc --image image-bmc --sig image-bmc.sig --type image --address 0x817e0000 --length 0x1000
```
3. Here is an example for upadting over PCI(both VGA and MailBox)and --type is fixed

```
sudo ./burn_my_bmc --command update --interface ipmipci --image image-bmc --sig image-bmc.sig --type image
```

### IPMI Library
This is an OpenBMC IPMI Library (Handler) for In-Band Firmware Update.

**How to use**
1.You need to enable the way you want to transfer data from host to bmc

```
nuvoton-lpc
enable-nuvoton-p2a-mbox
enable-nuvoton-p2a-vga
```

2.select the corresponding address for IPMI_FLASH_BMC_ADDRESS_nuvoton

```
0x7F400000
0xF0848000
0xc0008000
```

**Maintainer**
* Medad CChien

### Virtual Media
<img align="right" width="30%" src="https://cdn.rawgit.com/NTC-CCBG/snapshots/3bf2693/openbmc/vm.png">

Virtual Media (VM) is to emulate an USB drive on remote host PC via Network Block Device(NBD) and Mass Storage(MSTG).

**Source URL**

* [https://github.com/Nuvoton-Israel/openbmc/tree/runbmc/meta-phosphor/nuvoton-layer/recipes-connectivity/jsnbd](https://github.com/Nuvoton-Israel/openbmc/tree/runbmc/meta-phosphor/nuvoton-layer/recipes-connectivity/jsnbd)

**How to use**

1. Clone a physical usb drive to an image file
    * For Linux - use tool like **dd**
      ```
      dd if=/dev/sda of=usb.img bs=1M count=100
      ```
      > _**bs** here is block size and **count** is block count._
      >
      > _For example, if the size of your usb drive is 1GB, then you could set "bs=1M" and "count=1024"_

    * For Windows - use tool like **Win32DiskImager.exe**

    > _NOTICE : A simple *.iso file cannot work for this._

2. Login and switch to webpage of VM on your browser
    ```
    https://XXX.XXX.XXX.XXX/#/server-control/virtual-media
    ```

3. Operations of Virtual Media
    * After `Choose File`, click `Start` to start VM network service
    * After clicking `Start`, you will see a new usb device on HOST OS
    * If you want to stop this service, just click `Stop` to stop VM network service.

**Maintainer**
* Medad CChien

## Features In Progressing
* Improve IPMI
* Improve Redfish
* Sytem logs

## Features Planned
* PLDM
* NCSI
* Improve Power contorl
* Intel Platform related features

# IPMI Commands Verified

| Command | KCS | RMCP+ | IPMB |
| :--- | :---: | :---: | :---: |
| **IPM Device Global Commands** |  |  |  |
| Device ID | V | V | V |
| Cold Reset | V | V | V |
| Warm Reset | - | - | - |
| Get Self Test Results | V | V | V |
| Manufacturing Test On | - | - | - |
| Set ACPI Power State | V | V | V |
| Get ACPI Power State | V | V | V |
| Get Device GUID | V | V | V |
| Get NetFn Support | - | - | - |
| Get Command Support | - | - | - |
| Get Command Sub-function Support | - | - | - |
| Get Configurable Commands | - | - | - |
| Get Configurable Command Sub-functions | - | - | - |
| Set Command Enables | - | - | - |
| Get Command Enables | - | - | - |
| Set Command Sub-function Enables | - | - | - |
| Get Command Sub-function Enables | - | - | - |
| Get OEM NetFn IANA Support | - | - | - |
| **BMC Watchdog Timer Commands** |  |  |  |
| Reset Watchdog Timer | V | V | V |
| Set Watchdog Timer | V | V | V |
| Get Watchdog Timer | V | V | V |
| **BMC Device and Messaging Commands** |  |  |  |
| Set BMC Global Enables | V | V | V |
| Get BMC Global Enables | V | V | V |
| Clear Message Flags | - | - | - |
| Get Message Flags | V | V | V |
| Enable Message Channel Receive | - | - | - |
| Get Message | - | - | - |
| Send Message | - | - | - |
| Read Event Message Buffer | V | V | V |
| Get BT Interface Capabilities | V | V | V |
| Get System GUID | V | V | V |
| Set System Info Parameters | V | V | V |
| Get System Info Parameters | V | V | V |
| Get Channel Authentication Capabilities | V | V | V |
| Get Session Challenge | - | - | - |
| Activate Session | - | - | - |
| Set Session Privilege Level | - | - | - |
| Close Session | - | - | - |
| Get Session Info | - | - | - |
| Get AuthCode | - | - | - |
| Set Channel Access | V | V | V |
| Get Channel Access | V | V | V |
| Get Channel Info Command | V | V | V |
| User Access Command | V | V | V |
| Get User Access Command | V | V | V |
| Set User Name | V | V | V |
| Get User Name Command | V | V | V |
| Set User Password Command | V | V | V |
| Activate Payload | - | V | - |
| Deactivate Payload | - | V | - |
| Get Payload Activation Status | - | V | - |
| Get Payload Instance Info | - | V | - |
| Set User Payload Access | V | V | V |
| Get User Payload Access | V | V | V |
| Get Channel Payload Support | V | V | V |
| Get Channel Payload Version | V | V | V |
| Get Channel OEM Payload Info | - | - | - |
| Master Write-Read | V | V | V |
| Get Channel Cipher Suites | V | V| V |
| Suspend/Resume Payload Encryption | - | - | - |
| Set Channel Security Keys | - | - | - |
| Get System Interface Capabilities | - | - | - |
| Firmware Firewall Configuration | - | - | - |
| **Chassis Device Commands** |  |  |  |
| Get Chassis Capabilities | V | V | V |
| Get Chassis Status | V | V | V |
| Chassis Control | V | V | V |
| Chassis Reset | - | - | - |
| Chassis Identify | V | V | V |
| Set Front Panel Button Enables | - | - | - |
| Set Chassis Capabilities | V | V | V |
| Set Power Restore Policy | V | V | V |
| Set Power Cycle Interval | - | - | - |
| Get System Restart Cause | - | - | - |
| Set System Boot Options | V | V | V |
| Get System Boot Options | V | V | V |
| Get POH Counter | V | V | V |
| **Event Commands** |  |  |  |
| Set Event Receiver | - | - | - |
| Get Event Receiver | - | - | - |
| Platform Event | V | V | V |
| **PEF and Alerting Commands** |  |  |  |
| Get PEF Capabilities | - | - | - |
| Arm PEF Postpone Timer | - | - | - |
| Set PEF Configuration Parameters | - | - | - |
| Get PEF Configuration Parameters | - | - | - |
| Set Last Processed Event ID | - | - | - |
| Get Last Processed Event ID | - | - | - |
| Alert Immediate | - | - | - |
| PET Acknowledge | - | - | - |
| **Sensor Device Commands** |  |  |  |
| Get Device SDR Info | V | V | V |
| Get Device SDR | V | V | V |
| Reserve Device SDR Repository | V | V | V |
| Get Sensor Reading Factors | - | - | - |
| Set Sensor Hysteresis | - | - | - |
| Get Sensor Hysteresis | - | - | - |
| Set Sensor Threshold | - | - | - |
| Get Sensor Threshold | V | V | V |
| Set Sensor Event Enable | - | - | - |
| Get Sensor Event Enable | - | - | - |
| Re-arm Sensor Events | - | - | - |
| Get Sensor Event Status | - | - | - |
| Get Sensor Reading | V | V | V |
| Set Sensor Type | - | - | - |
| Get Sensor Type | V | V | V |
| Set Sensor Reading And Event Status | V | V | V |
| **FRU Device Commands** |  |  |  |
| Get FRU Inventory Area Info | V | V | V |
| Read FRU Data | V | V | V |
| Write FRU Data | V | V | V |
| **SDR Device Commands** |  |  |  |
| Get SDR Repository Info | V | V | V |
| Get SDR Repository Allocation Info | - | - | - |
| Reserve SDR Repository | V | V | V |
| Get SDR | V | V | V |
| Add SDR | - | - | - |
| Partial Add SDR | - | - | - |
| Delete SDR | - | - | - |
| Clear SDR Repository | - | - | - |
| Get SDR Repository Time | - | - | - |
| Set SDR Repository Time | - | - | - |
| Enter SDR Repository Update Mode | - | - | - |
| Exit SDR Repository Update Mode | - | - | - |
| Run Initialization Agent | - | - | - |
| **SEL Device Commands** |  |  |  |
| Get SEL Info | V | V | V |
| Get SEL Allocation Info | - | - | - |
| Reserve SEL | V | V | V |
| Get SEL Entry | V | V | V |
| Add SEL Entry | V | V | V |
| Partial Add SEL Entry | - | - | - |
| Delete SEL Entry | V | V | V |
| Clear SEL | V | V | V |
| Get SEL Time | V | V | V |
| Set SEL Time | V | V | V |
| Get Auxiliary Log Status | - | - | - |
| Set Auxiliary Log Status | - | - | - |
| Get SEL Time UTC Offset | - | - | - |
| Set SEL Time UTC Offset | - | - | - |
| **LAN Device Commands** |  |  |  |
| Set LAN Configuration Parameters | V | V | V |
| Get LAN Configuration Parameters | V | V | V |
| Suspend BMC ARPs | - | - | - |
| Get IP/UDP/RMCP Statistics | - | - | - |
| **Serial/Modem Device Commands** |  |  |  |
| Set Serial/Modem Mux | - | - | - |
| Set Serial Routing Mux | - | - | - |
| SOL Activating | - | V | - |
| Set SOL Configuration Parameters | - | V | - |
| Get SOL Configuration Parameters | - | V | - |
| **Command Forwarding Commands** |  |  |  |
| Forwarded Command | - | - | - |
| Set Forwarded Commands | - | - | - |
| Get Forwarded Commands | - | - | - |
| Enable Forwarded Commands | - | - | - |
> _V: Verified_  
> _-: Unsupported_

# DCMI Commands Verified
| Command | Verified |
| :--- | :---: | 
| **DCMI Capabilities & Discovery Configuration Commands** |  |
| Get DCMI Capabilities Info | V |
| Set DCMI Configuration Parameters | V |
| Get DCMI Configuration Parameters | V |
| Get Management Controller Identifier String | V |
| Set Management Controller Identifier String | V |
| **Platform & Asset Identification Commands** |  |
| Get Asset Tag | V |
| Set Asset Tag | V |
| **Sensor & SDR Commands** |  |
| Get DCMI Sensor Info| V |
| **Power Management** |  |
| Get Power Reading | V |
| Get Power Limit | V |
| Set Power Limit | V |
| Activate / Deactivate Power Limit | V |
| **Thermal Management** |  |
| Set Thermal Limit | - |
| Get Thermal List | - |
| Get Temperature Reading | V |

> _V: Verified_  
> _-: Unsupported_

# Image Size
Type          | Size    | Note                                                                                                     |
:-------------|:------- |:-------------------------------------------------------------------------------------------------------- |
image-uboot   |  450 KB | u-boot 2019.01 + bootblock for Poleg only                                                                       |
image-kernel  |  3.9 MB   | linux 5.2.11 version                                                                                       |
image-rofs    |  16.9 MB  | bottom layer of the overlayfs, read only                                                                 |
image-rwfs    |  0 MB  | middle layer of the overlayfs, rw files in this partition will be created at runtime,<br /> with a maximum capacity of 2MB|

# Modifications

* 2019.10.01 First release ReadME.md
