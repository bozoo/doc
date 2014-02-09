AIX NIM
=======

NIM Resources
-------------

 - lpp_source: directory for AIX filesets in format BFF (Backup File Format) or RPM

 - spot: directory for files used to create boot image (for remote install) and server install (kernel, binaries, libraries, shell scripts, ...)

 - mksysb: rootvg image used for system restoration or reinstall

 - bosinst_data: answer file for unattend install

 - resolv_conf: file with DNS information needed for install

 - script: file with post-install commands (ex: create user, create FS, ...)

Directories and files used for NIM
----------------------------------

 - /export/nim/lpp_source (6Go min)
 - /export/nim/spot (2Go min)
 - /export/nim/mksysb (1,5Go for each mksysb)
 - /export/nim/res: bosinst_data, image_data et scripts
 - /tftpboot: tftp boot images
 - /etc/bootptab: used by bootp
 - /etc/exports: used by nfsd
 - /etc/niminfo: used by all NIM commands
 - /tftpboot: NIM boot images
 - /var/adm/ras: NIM master logs
 - /usr/adm/ras/nimlog: failed NIM operations logs (`aloi -f /usr/adm/ras/nimlog -o`)

Filesets to install NIM: bos.net.tcp.server, bos.net.nfs.server, bos.sysmgt.nim.master, bos.sysmgt.nim.spot

Nim Master install on AIX 5.2
-----------------------------

1. Create VG nimvg

1. Install NIM server:
   - using script `nim_master_setup`
   - or `nimconfig –a netname=net_10_1_1 –a pif_name=en0 –a netboot_kernel=mp –a cable_type=tp –a client_reg=noz`

   This create following directories:
   - /export/nim/spot/spot_52
   - /export/nim/lpp_source/lpp_source_52
   - /tftpboot

Create lpp_source on AIX 5.2
----------------------------

1. Create FS /media for AIX CD copies

1. Insert first AIX 5.2 Install CD

1. Copy filesets from CD in /media/AIX/AIX_52 with `smit bffcreate`

1. Update metadatas with `inutoc`

NIM master configuration
------------------------

1. Create network resource: `smit nim_mknet`

1. Create LPP resource:
   - `smit nim_mkres`
   - or `nim –o define –t lpp_source –a server=master –a location=/export/lpp_source/lpp5300 –a source=/dev/cd0 lpp5300`

1. Create spot resource:
   - `smit nim_mkres`
   - or `nim –o define –t spot –a server=master –a location=/export/spot –a source=lpp5300 –a installp_flags=-aQg spot5300`

1. Create mksysb resource:
   - `smit nim_mkres`
   - or `nim –o define –t mksysb –a source=<machine> -a server=master –a location=<resource name>`

1. Add client:
   - `smit nim_mkmac`
   - or `nim –o define –t standalone –a if1=”net_10_1_1 lpar55 0 ent0” LPAR55`

1. Check NIM resources NIM: `lsnim -l`

Install NIM client
------------------

1. Create bosinst_data resource:

   - `smit nim_bosinst`
   - or `nim –o allocate –a spot=spot5304 –a lpp_source=lpp5304 LPAR55`
     then `nim –o bos_inst –a source=spot5304 –a installp_flags=agX –a accept_licenses=yes LPAR55`

1. Start server in MMS mode and select "Setup Remote IPL" to configure starting on Nim master

Add filesets to spot
--------------------

1. List spots on NIM master: `lsnim -t spot`

1. List lpp_source: `lsnim -t lpp_source`

1. Check if fileset is in the spot: `nim -o showres <SPOT_NIM> | grep -i <FILESET>`

1. Check if fileset is in lpp_source: `nim -o showres <LPPSOURCE_NIM> | grep -i <FILESET>`

1. Install fileset in spot from lpp_source: `nim -o cust -a filesets=<FILESET> -a lpp_source=<LPPSOURCE_NIM> <SPOT_NIM>`

Check NIM resources
-------------------

1. List NIM resources NIM: `lsnim -l`

1. List machines: `lsnim -c machines`

1. List other NIM resources: `lsnim -t spot / lsnim -t lpp_sources / lsnim -t mksysb / lsnim -c networks`

NIM Maintenance
---------------

Check NIM master: `lsnim -l client, tail -l /etc/bootptab, ls -l /tftpboot, showmount -e`

Recreate /etc/niminfo file on NIM master: `nimconfig –r`

Recreate /etc/niminfo in NIM client: `niminit –a master=<MASTER> -a name=<NAME>`

Check lpp_source: `nim –Fo check <LPP_SOURCE>`

Check spot: `nim -o check <SPOT>`

Backup NIM DB: `smit nim_backup_db`
