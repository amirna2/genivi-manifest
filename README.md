# genivi-manifest

##Pre-requisites

To build GENIVI DEMO Platform for the Raspberry Pi2 from this repository it is necessary to install
google repo tool to fetch the Yocto layers recipes.
The repo tool uses a manifest found in this repository (default.xml) to fetch the Yocto layers specifically needed for this build.

Instructions for installing repo can be found here (you only need to care about the Installing Repo section):
http://source.android.com/source/downloading.html#installing-repo

##1. Fetch all git trees

Initialize your local working repository:
```
$ mkdir -p ~/projects/genivi-rpi2
$ cd ~/projects/genivi-rpi2
$ repo init -u https://github.com/amirna2/genivi-manifest.git -b master
```
Checkout all project trees:
```
$ repo sync
```
##2. Run the build setup script (this will create a build folder)

```
$ source ./buildenv/meta-ivi-rpi-init-build-env
```
##3. Edit conf/bblayers.conf and conf/local.conf

For build/conf/bblayers.conf
```
BBLAYERS ?= " \
  /home/anathoo/projects/genivi-rpi2/poky/meta \
  /home/anathoo/projects/genivi-rpi2/poky/meta-yocto \
  /home/anathoo/projects/genivi-rpi2/poky/meta-yocto-bsp \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-openembedded/meta-oe \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-openembedded/meta-ruby \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-ivi/meta-ivi \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-ivi/meta-ivi-bsp \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-genivi-demo \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-qt5 \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-raspberry \
  "
BBLAYERS_NON_REMOVABLE ?= " \
  /home/anathoo/projects/genivi-rpi2/poky/meta \
  /home/anathoo/projects/genivi-rpi2/poky/meta-yocto \
  /home/anathoo/projects/genivi-rpi2/poky/../meta-ivi/meta-ivi \
  "
```
For build/conf/local.conf
```
MACHINE ??= "raspberrypi2"
GPU_MEM = "128"
CORE_IMAGE_EXTRA_INSTALL += "wayland weston"
LICENSE_FLAGS_WHITELIST  += "commercial"
PREFERRED_VERSION_weston ?="1.6.0"
MULTI_PROVIDER_WHITELIST += " \
             virtual/libgl \
             virtual/egl \
             virtual/libgles2 \
             virtual/mesa \
             "
```

```
#Comment out to avoid bitbake error with some GPLv3 licensed components             
#INCOMPATIBLE_LICENSE ?= "GPLv3"
```
##4. Start the build

```
$ bitbake -v genivi-demo-platform
```
##5. Flash image on the SD card


Replace sdX with the correct device ID
```
$sudo umount /dev/sdX
$sudo dd if=./tmp/deploy/images/raspberrypi2/genivi-demo-platform-raspberrypi2.rpi-sdimg  of=/dev/sdX bs=128M
$sync
```
