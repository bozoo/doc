AIX VIOS
========

Virtual Ethernet Adapter: Virtual Ethernet used for internal communication between LPARS

Shared Ethernet Adapter: Ethernet switch (OSI layer 2) for routing

Show IOS version (AIX VIO OS): `ioslevel`

Show AIX embeded in VIOS version: `oem_platform_level`

Backup VIOS: `backupios -file /backup_dir/backup_file`

Open root shell on VIOS: `oem_setup_env`

Open VIOS shell from root shell: `su - padmin`

Enable remote admin from NIM server: `remote_management nim_server`

List softwares installed on VIOS: `lssw`

Open Virtual Terminal on partition id 1: `mkvt -id 1`

Create VG: `mkvg -vg newvg hdisk0`

Create LV: `mklv -lv lv001 1G hdisk1`

List storage pools: `lssp`

Create storage pool: `mksp -f storage_pool`

Stop VIOS: `shutdown`

Restart VIOS: `shutdown -restart`

Secure VIOS: `viosecure -level high`

Show Security level of VIOS: `viosecure -view`

Enable VIOS firewall: `viosecure -firewall on`

Show Firewall rules: `viosecure -firewall view`

Devices Management
------------------

List devices: `lsdev`

List virtual devices: `lsdev -virtual`

List slots: `lsdev -slots`

Show disks and locations: `lsdev -type disk -field name physloc`

List SCSI or FC paths: `lspath`

Disable path between a disk and a FC: `chpath -dev hdisk1 -pdev fscsi0 -op disable`

Configure device: `cfgdev -dev Name`

Remove device: `rmdev -dev Name`

Create virtual device from existing LV: `mkvdev -vdev lv00 -vadapter vhost0`

Create virtual device from physical disk: `mkvdev -vdev hdisk2 -vadapter vhost1`

remove all virtual devices using specific LV: `rmvdev -vdev lv01`

List virtual devices using a specific virtual adapter: `lsmap -vadapter vhost0`

Create SEA (Shared Ethernet Adapter) from a physical network adapter: `mkvdev -sea ent4 -vadapter ent6,ent7 -default ent6 -defaultid 8`

List SEA and Ethernet adapters: `lsmap -all -net`

List physical adapter and etherchannel available for SEA creation: `lsdev -type ent4sea`

List all adapters which can have an IP address: `lsdev -type ent4ip`

Network Management
------------------

Configure IP on a NIC: `mktcpip -hostname name -inetaddr 192.168.1.2 -interface en2 -netmask 255.255.255.0 -gateway 192.168.1.1 -nsrvdomain example.com -start`

Show VIOS IP configuration: `lstcpip`

Show routes: `lstcpip -routtable`

List ethernet adapters: `lstcpip -adapters`

Liste hosts mapped on specific IP: `hostmap -ls`

Map host on IP: `hostmap -addr 192.168.2.2 -hosts server1`

Allow IP Forwarding (like a router): `optimizenet –set ipforwarding=1`
