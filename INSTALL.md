# Compile steps for Linksys EA9200

## Prerequisites

Source: https://openwrt.org/docs/guide-developer/toolchain/install-buildsystem

### Debian/Ubuntu
```
sudo apt update && sudo apt install build-essential clang flex bison g++ gawk gcc-multilib g++-multilib gettext git libncurses-dev libssl-dev python3-distutils rsync unzip zlib1g-dev file wget
sudo apt install libboost-dev libxml-parser-perl libusb-dev bin86 bcc sharutils gcc-multilib b43-fwcutter zip openjdk-8-jdk-headless
sudo apt install libpam0g-dev liblzma-dev libsnmp-dev libpcre2-dev
#pcre2-dev python-tomli python-poetry-core python3-editables python3-pathspec python3-trove-classifiers python-pluggy python-typing-extensions
```

## Build system usage

Source: https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem

### Steps

1. Pull in operwrt source code (~191MB)
```
git clone https://git.openwrt.org/openwrt/openwrt.git
```
2. Find what version of openwrt you want to use
```
git branch -a && git tag
```
3. Checkout that version
```
git checkout opernwrt-22.03
```
4. Add the `hurrian` EA9500 repo to the default feed file
```
nano feeds.conf.default
src-git ea9500_openwrt https://github.com/hurrian/ea9500_openwrt.git
```
5. Update and install the feeds (whatever this means) (~240MB)
```
./scripts/feeds update -a
./scripts/feeds install -a
```
6. Add a link to the ea9500 feed files
```
ln -s feeds/ea9500_openwrt/files files
```
7. Ensure the EA9200 target is enabled
```
nano +276 target/linux/bcm53xx/image/Makefile
```
8. Configure the firmware image
```
make menuconfig
```
   a. Target System --> BCM47XX/53XX (ARM)
   b. Target Profile --> Linksys EA9200 v1
   c. Right arrow to "Save"
   d. TBD: show all the custom things I added like Adblock, nginx, etc.
9. Download all sources upfront
```
make download
```
10. Build the image
```
make -j $(($(nproc)+1)) V=s
```
11. Your new build will be in the `bin/targets/bcm53xx/generic` folder
```
cd bin/targets/bcm53xx/generic
ls -lh
```

## Install openwrt on router

Be sure you have firmware version 1.1.5. If you have anything higher, flashing will not be possible!

Here is link to that firmware: https://downloads.linksys.com/downloads/firmware/FW_EA9200_1.1.5.164615_prod.img

### Steps


