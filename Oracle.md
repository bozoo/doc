ORACLE
======

Stop Oracle (standalone):

    sqlplus / as sysdba
    shutdown immediate
    lsnrctl stop

Stop Oracle (dataguard):

    sqlplus / as sysdba
    recover managed standby database cancel;
    shutdown immediate
    lsnrctl stop

Stop Oracle (standalone + ASM):

    sqlplus / as sysdba
    shutdown immediate
    lsnrctl stop
    crsctl stop resource -all

Stop Oracle (dataguard + ASM):

    sqlplus / as sysdba
    recover managed standby database cancel;
    shutdown immediate
    lsnrctl stop
    crsctl stop resource -all

ASM
---

Find which san volume is associated with an ASM disk:

    /etc/init.d/oracleasm listdisks
    VOLUME1
    VOLUME2

    /etc/init.d/oracleasm querydisk -d VOLUME2
    Disk "VOLUME2" is a valid ASM disk on device [253, 11]

    ls -lsa /dev/dm-1*
    0 brw-rw---- 1 root root 253, 10 Mar 17 11:44 /dev/dm-10
    *0 brw-rw---- 1 root root 253, 11 Mar 23 14:47 /dev/dm-11

The minor/major from VOLUME2 are the /dev/dm-11's minor/major

Automatic Memory Management
---------------------------

With Oracle11 AMM, Oracle can dynamically manage SGA and PGA in one chunk of memory:

1. Set `MEMORY_TARGET` and `MEMORY_MAX_TARGET` parameters in Oracle configuration (initial and max memory used for both SGA and PGA).
1. Allocate more memory for the shared memory FS (`/dev/shm`) than `MEMORY_MAX_TARGET`.

Example:
    MEMORY_TARGET=2G
    MEMORY_MAX_TARGET=5G
    mount -t tmpfs shmfs -o size=6G /dev/shm
    echo "tmpfs /dev/shm    tmpfs   size=6G 0 0" >> /etc/fstab
