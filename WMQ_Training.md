WMQ TRAINING
============

###Naming Best Practices:

 - QMGR: 8 characteres max
 - CHL: $QmgrSdr.$QmgrRcvr

###Runmqsc:

`runmqsc -e`: less verbose

`runmqsc -v`: dry run

`runmqsc -w 5 RQMGR`: connect to a remote QMGR (need a chl between local and remote QMGR)

###QL:

 - DLQ
 - InitQ: used by Trigger Monitor and Channel Initiator
 - CmdQ: send commands
 - EventQ
 - DefaultQ: template for queues creation
 - DLQ: QL with DLQ attribute

QA's atttributes may be differents from QL (put can be allowed on QA but not on QL...)

###MQ Explorer:

Add Remote QMGR in MQ Explorer:

1. Create channel SYSTEM.ADMIN.SVRCONN on QMGR: `> def channel(SYSTEM.ADMIN.SVRCONN) chltype(SVRCONN)`
1. Create listener LISTENER.TCP: `> def listener(LISTENER.TCP) trptype(TCP) port(1414)`
1. On MQ Explorer: Add Remote QMGR

###MQI:

 - MsgID based on 12 first characters of QMGR + timestamp
 - MaxMsgL: max size of a message (defaut: 4Mo)

MaxMSgL attribute can be configured at QMGR, QL or CHL level

###Triggers WMQ:

Trigger launched by initq

InitQ: QL called by another QL with initq() attribute

Any QL can become InitQ if called by another QL

    > def ql($QL) trigger trigtype(FIRST) process(ECHO) initq(SYSTEM.DEFAULT.INITIATION.QUEUE)
    > def process(ECHO) applicid('/opt/mqm/samp/bin/amqsech')

Start Trigger Monitor: `runmqtrm -m $QMGR -q $INITQ`

###WMQ Process

 - `runmqchi`: Channel Initiator
 - `runmqlsr`: Listener

WMQ can use shared meory and semaphores (Shared Binding)

WMQ v7 can use Isolated Binding instead of Shared Bindind (process amqzlsa instead of amqzlaa)

###Channels

Chl SDR and CHL RCVR need to share same name

MCA : Message Chl Agent

Every 50 messages (customisable), confirmation message is sent by WMQ

MaxChl and MaxActiveChl can be configured in qm.ini

TCP KeepAlive for chl can be configured in qm.ini (IMPORTANT to configure in Production!)

Test Chl status with PING command

###Distributed

MQPUT -> QR -> XMITQ -> MCA SDR -> CHL SDR -> CHLRCVR -> MCA RCVR -> QL

XMITQ use a trigger to start:
 - Chl SDR
 - Chl RCVR started by LSNR on Remote QMGR

Port 1414 started by Listener (RECOMMANDED) or inetd.conf

Best Practices to communicate with external QMGR:
 - 1 local QMGR with default xmitq to Hub QMGR
 - Hub QMGR in DMZ dedicated to external communication

Between 2 QMGR:
 - Only 1 chl sdr and 1 chl rcvr for same usage
 - if different usages (priority, message size, ...) 2 different chl between 2 QMGR

### WMQ Path

 - Binaries: /opt/mqm (/usr/mqm for AIX)
 - Datas: /var/mqm
 - QMGR: /var/mqm/qmgrs
 - Configuration: /var/mqm/mqs.ini and /var/mqm/qmgrs/$QMGR/qm.ini
 - Tools: /opt/mqm/samp
 - Logs: /var/mqm/errors and /var/mqm/qmgrs/$QMGR/errors
 - Logs (type RedoLogs Oracle): /var/mqm/logs

###Logs

Binary logs of get and put -> used to recover QMGR

Circular Logs:
- Log rotation
- Recover after system outage
- Can't recover after disk failure
- Primary and Secondary logs
- Only log persistent messages

Linear Logs:
- No rotation
- Archive only when inactives

Lock on message with circular logs:
1. Stop QMGR
1. Increase secondary logs in qm.ini
1. Restart QMGR

Display logs: `dmpmqlog` (QMGR need to be stopped)

Best Practices IBM:
 - Linear logs in production
 - Circular logs before production or for QMGR with get only (no put)

Recover for corruption, missing queue or disk failure:
1. Delete objects
2. Recreate objects with linear logs: `rcrmqobj`

Take QL image: `rcdmqimg` (cf. Redbook Backup and Recovery WebsphereMQ)

###XA Transactions

With XA Transactions, WMQ can handle databases commit (SYNCPOINT need to be configured in qm.ini)

###DLQ

DLQ can be configured with runmqdlq and Rules_Table

Rules_Table show how to handle different messages

Best Practises: Use applications specific DLQ (different of technical DLQ)

###WMQ Clusters

 - Cluster members don't need chl, remoteQ or xmitQ between them
 - Load Balancing between cluster members

Example:
 - 3 QMGR cluster members
 - QM1 and QM2: Full Repository (all objects are defined)
 - QM3: Partial Repository (only part of objects are defined, send message to full repos for objects not defined)

