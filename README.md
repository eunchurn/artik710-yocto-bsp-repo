# YOCTO BSP for Samsung Artik

## Build Packages

```bash
$ sudo apt install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat cpio python python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping
```

To get the BSP you need to have `repo` installed and use it as:

Install the `repo` utility:

```bash
$ sudo apt install repo
```

or

```bash
$ mkdir ~/bin
$ curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
$ PATH=${PATH}:~/bin
```
Download the BSP source:

```bash
$ mkdir yocto-artik && cd $_
$ repo init -u https://github.com/eunchurn/artik710-yocto-bsp-repo -b morty
$ repo sync
```

At the end of the commands you have every metadata you need to start work with.

To start a simple image build:

```bash
$ MACHINE=artik710 DISTRO=poky source setup-environment build
$ bitbake core-image-minimal
```
It takes 2 or 3 hours. You can use any directory to host your build.

In the meantime create two partitions on your microSD. You can use gparted on your Ubuntu PC.

| path    | file system |
|:-------:|:-----------:|
|  boot   |     ext4    |
|  rootfs |     ext4    |

At the end you will get the following files, copy tem into the microSD:

```bash
$ cd ~/yocto-artik/poky/build/tmp/deploy/images/artik710
$ sudo cp Image /media/boot/
$ sudo cp Image-s5p6818-artik710-raptor-rev00.dtb /media/boot/s5p6818-artik710-raptor-rev00.dtb
$ sudo cp Image-s5p6818-artik710-raptor-rev01.dtb /media/boot/s5p6818-artik710-raptor-rev01.dtb
$ sudo cp Image-s5p6818-artik710-raptor-rev03.dtb /media/boot/s5p6818-artik710-raptor-rev03.dtb
$ cp ../../../../../sources/meta-artik710/recipes-bsp/u-bootu-boot.bin /media/boot/
$ cp u-boot-artik710.bin /media/boot/
```

## Copying image to SD-card

- Format SD Card and mount it. (SD-card device find `/dev/sd[X]1/` with running `$ sudo fdisk -l`)

```bash
$ sudo mkfs.ext4 /dev/sd[X]1
$ sudo mount /dev/sd[X]1 /mnt
```

- Copy compiled binaries(`fip-nonsecure.img`, `params.bin`) into a micro sd card.
  
```bash
$ sudo cp fip-nonsecure.img params.bin /mnt
$ sudo cp ../boot-firmwares-artik710/partmap_emmc.txt /mnt
$ sudo umount /mnt
```

- Insert the microSD card into your board and enter u-boot shell during boot countdown

```bash
# sd_recovery mmc 1:1 48000000 partmap_emmc.txt
```
