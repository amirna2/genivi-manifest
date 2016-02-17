# genivi-manifest
Repo manifest for fetching GENIVI and Raspberry Pi 2 Yocto layers

This manifest is a 'repo' manifest [1] that manages the trees for this
Yocto project. Any OpenEmbedded based distribution ends up as a large collection 
of git trees (oe-core, bitbake, meta-oe, ...), and 'repo' is used as a tool to manage this collection.

[1] http://code.google.com/p/git-repo/

Build Instructions:

##1. Install 'repo'

The first thing to do in order to use this manifest, is to install
the 'repo' tool wrapper, and that needs to be done on each machine (or user).

The following instructions can be used:
```
$ curl https://raw.githubusercontent.com/amirna2/genivi-manifest/master/repo > /tmp/repo
$ chmod a+x /tmp/repo
$ sudo mv /tmp/repo /usr/local/bin/
```
Alternatively, if you don't have 'administrative' permission, or prefer to
install in a user $HOME folder, you can do something along these lines:
```
$ mkdir ~/bin
$ curl https://raw.githubusercontent.com/amirna2/genivi-manifest/master/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
$ export PATH=~/bin:$PATH
```
Do not forget to add ~/bin permanently to your PATH.

##2. Fetch all git trees


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
##3. Run the build setup script (this will create a build folder)

```
$ source ./buildenv/meta-ivi-rpi-init-build-env
```
##4. Edit conf/bblayers.conf and conf/local.conf

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
##5. Start the build

```
$ bitbake -v genivi-demo-platform
```
##6. Flash image on the SD card


Replace sdX with the correct device ID
```
$sudo umount /dev/sdX
$sudo dd if=./tmp/deploy/images/raspberrypi2/genivi-demo-platform-raspberrypi2.rpi-sdimg  of=/dev/sdX bs=128M
$sync
```
