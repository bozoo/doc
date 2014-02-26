CIS Red Hat Enterprise Linux 6 Benchmark
========================================

Filesystem Configuration
------------------------

    /dev/vg00/lvtmp   /tmp            ext4  nodev,nosuid,noexec 0 0
    /dev/vg00/lvvar   /var            ext4  defaults  0 0
    /tmp              /var/tmp        none  bind 0 0
    /dev/vg00/lvlog   /var/log        ext4  defaults 0 0
    /dev/vg00/lvaudit /var/log/audit  ext4  defaults 0 0
    /dev/vg00/lvhome  /home           ext4  nodev 0 0
    shmfs             /dev/shm        tmpfs nodev,nosuid,noexec 0 0

Set Sticky Bit on All World-Writable Directories:

    df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type d \( -perm -0002 -a ! -perm -1000 \) 2>/dev/null | xargs chmod a+t

Configure Software Updates
--------------------------

Verify Red Hat GPG Key is Installed: `gpg --quiet --with-fingerprint /etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release`

Verify that gpgcheck is Globally Activated: `echo "gpgcheck=1" >> /etc/yum.conf`

Disable the rhnsd Daemon: `chkconfig rhnsd off`

Verify Package Integrity Using RPM: `rpm -qVa | awk '$2 != "c" { print $0}'`

SELinux
-------

Set the SELinux State: `echo "SELINUX=enforcing" >> /etc/selinux/config`

Set the SELinux Policy: `echo "SELINUXTYPE=targeted" >> /etc/selinux/config`

Remove SETroubleshoot: `yum remove setroubleshoot`

Remove MCS Translation Service: `yum remove mcstrans`

Additional Process Hardening
----------------------------

Restrict Core Dumps:

    echo "hard core" >> /etc/security/limits.conf
    echo "fs.suid_dumpable = 0" >> /etc/sysctl.conf

Configure ExecShield: `echo "kernel.exec-shield = 1" >> /etc/sysctl.conf`

Enable Randomized Virtual Memory Region Placement: `echo "kernel.randomize_va_space = 2" >> /etc/sysctl.conf`

OS Services
-----------

Remove Legacy Services:

    yum remove telnet-server telnet
    yum remove rsh-server rsh
    yum remove ypserv ypbind
    yum remove tftp-server tftp
    yum remove talk-server talk
    yum remove xinetd
    chkconfig chargen-dgram off
    chkconfig chargen-stream off
    chkconfig daytime-dgram off
    chkconfig daytime-stream off
    chkconfig echo-dgram off
    chkconfig echo-stream off
    chkconfig tcpmux-server off

Special Purpose Services
------------------------

Set Daemon umask: `echo "umask 027" >> /etc/sysconfig/init`

Remove X Windows: `echo "id:3:initdefault" /etc/inittab`

Disable Avahi Server: `chkconfig avahi-daemon off`

Disable Zeroconf: `echo "NOZEROCONF=true" >> /etc/sysconfig/network`

Disable Print Server - CUPS: `chkconfig cups off`

Remove DHCP Server: `yum remove dhcp`

Configure Network Time Protocol (NTP):

    cat << EOF >> /etc/ntp.conf
    restrict default kod nomodify notrap nopeer noquery
    restrict -6 default kod nomodify notrap nopeer noquery
    server <ntp-server>
    EOF

Remove LDAP: `yum remove openldap-servers openldap-clients`

Disable NFS and RPC:

    chkconfig nfslock off
    chkconfig rpcgssd off
    chkconfig rpcbind off
    chkconfig rpcidmapd off
    chkconfig rpcsvcgssd off

Remove DNS Server: `yum remove bind`

Remove FTP Server: `yum remove vsftpd`

Remove SNMP Server: `yum remove net-snmp`

Configure Mail Transfer Agent for Local-Only Mode: `echo "inet_interfaces = localhost" >> /etc/postfix/main.cf`

Network Configuration and Firewalls
-----------------------------------

Disable IP Forwarding: `echo "net.ipv4.ip_forward=0" >> /etc/sysctl.conf`

Disable Send Packet Redirects:

    cat <<EOF >> /etc/sysctl.conf
    net.ipv4.conf.all.send_redirects=0
    net.ipv4.conf.default.send_redirects=0
    EOF

