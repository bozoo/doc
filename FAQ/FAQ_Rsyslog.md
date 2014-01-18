FAQ RSYSLOG
===========

# Remote rsyslogd
# for redhat5 : 

# /etc/sysconfig/rsyslog :
 SYSLOGD_OPTIONS="-m 0 -r514 -x"

# for redhat6 :

# /etc/rsyslog.conf :
# Provides UDP syslog reception
$ModLoad imudp.so
$UDPServerRun 514

# Provides TCP syslog reception
$ModLoad imtcp.so
$InputTCPServerRun 514

# configure logfile on 10.1.1.2 server for 10.1.1.1 client:

# /etc/rsyslog.conf :
:FROMHOST-IP, isequal, "10.1.1.1"           /var/log/10.1.1.1.log

# configure remote log from 10.1.1.1 client to 10.1.1.2 server:

# /etc/rsyslog.conf:
user.*          @@10.1.1.2:514 # @ for tcp / @@ for udp