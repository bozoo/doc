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
