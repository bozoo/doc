Linux
=====

SSH
---

Allow AgentForwarding:
- Putty: select "Allow agent forwarding" in "Connection"/"SSH"/"Auth"
- /etc/ssh_config: add line `ForwardAgent yes`
- /etc/sshd_config (for RHEL 6): add line `AllowAgentForwarding yes`

LVM and Disk Management
-----------------------

force multipath names in /etc/lvm/lvm.conf:

    filter = [ "a|/dev/mapper/mpath|", "a|/dev/sda|", "r|.*|" ]
    preferred_names = [ "^/dev/mpath/", "^/dev/mapper/mpath", "^/dev/sd" ]

Show FC WWN: `cat /sys/class/scsi_host/hostX/device/fc_host\:hostX/port_name` (X for port number)

Discover SAN disk: `for host in $(ls /sys/class/fc_host); do echo "1" > /sys/class/fc_host/${host}/issue_lip; done`

Discover SCSI disk: `for host in $(ls /sys/class/scsi_host); do echo "---" > /sys/class/scsi_host/${host}/scan; done`
                 or `for host in $(ls /sys/class/scsi_host); do echo "- - -" > /sys/class/scsi_host/${host}/scan; done`

Remove SAN disk:

1. Remove PV: `pvremove /dev/$disk`
1. Remove disk on SAN system
1. Remove disk on Linux (Remove 2 path for multipath): `echo 1 > /sys/block/sdX/device/delete; echo 1 > /sys/block/sdY/device/delete`
1. Restart multipath: `service multipathd restart`

Move LV to another VG:

1. Create same LV on other VG
1. Umount FS
1. Copy LV: `dd if=/dev/vg00/lv-to-move of=/dev/vg01/lv-to-move`
1. Change Mount line in /etc/fstab: `sed -i 's/vg00\/lv-to-move/vg01\/lv-to-move/' /etc/fstab`
1. Remount FS
1. Remove old LV: `lvremove vg00/lv-to-move`

or:

1. Umount FS
1. Disable LV: `lvchange -an <lvname>`
1. Extend VG with new disk
1. Move LV on new disk: `pvmove -n <lvname> <pvname>`
1. Split disk on new VG: vgsplit `<src vgname> <dst vgname> <pvname>`
1. Enable LV: `lvchange -ay <lvname>`
1. Modify /etc/fstab
1. Remount FS


Shrink FS:

```shell
lv=$1;size=$2
fs=$(grep $lv /etc/fstab | awk '{print $2}')
umount $fs
service multipathd restart
e2fsck -f /dev/vg01/$lv
resize2fs -p /dev/vg01/$lv ${size}
lvreduce -L ${size} /dev/vg01/$lv
resize2fs -p /dev/vg01/$lv
mount $fs
```

Resize SCSI disk (on VM for example):

1. Rescan disk: `echo 1 > /sys/block/sdX/device/rescan`
1. Check new size with fdisk
1. Create new partition on additional storage with fdisk
1. Run `partprobe` to reflect change
1. Create PV on new partition: `pvcreate /dev/sdX2`
1. Extend VG: `vgextend vg0X /dev/sdX2`

or

1. Rescan disk: `echo 1 > /sys/block/sdX/device/rescan`
1. Resize PV: `pvresize /dev/sdX`

Identify SAN system for LUN allocated: `multipath -ll | awk --non-decimal-data '/^mpath/ {x = "0x"substr($2,23,4) ; printf("san connected: %d\n",x)}' | sort | uniq`

Display mapping LV/PV: `lvs -a -o +devices`
Disable space reserved to root on FS: `tune2fs -m 0 $lv`
Display total storage available on server: `df -m | awk 'BEGIN { a = 0; } { a += $4 } END { print "Total avail space: " a"MB" }'`

Network Management
------------------

Identify VLAN: `tcpdump -vv -n -s 1500 -c 1 -f "ether[20:2]==0x2000" -i eth0`

AutoFS
------

    /etc/exports:          /exports     localhost(rw)
    /etc/automaster:     /imports     /etc/auto.imports
    /etc/auto.imports:     archives     -rw     localhost:/exports/archives


ACL
---

1. `tune2fs -l <fs> | grep acl` or `grep acl /etc/fstab`
1. `getfacl`
1. `setfacl` (`setfacl -m u:user01:--- /file; setfacl -m u:user02:rw- /file`)

