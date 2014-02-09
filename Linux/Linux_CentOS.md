LINUX CENTOS
============

Packages to install: `yum install open-ssh nmon vim nmap sysstat wget nc`

CentOS base install for virtualization
--------------------------------------

Disable DNS for SSH

    sed -i 's/#UseDNS no/UseDNS yes/' /etc/ssh/sshd_config
    service sshd restart

Delete specific hardware config (MAC and UUID) for eth0

    sed -i '/HWADDR/d' /etc/sysconfig/network-script
    sed -i '/UUID/d' /etc/sysconfig/network-script
    sed -i '/ATTR{address}/d' /etc/udev/rules.d/70-persistent-net.rules

RPMS Management
---------------

Upgrade Server with frozen RPM:

 - install plugin yum-versionlock
 - add frozen rpms to /etc/yum/pluginconf.d/versionlock.list
 - `yum upgrade --skip-broken`