Disable Source Routed Packet Acceptance:

    cat <<EOF >> /etc/sysctl.conf
    net.ipv4.conf.all.accept_source_route=0
    net.ipv4.conf.default.accept_source_route=0
    EOF

Disable ICMP Redirect Acceptance:

    cat <<EOF >> /etc/sysctl.conf
    net.ipv4.conf.all.accept_redirects=0
    net.ipv4.conf.default.accept_redirects=0
    EOF

Disable Secure ICMP Redirect Acceptance:

    cat <<EOF >> /etc/sysctl.conf
    net.ipv4.conf.all.secure_redirects=0
    net.ipv4.conf.default.secure_redirects=0
    EOF

Log Suspicious Packets:

    cat <<EOF >> /etc/sysctl.conf
    net.ipv4.conf.all.log_martians=1
    net.ipv4.conf.default.log_martians=1
    net.ipv4.route.flush=1
    EOF

Enable Ignore Broadcast Requests: `echo "net.ipv4.icmp_echo_ignore_broadcasts=1" >> /etc/sysctl.conf`

Enable Bad Error Message Protection: `echo "net.ipv4.icmp_ignore_bogus_error_responses=1" >> /etc.sysctl.conf`

Enable RFC-recommended Source Route Validation:

    cat << EOF >> /etc/sysctl.conf
    net.ipv4.conf.all.rp_filter=1
    net.ipv4.conf.default.rp_filter=1
    EOF

Enable TCP SYN Cookies: `echo "net.ipv4.tcp_syncookies=1" >> /etc/sysctl.conf`

Disable IPv6
------------

Disable IPv6 Router Advertisements:

    cat << EOF >> /etc/sysctl.conf
    net.ipv6.conf.all.accept_ra=0
    net.ipv6.conf.default.accept_ra=0
    EOF

Disable IPv6 Redirect Acceptance:

    cat << EOF >> /etc/sysctl.conf
    net.ipv6.conf.all.accept_redirects=0
    net.ipv6.conf.default.accept_redirects=0
    EOF

Disable IPv6:

    cat << EOF >> /etc/sysconfig/network
    NETWORKING_IPV6=no
    IPV6INIT=no
    EOF

    echo "options ipv6 disable=1" >> /etc/modprobe.d/ipv6.conf

    chkconfig ip6tables off

Install TCP Wrappers
--------------------

Install TCP Wrappers: `yum install tcp_wrappers`

Create /etc/hosts.allow: `echo "ALL: <net>/<mask>, <net>/<mask>, ..." >/etc/hosts.allow`

Verify Permissions on /etc/hosts.allow: `chmod 644 /etc/hosts.allow`

Create /etc/hosts.deny: `echo "ALL: ALL" >> /etc/hosts.deny`

Verify Permissions on /etc/hosts.deny: `chmod 644 /etc/hosts.deny`

Uncommon Network Protocols
--------------------------

Disable DCCP: `echo "install dccp /bin/true" >> /etc/modprobe.d/CIS.conf`

Disable SCTP: `echo "install sctp /bin/true" >> /etc/modprobe.d/CIS.conf`

Disable RDS: `echo "install rds /bin/true" >> /etc/modprobe.d/CIS.conf`

Disable TIPC: `echo "install tipc /bin/true" >> /etc/modprobe.d/CIS.conf`

Logging and Auditing
--------------------

Install the rsyslog package: `yum install rsyslog`

Activate the rsyslog Service: `chkconfig syslog off && chkconfig rsyslog on`

Configure /etc/rsyslog.conf:

    auth,user.* /var/log/messages
    kern.* /var/log/kern.log
    daemon.* /var/log/daemon.log
    syslog.* /var/log/syslog
    lpr,news,uucp,local0,local1,local2,local3,local4,local5,local6.* /var/log/unused.log

Create and Set Permissions on rsyslog Log Files

    for log in /var/log/messages /var/log/kern.log /var/log/daemon.log /var/log/syslog /var/log/unused.log
    do
      touch ${log}
      chown root:root
      chmod 600 ${log}
    done

