AIX
===

Troubleshoot
------------

Can't connect with SSH key:
On AIX password must be set to connect with SSH key (even if it isn't used)

Hardware
--------

Upgrade Firmware:

    invscout

LVM
---

 - VGDA (Volume group descriptor area): Metadatas LVM on PV
 - LVCB (Logical volume control block): Metadatas LVM on LV

Create PVID of a disk:

    chdev -l hdisk3 -a pv=yes

Remove PVID of a disk:

    chdev -l hdisk3 -a pv=clear

Lock new PP allocation on a PV:

    chpv -an hdisk2

Change disk state to unavailable:

    chpv -vr hdisk2

Remove disk boot record:

    chpv -c hdisk1

increase max LV allocation:

    0516-787 extendlv: Maximum allocation for logical volume xxxxlv is 1024.
    chlv -x 2048 xxxxlv

Check and fix STALE PP problems:

    for vg in $(lsvg); do echo "$vg - `lsvg $vg | awk '/STALE/ {print $4,$5,$6}'`"; done
    syncvg -v $vg

Create FS:

    /usr/sbin/mklv -y<lvname> -tjfs2 <vgname> 1
    /usr/sbin/crfs -v jfs2 -d<lvname> -m<mnt_dir> -A -prw -a agblksize=4096 -a logname=INLINE
    mount <mnt_dir>
    chfs -a size=<size> <mnt_dir>

Show disk size:

    lsattr -El hdiskX

Print Management
----------------

Start print queue:

    enq -U –P $IMP

Stop print queue:

    enq -U –P $IMP

Cancel print job:

    enq -P $IMP -x $JOB

Show all print queues status:

    enq -q -A

Show specific print queue status:

    enq -q -P $IMP

Network Management
------------------

Remove duplicate route:

    lsattr -El inet0
    chdev -l inet0 -a delroute="net,-hopcount,0,,0,X.X.X.X"

Check speed and duplex for a NIC:

    entstat -d entX | grep -i speed

print MAC address for a NIC:

    lscfg -vpl ent0

Users Management
----------------

Change max length of username:

    getconf LOGIN_NAME_MAX
    lsattr -El sys0 -a max_logname
    chdev -l sys0 -a max_logname=<new_value>

AIO Servers Management
----------------------

Show min/max AIO Servers numbers:

    lsattr -El aio0

Show number of AIO Servers process:

    ps -ek | grep aioserver | wc -l

Change AIO servers number:

    chdev aio0


NFS Management
--------------

Use AIX NFS client with Linux NFS server:

    nfso -p -o nfs_use_reserved_ports=1

Mount NFS share:

    mknfsmnt -f /path/to/mount -d /remote/path -h nfsserver -M sys -B -A -t rw -w bg -Y -Z -X -H

Other
-----

Activation fullcore (for coredump):

    chdev -l sys0 -a fullcore=true
    lsattr -El sys0 | grep fullcore

Show $user@$server in SSH window title:

    echo 'echo "\\033]0;$USER@`hostname`\\007"' >> /etc/profile

Maintenance
-----------

Use alternate rootvg on a second disk:

    alt_disk_copy -B -d hdisk1



Best Practices
--------------

1. Install openssh and use SSH keys

1. Configure Syslog:

    ```
    cat << EOF >> /etc/syslog.conf:
    *.debug                 /var/adm/ras/syslog/syslog.out     rotate size 1m files 7
    EOF

    mkdir /var/adm/ras/syslog
    touch /var/adm/ras/syslog/syslog.out
    refresh -s syslogd
    ```

1. Install sudo and use user with sudo root instead of root

1. Schedule background nmon

1. Schedule logs cleaning for:

    ```
    /usr/lpp/*/deinstl
    /var/adm/wtmp
    /etc/security/failedlogin
    /var/adm/wtmp
    /smit.log
    ```

1. Add start/stop scripts in /etc/rc.d/rc2.d/Src.local and /etc/rc.d/rc2.d/Krc.local

1. Create dedicated sysdump LV:

    ```
    /var/adm/ras/sysdumpdev -l
    /usr/lib/ras/dumpcheck -p
    /usr/sbin/mklv -y lg_dumplv -t sysdump rootvg 1
    ```

1. Create mksysb after install and before servers upgrade

1. Configure /etc/profile to show $user@$server in SSH window title:

    ```
    echo 'echo "\\033]0;$USER@`hostname`\\007"' >> /etc/profile
    ```

1. Configure alias and environments variables in /etc/security/.profile (ex: `alias ll='ls -l'`)

1. Configure host resolution to host then DNS:

    ```
    cat << EOF >> /etc/netsvc.conf
    hosts=local,bind
    EOF

    cat << EOF >> /etc/environment
    NSORDER=local,bind
    EOF
    ```

1. Configure NTP

1. Use WLM to reserve CPU resources to root

1. Create FS /usr/local with admin scripts in /usr/local/bin /usr/local/sbin and add them to PATH

1. Change root home dir to /root
