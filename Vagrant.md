VAGRANT
=======

Create CentOS vagrant box
-------------------------

1. create vm in VirtualBox with audio and usb disabled
2. install centos by default
3. set root password to "vagrant"
4. create user named vagrant with password "vagrant"
5. set hostname to vagrant-centos64
6. install and enable openssh-server
7. shutdown vm and run :

    VBoxManage modifyvm "vagrant-centos64" --natpf1 "guestssh,tcp,,2222,,22"

8. start vm and connect it by ssh :

    ssh -p 2222 root@127.0.0.1

9. update packages
10. install wget gcc bzip2 make kernel-devel
11. install guest additions
12. create group admin and add vagrant to it
13. edit /etc/sudoers :

    #Defaults    requiretty
    Defaults    env_keep += "SSH_AUTH_SOCK"
    %admin          ALL=(ALL)       NOPASSWD: ALL

14. add vagrant ssh key :

    curl -k https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub > /home/vagrant/.ssh/authorized_keys

15. edit /etc/sysconfig/network-scripts/ifcfg-eth0 :

    ONBOOT=yes
    NM_CONTROLLED=yes
    BOOTPROTO=dhcp
    #HWADDR="XXXXXXX" # comment it

16. edit /etc/udev/rules.d/70-persistent-net.rules and comment HWADDR line
17. clean yum, /tmp and /var/tmp

    yum clean all
    rm -fr /tmp/* /var/tmp/*

18. shutdown vm and package it :

    vagrant package --output centos64-64.box --base vagrant-centos64-64
    vagrant box add centos64-64 centos64-64.box
    vagrant box list

19. create a work directory and start vm from it :

    vagrant init centos64-64
    vagrant up
    vagrant ssh