Configure rsyslog to Send Logs to a Remote Log Host: `echo "*.* @@loghost.example.com" >> /etc/rsyslog.conf`

Accept Remote rsyslog Messages Only on Designated Log Hosts:

    cat << EOF >> /etc/rsyslog.conf
    $ModLoad imtcp.so
    $InputTCPServerRun 514
    EOF

Configure System Accounting
---------------------------

Disable System on Audit Log Full:

    cat << EOF >> /etc/audit/auditd.conf
    space_left_action = email
    action_mail_acct = root
    admin_space_left_action = halt
    EOF

Keep All Auditing Information: `echo "max_log_file_action = keep_logs" >> /etc/audit/auditd.conf`

Enable auditd Service: `chkconfig auditd on`

Enable Auditing for Processes That Start Prior to auditd:

    ed /etc/grub.conf << END
    g/audit=1/s///g
    g/kernel/s/$/ audit=1/
    w
    q END

Record Events That Modify Date and Time Information

    cat << EOF >> /etc/audit/audit.rules
    -a always,exit -F arch=b64 -S adjtimex -S settimeofday -k time-change
    -a always,exit -F arch=b32 -S adjtimex -S settimeofday -S stime -k time-change
    -a always,exit -F arch=b64 -S clock_settime -k time-change
    -a always,exit -F arch=b32 -S clock_settime -k time-change
    -w /etc/localtime -p wa -k time-change
    EOF

Record Events That Modify User/Group Information:

    cat << EOF >> /etc/audit/audit.rules
    -w /etc/group -p wa -k identity
    -w /etc/passwd -p wa -k identity
    -w /etc/gshadow -p wa -k identity
    -w /etc/shadow -p wa -k identity
    -w /etc/security/opasswd -p wa -k identity
    EOF

Record Events That Modify the System's Network Environment:

    cat << EOF >> /etc/audit/audit.rules
    -a always,exit -F arch=b64 -S sethostname -S setdomainname -k system-locale
    -a always,exit -F arch=b32 -S sethostname -S setdomainname -k system-locale
    -w /etc/issue -p wa -k system-locale
    -w /etc/issue.net -p wa -k system-locale
    -w /etc/hosts -p wa -k system-locale
    -w /etc/sysconfig/network -p wa -k system-locale
    EOF

Record Events That Modify the System's Mandatory Access Controls: `echo "-w /etc/selinux/ -p wa -k MAC-policy" >> /etc/audit/audit.rules`

Collect Login and Logout Events:

    cat << EOF >> /etc/audit/audit.rules
    -w /var/log/faillog -p wa -k logins
    -w /var/log/lastlog -p wa -k logins
    -w /var/log/tallylog -p wa -k logins
    EOF

Collect Session Initiation Information:

    cat << EOF >> /etc/audit/audit.rules
    -w /var/run/utmp -p wa -k session
    -w /var/log/wtmp -p wa -k session
    -w /var/log/btmp -p wa -k session
    EOF

Collect Discretionary Access Control Permission Modification Events:

    cat << EOF >> /etc/audit/audit.rules
    -a always,exit -F arch=b64 -S chmod -S fchmod -S fchmodat -F auid>=500 -F auid!=4294967295 -k perm_mod
    -a always,exit -F arch=b32 -S chmod -S fchmod -S fchmodat -F auid>=500 -F auid!=4294967295 -k perm_mod
    -a always,exit -F arch=b64 -S chown -S fchown -S fchownat -S lchown -F auid>=500 -F auid!=4294967295 -k perm_mod
    -a always,exit -F arch=b32 -S chown -S fchown -S fchownat -S lchown -F auid>=500 -F auid!=4294967295 -k perm_mod
    -a always,exit -F arch=b64 -S setxattr -S lsetxattr -S fsetxattr -S removexattr -S lremovexattr -S fremovexattr -F auid>=500 -F auid!=4294967295 -k perm_mod
    -a always,exit -F arch=b32 -S setxattr -S lsetxattr -S fsetxattr -S removexattr -S lremovexattr -S fremovexattr -F auid>=500 -F auid!=4294967295 -k perm_mod
    EOF