Cluster Chl SDR and RCVR
Each member need to have at least a chl RCVR
Full Repos need to have 1 CHL RCVR and SDR between them

All cluster members need to reach all others QMGR clusters members

Test Cluster:
 - send a message in a clusterQ not defined in partial repos, message will be forwarded to full repos

3 QL SYSTEM.CLUSTER needed for internal operations (xmitq, reposq and cmdq)

Fix cluster problems (error in definition):

1. Kill the process using it: `> dis qstatus(SYSTEM.CLUSTER.REPOSITORY.QUEUE) type(handle) pid` then `kill -9 $pid`
1. Clear QL SYSTEM.CLUSTER.REPOSITORY.QUEUE: `> clear ql(SYSTEM.CLUSTER.REPOSITORY.QUEUE)`
1. Stop WMQ: `endmqm -p $QMGR`
1. Start WMQ: `strmqm $QMGR`

Security (OAM)
--------------

###ACL

Apply security modifications: REFRESH SECURITY

OAM configuration: mq.ini

 - Add ACL: `setmqaut`
 - List ACL: `dspmqaut`
 - Export ACL: `amqoamd -s` (generate setmqauth commands)

Security on channel with mcauser

###SSL

To configure in QMGR, authinfo and channel

###Client

Connect to QMGR: MQSERVER=SYSTEM.ADMIN.SRVCONN/TCP/@IP(@PORT)

Limitations:
- Depends on network
- No triggers

WMQ Administration
------------------

### Tools

FTP Support Pack: ftp://170.225.15.40/software/integration/support/supportpacs/individual/

 - Backup QMGR objects: MS03 Support Pack
 - Events, Statistiques and accounting: MS03 Support Pack
 - SSL Config: MH03 Support Pack
 - Perf Report: MPnn and IH03 Support Pack
 - Tunning UNIX: MQconfig

### Backup

1. Backup QMGR objects: `samveqmgr` (MS03) generate mqsc files with commands to recreate objects
1. Backup files (QMGR need to be stopped):
 - /var/mqm/qmgrs/$QMRG/qm.ini
 - /var/mqm/qmgrs/$QMRG/
 - /var/mqm/qmgrs/mqs.ini
 - /var/mqm/qmgrs/log
1. Backup security objects: `amqoamd`

### Message Tracking

Check List:
1. MQPUT return code
1. Chl status
1. Message Persists
1. Message Expiration
1. Commit

Message not commited can't be viewed with a browse on QL

### Trace Route
 - `dspmqrte` send traceroute messages through the Q
 - MS0P: MQ Explorer plugin (more verbose than dspmqrte)

### Errors

Errors files:
 - /var/mqm/errors
 - /var/mqm/qmgrs/$QMGR/errors

Error files contains:
 - Logs
 - FDC (coredump)
 - Traces with `strmqtrc`, `endmqtrc` and `dspmqtrc`

Resolve Problem on Channel:

1. `stop chl($CHL)`
1. `reset chl($CHL) seqnum(1)`
1. `resolve chl($CHL) action(backout or commit)`
 - backout: send again blocked msg in xmitq
 - commit: delete blocked msg
1. `start chl($CHL)`

Message in DLQ: Check error code (hexadecimal conversion of bits 9 to 12)

Problem with MQ Explorer:

1. Check CmdSrv Proc on Remote QMGR
1. Check Listener RQMGR
1. Check chl SYSTEM.ADMIN/SVRCONN
1. Check error codes: AMQ4043 = Security or Conversion PB
1. Check qm SYSTEM.MQEXPLORER.REPLY.MODEL

Problem with chl trigger:
1. Check that chl initiator is started (`runmqchi -q SYSTEM.CHANNEL.INITQ -M CLIENT`)
1. CHECK conf QL TRIGGER TRIGDATA($CHL) INITQ(SYSTEM.CHANNEL.INITQ)

Trigger start the channel when a message comes. Don't start the channel manually!

Clear QL locked by a program with MQ Explorer: use clear via MQGET API

### HA

Cluster MQ:
 - scalability

Backup QMGR:
 - QMGR with the same configuration on remote server
 - Before backup linear logs: `reset qmgr type(advancelog)` (to pass to next log)
 - Show current log file: `dis qmstatus currlog`
 - Backup log files with tar and copy them on backup QMGR
 - Replay logs on backup QMGR: `strmqm -r` (don't start QMGR)
 - Activate QMGR: `strmqm -a QMGR`
 - Start QMGR: `strmqm`

StandyBy:
 - StandBy FailOver (only one mq licence payed)

Perfs:
 - Split Data and Logs on different FS

### Topic

Use cases:
 - message handled by more than one application
 - duplicate message in many Q

### JMS

Config Files: /opt/mqm/java/bin/JMSAdmin.config
mapping JNDI name / Q

JAR for JMS in /opt/mqm/java/lib:
 - jms.jar
 - jndi.jar
 - connector.jar
