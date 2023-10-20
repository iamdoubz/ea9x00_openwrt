ea9x00_openwrt
=====

# Compile steps for Linksys EA9200

## Prerequisites

Source: https://openwrt.org/docs/guide-developer/toolchain/install-buildsystem

### Debian/Ubuntu
```
sudo apt update && sudo apt install build-essential clang flex bison g++ gawk gcc-multilib g++-multilib gettext git libncurses-dev libssl-dev python3-distutils rsync unzip zlib1g-dev file wget
sudo apt install libboost-dev libxml-parser-perl libusb-dev bin86 bcc sharutils gcc-multilib b43-fwcutter zip openjdk-8-jdk-headless libpam0g-dev liblzma-dev libsnmp-dev libpcre2-dev libacl1-dev libattr1-dev
#pcre2-dev python-tomli python-poetry-core python3-editables python3-pathspec python3-trove-classifiers python-pluggy python-typing-extensions
```

## Build system usage

Source: https://openwrt.org/docs/guide-developer/toolchain/use-buildsystem

### Steps

1. Pull in openwrt source code (~191MB)
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
4. Update and install the feeds (whatever this means) (~240MB)
```
./scripts/feeds update -a
./scripts/feeds install -a
```
5. Ensure the EA9200 target is enabled
```
nano +276 target/linux/bcm53xx/image/Makefile
```
6. Configure the firmware image
```
make menuconfig
```
  - Target System --> BCM47XX/53XX (ARM)
  - Target Profile --> Linksys EA9200 v1
  - Right arrow to "Save"
  - See [my.config](my.config) for all the packages I (pre-)installed
7. Download all sources upfront
```
make download
```
8. Build the image
```
make -j $(($(nproc)+1)) V=s
```
9. Your new build will be in the `bin/targets/bcm53xx/generic` folder
```
cd bin/targets/bcm53xx/generic
ls -lh
```