Collect Unsuccessful Unauthorized Access Attempts to Files:

    cat << EOF >> /etc/audit/audit.rules
    -a always,exit -F arch=b64 -S creat -S open -S openat -S truncate -S ftruncate -F exit=-EACCES -F auid>=500 -F auid!=4294967295 -k access
    -a always,exit -F arch=b32 -S creat -S open -S openat -S truncate -S ftruncate -F exit=-EACCES -F auid>=500 -F auid!=4294967295 -k access
    -a always,exit -F arch=b64 -S creat -S open -S openat -S truncate -S ftruncate -F exit=-EPERM -F auid>=500 -F auid!=4294967295 -k access
    -a always,exit -F arch=b32 -S creat -S open -S openat -S truncate -S ftruncate -F exit=-EPERM -F auid>=500 -F auid!=4294967295 -k access
    EOF

Collect Use of Privileged Commands:

    find PART -xdev \( -perm -4000 -o -perm -2000 \) -type f | awk '{print "-a always,exit -F path=" $1 " -F perm=x -F auid>=500 -F auid!=4294967295 -k privileged" } >> /etc/audit/audit.rules

Collect Successful File System Mounts:

    cat << EOF >> /etc/audit/audit.rules
    -a always,exit -F arch=b64 -S mount -F auid>=500 -F auid!=4294967295 -k mounts
    -a always,exit -F arch=b32 -S mount -F auid>=500 -F auid!=4294967295 -k mounts
    EOF

Collect File Deletion Events by User:

    cat << EOF >> /etc/audit/audit.rules
    -a always,exit -F arch=b64 -S unlink -S unlinkat -S rename -S renameat -F auid>=500 -F auid!=4294967295 -k delete
    -a always,exit -F arch=b32 -S unlink -S unlinkat -S rename -S renameat -F auid>=500 -F auid!=4294967295 -k delete
    EOF

Collect Changes to System Administration Scope (sudoers): `echo "-w /etc/sudoers -p wa -k scope" >> /etc/audit/audit.rules`

Collect System Administrator Actions (sudolog): `echo "-w /var/log/sudo.log -p wa -k actions" >> /etc/audit/audit.rules`

Collect Kernel Module Loading and Unloading:

    cat << EOF >> /etc/audit/audit.rules
    -w /sbin/insmod -p x -k modules -w /sbin/rmmod -p x -k modules
    -a always,exit -F arch=b64 -S init_module -S delete_module -k modules
    EOF

Make the Audit Configuration Immutable: `echo "-e 2" >> /etc/audit/audit.rules`

System Access, Authentication and Authorization
-----------------------------------------------

Enable anacron Daemon: `yum install cronie-anacron`

Enable crond Daemon: `chkconfig crond on`

Set User/Group Owner and Permission on /etc/anacrontab: `chown root:root /etc/anacrontab && chmod og-rwx /etc/anacrontab`

Set User/Group Owner and Permission on /etc/crontab: `chown root:root /etc/crontab && chmod og-rwx /etc/crontab`

Set User/Group Owner and Permission on /etc/cron.hourly: `chown root:root /etc/cron.hourly && chmod og-rwx /etc/cron.hourly`

Set User/Group Owner and Permission on /etc/cron.daily: `chown root:root /etc/cron.daily && chmod og-rwx /etc/cron.daily`

Set User/Group Owner and Permission on /etc/cron.weekly: `chown root:root /etc/cron.weekly && chmod og-rwx /etc/cron.weekly`

Set User/Group Owner and Permission on /etc/cron.monthly: `chown root:root /etc/cron.monthly && chmod og-rwx /etc/cron.monthly`

Set User/Group Owner and Permission on /etc/cron.d: `chown root:root /etc/cron.d && chmod og-rwx /etc/cron.d`

Restrict at Daemon:

    rm /etc/at.deny
    touch /etc/at.allow
    chown root:root /etc/at.allow
    chmod og-rwx /etc/at.allow

Restrict at/cron to Authorized Users:

    rm /etc/at.deny
    touch /etc/at.allow
    chown root:root /etc/at.allow
    chmod og-rwx /etc/at.allow

Configure SSH
-------------

Set SSH Protocol to 2: `echo "Protocol 2" >> /etc/ssh/sshd_config`

