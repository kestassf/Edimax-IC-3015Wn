# Edimax-IC-3015Wn
Information collected about Edimax IC-3015Wn wifi camera. 

# Serial interface
TTL (3.3V) serial interface (38400 8N1) is present on **TP6** (RX) ant **TP8** (TX)
testpoints (CPU 2 and 3 pins routed to the PCB opposite side). GND can be
attached to any ground pin (eg. grounded pins of LAN connector).

# Debricking
Hold reset button while attaching power. Camera enters RealTek bootloader and
sets LAN interface IP to **192.168.1.6**. Set Your PC IP to 192.168.1.2. Bootloader does not respond to ICMP (ping), You can check connection using ARP packets (replace ethX with Your LAN interface name):

    arping -I ethX 192.168.1.6

Firmware (kernel) is uploaded via TFTP:

    tftp -m binary -v 192.168.1.6 -c put kernel_v1.11

You can see something like this on serial:

    <RealTek>
    **TFTP Client Upload, File Name: kernel_v1.11
    -
    **TFTP Client Upload File Size = F8012 Bytes at 80500000
    
    Success!
    <RealTek>
    Linux kernel (root-fs) upgrade.
    checksum Ok !
    burn Addr =0x10000! srcAddr=0x80500000 len =0xf8012 
    ................................................................................................................
    Flash Write Successed!

Bootloader receives file to RAM at 0x80500000, then flashes it to 0x10000 and reboots.

It seams kernel and rootfs need to be uploaded separately.

I've only tried flashing kernel this way, and rootfs I've wrote manually to flash once received via TFTP using serial:

    tftp -m binary -v 192.168.1.6 -c put rootfs_v1.11

Serial:

    <RealTek>
    **TFTP Client Upload, File Name: rootfs_v1.11
    -
    **TFTP Client Upload File Size = 213003 Bytes at 80500000
    
    Success!
    
    <RealTek>flw 00110000 80500000 213003
    Write 0x213003 Bytes to SPI flash#1, offset 0x110000<0xbd110000>, from RAM 0x80500000 to 0x80713003
    (Y)es, (N)o->y

# Links
* [Oficial site, v1.11 firmware, manuals, software, etc](http://www.edimax.com/edimax/download/download/data/edimax/global/download/for_home/home_legacy_products/home_legacy_ip_cameras_network_cameras/ic-3015wn)
* [v1.9 firmware](http://www.edimax.us/html/english/frames/b-download.htm)
* [RealTek bootloder](https://wiki.openwrt.org/doc/techref/bootloader/realtek)
* [Similar firmware principles](https://wiki.openwrt.org/toh/upvel/ur825ac)
* [OpenWrt for Edimax / untested](https://github.com/utessel/edimax)
  * [Hotplug2 dependency](https://github.com/rdebath/hotplug2)
