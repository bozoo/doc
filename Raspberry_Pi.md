RASPBERRY PI
============

Emulate PI with Qemu
--------------------

Download Raspbian image and Linux kernel:

    wget http://downloads.raspberrypi.org/raspbian_latest
    wget http://xecdesign.com/downloads/linux-qemu/kernel-qemu

Extract Raspbian image:

    unzip raspbian_latest

First boot:

    qemu-system-arm -kernel kernel-qemu -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw init=/bin/bash" -hda 2014-01-07-wheezy-raspbian.img

On Qemu:

    vi /etc/ld.so.preload and comment /usr/lib/arm-linux-gnueabihf/libcofi_rpi.so

    cat << EOF >> /etc/udev/rules.d/90-qemu.rules
KERNEL=="sda", SYMLINK+="mmcblk0"
KERNEL=="sda?", SYMLINK+="mmcblk0p%n",
EOF

    halt

Second Boot:

    qemu-system-arm -kernel kernel-qemu -cpu arm1176 -m 256 -M versatilepb -no-reboot -serial stdio -append "root=/dev/sda2 panic=1 rootfstype=ext4 rw" -hda 2014-01-07-wheezy-raspbian.img