Set LogLevel to INFO: `echo "LogLevel INFO" >> /etc/ssh/sshd_config`

Set Permissions on /etc/ssh/sshd_config: `chown root:root /etc/ssh/sshd_config && chmod 600 /etc/ssh/sshd_config`

Disable SSH X11 Forwarding: `echo "X11Forwarding no" >> /etc/ssh/sshd_config`

Set SSH MaxAuthTries to 4 or Less: `echo "MaxAuthTries 4" >> /etc/ssh/sshd_config`

Set SSH IgnoreRhosts to Yes: `echo "IgnoreRhosts yes" >> /etc/ssh/sshd_config`

Set SSH HostbasedAuthentication to No: `echo "HostbasedAuthentication no" >> /etc/ssh/sshd_config`

Disable SSH Root Login: `echo "PermitRootLogin no" >> /etc/ssh/sshd_config`

Set SSH PermitEmptyPasswords to No: `echo "PermitEmptyPasswords no" >> /etc/ssh/sshd_config`

Do Not Allow Users to Set Environment Options: `echo "PermitUserEnvironment no" >> /etc/ssh/sshd_config`

Use Only Approved Cipher in Counter Mode: `echo "Ciphers aes128-ctr,aes192-ctr,aes256-ctr" >> /etc/ssh/sshd_config`

Set Idle Timeout Interval for User Login:

    cat << EOF >> /etc/ssh/sshd_config
    ClientAliveInterval 300
    ClientAliveCountMax 0
    EOF

Limit Access via SSH:

    cat << EOF >> /etc/ssh/sshd_config
    AllowUsers <userlist>
    AllowGroups <grouplist>
    DenyUsers <userlist>
    DenyGroups <grouplist>
    EOF

Set SSH Banner: `echo "Banner /etc/issue.net" >> /etc/ssh/sshd_config`

Configure PAM
-------------

Upgrade Password Hashing Algorithm to SHA-512: `authconfig --passalgo=sha512 --update`

Set Password Creation Requirement Parameters Using pam_cracklib: `echo "password required pam_cracklib.so try_first_pass retry=3 minlen=14 dcredit=-1 ucredit=-1 ocredit=-1 lcredit=-1" >> /etc/pam.d/system-auth`

Set Lockout for Failed Password Attempts:

    cat << EOF >> /etc/pam.d/password-auth
    #%PAM-1.0
    # This file is auto-generated.
    # User changes will be destroyed the next time authconfig is run.
    auth required pam_env.so
    auth required pam_faillock.so preauth audit silent deny=5 unlock_time=900 auth [success=1 default=bad] pam_unix.so
    auth [default=die] pam_faillock.so authfail audit deny=5 unlock_time=900 auth sufficient pam_faillock.so authsucc audit deny=5 unlock_time=900 auth required pam_deny.so
    EOF

    cat << EOF >> /etc/pam.d/system-auth
    #%PAM-1.0
    # This file is auto-generated.
    # User changes will be destroyed the next time authconfig is run.
    auth required pam_env.so
    auth required pam_faillock.so preauth audit silent deny=5 unlock_time=900 auth [success=1 default=bad] pam_unix.so
    auth [default=die] pam_faillock.so authfail audit deny=5 unlock_time=900 auth sufficient pam_faillock.so authsucc audit deny=5 unlock_time=900 auth required pam_deny.so
    EOF

Limit Password Reuse: `echo "password sufficient pam_unix.so remember=5" >> /etc/pam.d/system_auth`

Restrict Access to the su Command: `echo "auth required pam_wheel.so use_uid" >> /etc/pam.d/su`

User Accounts and Environment
-----------------------------

Set Password Expiration Days: `echo "PASS_MAX_DAYS 90" >> /etc/login.defs`

Set Password Change Minimum Number of Days: `echo "PASS_MIN_DAYS 7" >> /etc/login.defs`

Set Password Expiring Warning Days: `echo "PASS_WARN_AGE 7" >> /etc/login.defs`

Disable System Accounts:

    #!/bin/bash
    for user in `awk -F: '($3 < 500) {print $1 }' /etc/passwd`; do
      if [ $user != "root" ]
      then
        /usr/sbin/usermod -L $user
        if [ $user != "sync" ] && [ $user != "shutdown" ] && [ $user != "halt" ]
        then
          /usr/sbin/usermod -s /sbin/nologin $user
        fi
      fi
    done

