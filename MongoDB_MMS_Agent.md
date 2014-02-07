MongoDB_MMS_Agent
=================

[Documentation](https://mms.mongodb.com/help-hosted/v1.2/monitoring/)

MMS Server : monitoring server
MMS Agent : sonde for mongodb monitoring
MMS Node : mongodb monitored by MMS Agent

Sysctl parameters for MMS Server
--------------------------------

    net.core.netdev_max_backlog = 30000
    net.core.wmem_max = 16777216
    net.core.rmem_max = 16777216

Install MongoDB on MMS Server
-----------------------------

    yum install mongo-10gen mongo-10gen-server
    service mongod start
    chkconfig mongod on

Install MMS Server
------------------

    rpm -ivh 10gen-mms-<version>.x86_64.rpm
    service 10gen-mms start
    chkconfig 10gen-mms on

RPM install dir is /opt/10gen/mms

Install MMS Agent
-----------------

    yum install python-setuptools
    yum install gcc python-devel
    easy_install pymongo

Start MMS Agent
---------------

    nohup python agent.py > /[LOG-DIRECTORY]/agent.log 2>&1 &

Register MMS nodes
------------------

Create user on mongod:

    db.addUser( { user:"mms",
                  pwd:"password",
                  roles : [ "clusterAdmin", "readAnyDatabase", "dbAdminAnyDatabase" ] } )

Hardware Monitoring
-------------------

Install Munin agent on MMS Nodes:

    yum install munin-node munin-common # RPM from EPEL
    service munin-node start
    chkconfig munin-node on

Open port 4949 from MMS Agent to MMS Nodes

Add MMS Server to /etc/hosts

Edit /etc/munin/munin-node.conf:

    allow ^192\.168\.1\.1$ # IP of MMS Server. Doesn't work with the hostname

Relink missing plugins on RedHat:

    sudo ln -s /usr/share/munin/plugins/iostat /etc/munin/plugins/iostat
    sudo ln -s /usr/share/munin/plugins/iostat_ios /etc/munin/plugins/iostat_ios

Change rights for Munin iostat:

    touch /var/lib/munin/plugin-state/iostat-ios.state
    chown -R [username]:[group] /var/lib/munin/plugin-state/
    chmod -R 660 /var/lib/munin/plugin-state/

Restart Munin agent:

    sudo /etc/init.d/munin-node restart