Quotas
------

/etc/fstab: `/dev/mapper/vg_rhel01-lv_root / ext4 defaults,usrquota 1 2`

    quotacheck -cum /
    quotaon -v /
    edquota
    repquota

Chroot
------

1. Add setsuid to chroot: `chmod +s /usr/sbin/chroot`
1. Create home directory: `mkdir /home/chroot && chgrp chroot /home/chroot`
1. Copy binaries and libraries for chroot:

```
cd /home/chroot
mkdir bin usr lib64 proc
cp /bin/bash /bin/ls /bin/cat /bin/more /usr/bin/tail bin/
cp /lib64/libtermcap.so.2 /lib64/libdl.so.2 /lib64/libc.so.6 /lib64/ld-linux-x86-64.so.2 \
   /lib64/librt.so.1 /lib64/libacl.so.1 /lib64/libselinux.so.1 /lib64/libpthread.so.0 \
   /lib64/libattr.so.1 /lib64/libsepol.so.1 lib64/
cp /bin/ps /bin/netstat bin/
cp /lib64/libproc-3.2.7.so lib64/
```

1. Mount /proc and usefull FS with "bind":

```
cat << EOF >> /etc/fstab
/proc              /home/chroot/proc    none  bind,ro  0 0
/etc/httpd/conf    /home/chroot/conf    none  bind,ro  0 0
/etc/httpd/conf.d  /home/chroot/conf.d  none  bind,ro  0 0
/etc/httpd/logs    /home/chroot/logs    none  bind,ro  0 0
EOF
```

1. Create mount dir in chroot home dir: mkdir -p /home/chroot/{proc,conf,conf.d,logs}
1. Create chroot shell:

```shell
cat 1> /usr/local/bin/chrootshell <<CHROOT
#!/bin/bash
cat <<EOF
---------------------------------
Bienvenue !
Vous êtes sur un système chrooté
---------------------------------
EOF
/usr/sbin/chroot /home/chroot /bin/bash
CHROOT
```

1. Add execution rights to chrootshell: `chmod +x /usr/local/bin/chrootshell`
1. Add chrootshell to /etc/shells: `echo /usr/local/bin/chrootshell >> /etc/shells`
1. Change shell and homedir for chrooted users: `usermod -s /usr/local/bin/chrootshell -d /home/chroot $user`

SELinux
-------

    getenforce / setenforce
    getsebool / setsebool
    semanage fcontext
    restorecon -RFvv
    ll -Z
    ps -Z
    getsebool -a | grep http
    semanage boolean -l | grep http

Chiffrement FS
--------------

    cryptsetup --verbose --verify-passphrase luksFormat /dev/mapper/vg_rhel-lv_luks
    cryptsetup luksOpen /dev/mapper/vg_rhel-lv_luks mnt_luks
    mkfs.ext4 /dev/mapper/mnt_luks
    mount /dev/mapper/mnt_luks /mnt/luks

    /etc/crypttab:
    /dev/mapper/vg_rhel-lv_luks mnt_luks none


    /etc/fstab:
    /dev/mapper/mnt_luks /mnt/luks ext4 defaults 1 2

Iptables
--------

    iptables -P INPUT DROP
    iptables -F
    iptables -A INPUT -s 127.0.0.1 -j ACCEPT
    iptables -I INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
    iptables -I INPUT -s 192.168.0.0/24 -p icmp --icmp-type any -j ACCEPT
    iptables -I INPUT -p tcp --dport 22 -m state --state NEW -j ACCEPT
    service iptables save

Postfix
-------

Flush locked mails: `mailq | grep ^[0-9A-Z] | awk '{print $1}'|sed 's/*//' > mail-list.txt && for i in $(cat mail-list.txt); do postsuper -d $i; done`

Tuning
------

### For directories with thousands of files:

 - Add option `dir_index` to FS (ext4) with `tune2fs`
 - Split in subdir with 10000 files max (ex: use 2 first characters of file's md5sum as hashing key)
 - store files in database (blob) or indexed files (GDBM)
 - Mount FS with `noatime` option

### For NFS:

Fore rsize and wsize (block size in read / write): default value is "1024" but better performances with "8192"

Mount option: `hard,intr`

VirtualBox
----------

Mount VBox Shared Folder: `mount -t vboxsf <Share> /mnt`