Set Default umask for Users: `for i in /etc/bashrc /etc/profile; do echo "umask 77" >> $i; done`

Lock Inactive User Accounts: `useradd -D -f 35`

Warning Banners
---------------

Set Warning Banner for Standard Login Services:

    touch /etc/motd
    echo "Authorized uses only. All activity may be monitored and reported." > /etc/issue
    echo "Authorized uses only. All activity may be monitored and reported." > /etc/issue.net
    chown root:root /etc/motd
    chmod 644 /etc/motd
    chown root:root /etc/issue
    chmod 644 /etc/issue
    chown root:root /etc/issue.net
    chmod 644 /etc/issue.net

System Maintenance
------------------

Verify System File Permissions: `rpm -Va --nomtime --nosize --nomd5 --nolinkto > <filename>`

Verify Permissions on /etc/passwd: `chmod 644 /etc/passwd`

Verify Permissions on /etc/shadow: `chmod 000 /etc/shadow`

Verify Permissions on /etc/gshadow: `chmod 000 /etc/gshadow`

Verify Permissions on /etc/group: `chmod 644 /etc/group`

Verify User/Group Ownership on /etc/passwd: `chown root:root /etc/passwd`

Verify User/Group Ownership on /etc/shadow: `chown root:root /etc/shadow`

Verify User/Group Ownership on /etc/gshadow: `chown root:root /etc/gshadow`

Verify User/Group Ownership on /etc/group: `chown root:root /etc/group`

Find World Writable Files:

    #!/bin/bash
    df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type f -perm -0002

Find Un-owned Files and Directories:

    #!/bin/bash
    df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -nouser -ls

Find Un-grouped Files and Directories:

    #!/bin/bash
    df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -group -ls

Find SUID System Executables:

    #!/bin/bash
    df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type f -perm -4000 -print

Find SGID System Executables:

    #!/bin/bash
    df --local -P | awk {'if (NR!=1) print $6'} | xargs -I '{}' find '{}' -xdev -type f -perm -2000 -print

Ensure Password Fields are Not Empty:

    #!/bin/bash
    cat /etc/shadow | awk -F: '($2 == "" ) { print $1 " does not have a password "}'
    # then passwd -l <username>

Verify No Legacy "+" Entries Exist in /etc/passwd File:

    grep '^+:' /etc/passwd
    # then delete <line>

Verify No Legacy "+" Entries Exist in /etc/shadow File:

    grep '^+:' /etc/shadow
    # then delete <line>

Verify No Legacy "+" Entries Exist in /etc/group File:

    grep '^+:' /etc/group
    # then delete <line>

Verify No UID 0 Accounts Exist Other Than root:

    #!/bin/bash
    cat /etc/passwd | /bin/awk -F: '($3 == 0) { print $1 }'

Ensure root PATH Integrity:

    #!/bin/bash
    if [ "`echo $PATH | /bin/grep :: `" != "" ]; then
      echo "Empty Directory in PATH (::)"
    fi
    if [ "`echo $PATH | bin/grep :$`"  != "" ]; then
      echo "Trailing : in PATH"
    fi
    p=`echo $PATH | /bin/sed -e 's/::/:/' -e 's/:$//' -e 's/:/ /g'`
    set -- $p
    while [ "$1" != "" ]; do
      if [ "$1" = "." ]; then
        echo "PATH contains ."
        shift
        continue
      fi
      if [ -d $1 ]; then
        dirperm=`/bin/ls -ldH $1 | /bin/cut -f1 -d" "`
        if [ `echo $dirperm | /bin/cut -c6 ` != "-" ]; then
          echo "Group Write permission set on directory $1"
        fi
        if [ `echo $dirperm | /bin/cut -c9 ` != "-" ]; then
          echo "Other Write permission set on directory $1"
        fi
        dirown=`ls -ldH $1 | awk '{print $3}'`
        if [ "$dirown" != "root" ] ; then
          echo $1 is not owned by root
        fi
      else
        echo $1 is not a directory
      fi
      shift
    done

