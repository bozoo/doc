KVM LINUX
=========

http://doc.ubuntu-fr.org/kvm

Prerequisites:

    apt-get install cpu-checker qemu-kvm qemu-utils virt-manager
    adduser $myuser kvm
    kvm-ok

Directories:

images dir : /var/lib/libvirt/images
config dir : /etc/libvirt/qemu/

Tools
-----

 - virsh commands:

List started VM : virsh list
Start VM : virsh create ${VM}.xml # ${VM}.xml : VM config file
           virsh start ${VM}
Stop VM (forced) : virsh destroy ${VM}
Stop VM (clean) : virsh shutdown ${VM}
Show VM info : virsh dominfo ${VM}

Connect to VM : virsh console ${VM}

escape character :
- on azerty : CTRL + ALTGR + 8
- on mac : CTRL + 6

 - qemu-img commands:

Convert IMG : qemu-img convert -c -f raw -O qcow2 file.img file.qcow2

 - Other tools

    apt-get install libguestfs-tools
    virt-edit
    virt-ls
    virt-cat
    ...

Allow virsh console to work
---------------------------

Add the following XML in domain's XML (using "virsh edit")

    <serial type='pty'>
       <target port='0'/>
    </serial>
    <console type='pty'>
       <target type='serial' port='0'/>
    </console>

Edit Grub config

on CentOS:

    add "console=ttyS0,115200" to /boot/grub/grub.conf

on Debian:

    uncomment line "T0:23:respawn:/sbin/getty -L ttyS0 9600 vt100" in /etc/inittab
    add GRUB_CMDLINE_LINUX="console=ttyS0" to /etc/default/grub
    update-grub

Configure Network Bridge

    apt-get install uml-utilities openvswitch-brcompat

    cat << EOF >> /etc/networking/interfaces
    auto br0

    iface br0 inet dhcp
        bridge_ports    eth0
        bridge_stp      off
        bridge_maxwait  0
        bridge_fd       0
    EOF

Clone VM

    virt-clone -o _centos6 -n centos601 -f /var/lib/libvirt/images/centos601.qcow2
