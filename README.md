ea9x00_openwrt
=====
OpenWrt support for the Linksys EA9500, EA9200

This repository aims to support the Linksys EA9500 `(linksys,panamera)` using the `swconfig` driver and configuration framework.
Support for EA9200 is added using the already existing brcmfmac drivers. 

# Build from scratch method I used

See INSTALL.md for more information.

## Components
*[ea_9x00 Support] : Adds LED, switch, and dual-partition support for the Linksys EA9500, LinkSys EA9200

## Building

### Step 1
To build OpenWrt for the EA9500, you must first add this feed to your `feeds.conf`

```
src-git ea9500_openwrt https://github.com/hurrian/ea9500_openwrt.git

$ ./scripts/feeds update -a
$ ./scripts/feeds install -a
```
### Step 2
The custom configuration overlay needs to be enabled by using the [custom files](https://openwrt.org/docs/guide-developer/build-system/use-buildsystem#custom_files) feature of the OpenWrt buildroot.
While in your buildroot directory (ex. ``~/openwrt``), run the following command:

```
~/openwrt $   ln -s feeds/ea9500_openwrt/files files
```

### Step 3
#### EA9500
You must then enable the Linksys EA9500 target in `target/linux/bcm53xx/image/Makefile` by uncommenting `TARGET_DEVICES += linksys-ea9500`.

```
define Device/linksys-ea9500
  DEVICE_VENDOR := Linksys
  DEVICE_MODEL := EA9500
  DEVICE_PACKAGES := $(BRCMFMAC_4366C0) $(USB3_PACKAGES)
  DEVICE_DTS := bcm47094-linksys-panamera
endef
TARGET_DEVICES += linksys-ea9500
```

#### EA9200
For EA9200, uncomment the following line from `target/linux/bcm53xx/image/Makefile`
```
define Device/linksys-ea9200
  DEVICE_VENDOR := Linksys
  DEVICE_MODEL := EA9200
  DEVICE_VARIANT := v1
  DEVICE_PACKAGES := $(BRCMFMAC_43602A1) $(USB3_PACKAGES)
endef
TARGET_DEVICES += linksys-ea9200
```

### Step 4 (Optional , Not tested on EA9200))
Copy `999-vX.XX-0001-ARM-dts-BCM47094-LinksysPanamera-Specify-Flash-Partitions.patch` to `target/linux/bcm53xx/patches-4.14` to allow access to some extra space at the end of the router's flash.

```
~/openwrt $ cp feeds/ea9500_openwrt/999-vX.XX-0001-ARM-dts-BCM47094-LinksysPanamera-Specify-Flash-Partitions.patch target/linux/bcm53xx/patches-4.14/
```

### Step 5
In `make menuconfig`, select `Base System -> ea9x00_support <*>`.

### Step 6
Issue the command to build OpenWrt.
```
~/openwrt $ make V=s
```
You will find the built images at `bin/targets/bcm53xx/generic`.

## Quirks
The EA9500's CPU ports are directly connected to a BCM53012 switch wired to LAN4,7,8 plus an external BCM53125 switch wired to LAN1,2,3,5,6.
This means that you will only see the internal BCM53012 ports in LuCI's switch configuration.

For some reason, the latest Broadcom firmware from `linux-firmware` (10.10.122.x) is not properly supported by brcmfmac - using the newer version will render `radio2` non-functional.
This repository includes the latest known working version of BCM4366C0 firmware for EA9500 (10.10.69.69).

## License
Files are licensed under the terms of GNU GPLv2 License; see LICENSE file for details.

## Credits
Credit goes to [Vivek Unune / npcomplete13](https://github.com/npcomplete13/openwrt) for figuring out the EA9500.