Check Permissions on User Home Directories:

    #!/bin/bash
    for dir in `cat /etc/passwd  | egrep -v '(root|halt|sync|shutdown)' | awk -F: '($8 == "PS" && $7 != "/sbin/nologin") { print $6 }'`; do
      dirperm=`/bin/ls -ld $dir | /bin/cut -f1 -d" "`
      if [ `echo $dirperm | /bin/cut -c6 ` != "-" ]; then
        echo "Group Write permission set on directory $dir"
      fi
      if [ `echo $dirperm | /bin/cut -c8 ` != "-" ]; then
        echo "Other Read permission set on directory $dir"
      fi
      if [ `echo $dirperm | /bin/cut -c9 ` != "-" ]; then
        echo "Other Write permission set on directory $dir"
      fi
      if [ `echo $dirperm | /bin/cut -c10 ` != "-" ]; then
        echo "Other Execute permission set on directory $dir"
      fi
    done

Check User Dot File:

    #!/bin/bash
    for dir in `cat /etc/passwd | egrep -v '(root|sync|halt|shutdown)' | awk -F: '($7 != "/sbin/nologin") { print $6 }'`; do
      for file in $dir/.[A-Za-z0-9]*; do
        if [ ! -h "$file" -a -f "$file" ]; then
          fileperm=`/bin/ls -ld $file | /bin/cut -f1 -d" "`
        if [ `echo $fileperm | /bin/cut -c6 ` != "-" ]; then
          echo "Group Write permission set on file $file"
        fi
        if [ `echo $fileperm | /bin/cut -c9 ` != "-" ]; then
          echo "Other Write permission set on file $file"
        fi
      done
    done

Check Permissions on User .netrc Files:

    #!/bin/bash
    for dir in `cat /etc/passwd | egrep -v '(root|sync|halt|shutdown)' | awk -F: '($7 != "/sbin/nologin") { print $6 }'`; do
      for file in $dir/.netrc; do
        if [ ! -h "$file" -a -f "$file" ]; then
          fileperm=`/bin/ls -ld $file | /bin/cut -f1 -d" "`
          if [ `echo $fileperm | /bin/cut -c5 ` != "-" ]
          then
            echo "Group Read set on $file"
          fi
          if [ `echo $fileperm | /bin/cut -c6 ` != "-" ]
          then
            echo "Group Write set on $file"
          fi
          if [ `echo $fileperm | /bin/cut -c7 ` != "-" ]
          then
            echo "Group Execute set on $file"
          fi
          if [ `echo $fileperm | /bin/cut -c8 ` != "-" ]
          then
            echo "Other Read  set on $file"
          fi
          if [ `echo $fileperm | /bin/cut -c9 ` != "-" ]
          then
            echo "Other Write set on $file"
          fi
          if [ `echo $fileperm | /bin/cut -c10 ` != "-" ]
          then
            echo "Other Execute set on $file"
          fi
        fi
      done
    done

Check for Presence of User .rhosts Files:

    #!/bin/bash
    for dir in `cat /etc/passwd | egrep -v '(root|halt|sync|shutdown)' | awk -F: '($7 != "/sbin/nologin") { print $6 }'`; do
      for file in $dir/.rhosts; do
        if [ ! -h "$file" -a -f "$file" ]; then
          echo ".rhosts file in $dir"
        fi
      done
    done

Check Groups in /etc/passwd:

    #!/bin/bash
    for i in $(cut -s -d: -f4 /etc/passwd | sort -u ); do
      grep -q -P "^.*?:x:$i:" /etc/group
      if [ $? -ne 0 ]; then
        echo "Group $i is referenced by /etc/passwd but does not exist in /etc/group"
      fi
    done

Check That Users Are Assigned Valid Home Directories:

    #!/bin/bash
    cat /etc/passwd | awk -F: '{ print $1 " " $3 " " $6 }' | while read user uid dir; do
      if [ $uid -ge 500 -a ! -d "$dir" -a $user != "nfsnobody" ]; then
        echo "The home directory ($dir) of user $user does not exist."
      fi
    done

Check User Home Directory Ownership:

    #!/bin/bash
    cat /etc/passwd | awk -F: '{ print $1 " " $3 " " $6 }' | while read user uid dir; do
      if [ $uid -ge 500 -a -d "$dir" -a $user != "nfsnobody" ]; then
        owner=$(stat -L -c "%U" "$dir")
        if [ "$owner" != "$user" ]; then
          echo "The home directory ($dir) of user $user is owned by $owner."
        fi
      fi
    done

Check for Duplicate UIDs:

    #!/bin/bash
    echo "The Output for the Audit of Control 9.2.15 - Check for Duplicate UIDs is"
    cat /etc/passwd | cut -f3 -d":" | sort -n | uniq -c |\
      while read x ; do
        [ -z "${x}" ] && break
        set - $x
        if [ $1 -gt 1 ]; then
          users=`gawk -F: '($3 == n) { print $1 }' n=$2 /etc/passwd | xargs`
          echo "Duplicate UID ($2): ${users}"
        fi
      done

Check for Duplicate GIDs:

    #!/bin/bash
    echo "The Output for the Audit of Control 9.2.16 - Check for Duplicate GIDs is"
    cat /etc/group | cut -f3 -d":" | sort -n | uniq -c |\
      while read x ; do
        [ -z "${x}" ] && break
        set - $x
        if [ $1 -gt 1 ]; then
          grps=`gawk -F: '($3 == n) { print $1 }' n=$2 /etc/group | xargs`
          echo "Duplicate GID ($2): ${grps}"
        fi
      done

Check That Reserved UIDs Are Assigned to System Accounts:

    #!/bin/bash
    echo "The Output for the Audit of Control 9.2.17 - Check That Reserved UIDS Are Assigned to System Accounts is"
    defUsers="root bin daemon adm lp sync shutdown halt mail news uucp operator games gopher ftp nobody nscd vcsa rpc mailnull smmsp pcap ntp dbus avahi sshd rpcuser nfsnobody haldaemon avahi-autoipd distcache apache oprofile webalizer dovecot squid named xfs gdm sabayon usbmuxd rtkit abrt saslauth pulse postfix tcpdump"

    cat /etc/passwd | awk -F: '($3 < 500) { print $1" "$3 }' |\
      while read user uid; do
        found=0
        for tUser in ${defUsers}
        do
          if [ ${user} = ${tUser} ]; then
            found=1
          fi
        done
        if [ $found -eq 0 ]; then
          echo "User $user has a reserved UID ($uid)."
        fi
      done

Check for Duplicate User Names:

    #!/bin/bash
    echo "The Output for the Audit of Control 9.2.18 - Check for Duplicate User Names is"
    cat /etc/passwd | cut -f1 -d":" | sort -n | uniq -c |\
      while read x ; do
        [ -z "${x}" ] && break
        set - $x
        if [ $1 -gt 1 ]; then
          uids=`/bin/gawk -F: '($1 == n) { print $3 }' n=$2 /etc/passwd | xargs`
          echo "Duplicate User Name ($2): ${uids}"
        fi
      done

Check for Duplicate Group Names:

    #!/bin/bash
    echo "The Output for the Audit of Control 9.2.19 - Check for Duplicate Group Names is"
    cat /etc/group | cut -f1 -d":" | sort -n | uniq -c |\
      while read x ; do
        [ -z "${x}" ] && break
        set - $x
        if [ $1 -gt 1 ]; then
          gids=`/bin/gawk -F: '($1 == n) { print $3 }' n=$2 /etc/group | xargs`
          echo "Duplicate Group Name ($2): ${gids}"
        fi
      done

Check for Presence of User .netrc Files:

    #!/bin/bash
    for dir in `cat /etc/passwd | awk -F: '{ print $6 }'`; do
      if [ ! -h "$dir/.netrc" -a -f "$dir/.netrc" ]; then
        echo ".netrc file $dir/.netrc exists"
      fi
    done

Check for Presence of User .forward Files:

    #!/bin/bash
    for dir in `cat /etc/passwd | /bin/awk -F: '{ print $6 }'`; do
      if [ ! -h "$dir/.forward" -a -f "$dir/.forward" ]; then
        echo ".forward file $dir/.forward exists"
      fi
    done
