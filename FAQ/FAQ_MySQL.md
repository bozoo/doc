FAQ MYSQL
=========

Secure MySQL after install :

    /usr/bin/mysql_secure_installation

Set root password :

    mysqladmin -u root password YOURNEWPASSWORD

Ping mysql :

    mysqladmin -u root -p ping

Check version :

    mysqladmin -u root -p version

    Server version          5.5.28
    Protocol version        10
    Connection              Localhost via UNIX socket
    UNIX socket             /var/lib/mysql/mysql.sock
    Uptime:                 7 days 14 min 45 sec
    Threads: 2  Questions: 36002  Slow queries: 0  Opens: 15
    Flush tables: 1  Open tables: 8  Queries per second avg: 0.059

Check status :

    mysqladmin -u root -p status

    Uptime: 606704  Threads: 2  Questions: 36003  Slow queries: 0  Opens: 15  Flush tables: 1  Open tables: 8  Queries per second avg: 0.059

or

    mysqladmin -u root -p extended-status
    +------------------------------------------+-------------+
    | Variable_name                            | Value       |
    +------------------------------------------+-------------+
    | Aborted_clients                          | 3           |
    | Aborted_connects                         | 3           |
    | Binlog_cache_disk_use                    | 0           |
    | Binlog_cache_use                         | 0           |
    | Binlog_stmt_cache_disk_use               | 0           |
    | Binlog_stmt_cache_use                    | 0           |
    | Bytes_received                           | 6400357     |
    | Bytes_sent                               | 2610105     |
    | Com_admin_commands                       | 3           |
    | Com_assign_to_keycache                   | 0           |
    | Com_alter_db                             | 0           |
    | Com_alter_db_upgrade                     | 0           |
    | Com_alter_event                          | 0           |
    | Com_alter_function                       | 0           |
    | Com_alter_procedure                      | 0           |
    | Com_alter_server                         | 0           |
    | Com_alter_table                          | 0           |
    | Com_alter_tablespace                     | 0           |
    +------------------------------------------+-------------+

Show MySQL variables :

    mysqladmin  -u root -p variables

Show running process :

    mysqladmin -u root -p processlist

Kill running process :

    mysqladmin -u root -p kill $idProcess

Create / Drop database :

    mysqladmin -u root -p create $databaseName
    mysqladmin -u root -p drop $databaseName

Shutdown MySQL :

    mysqladmin -u root -p shutdown

Stop / Start replication :

    mysqladmin  -u root -p start-slave
    mysqladmin  -u root -p stop-slave

Set MySQL logs to debug :

    mysqladmin  -u root -p debug

Show databases :

    > show databases;

Change database :

    > use myDatabase;

Show tables :

    > show tables;

Show columns of a table :

    > desc myTable;

Show date :

    > select now() from dual;

Show plan of a query :

    > explain myQuery; # myQuery = "select * from whatyouwhant or any other query"

Show index for at table :

    > show index from myTable;

Save result of a query into a file :

    > select * into outfile '/tmp/myfile.txt' from myTable;

or

    > select * into outfile '/tmp/myfile.txt' fields terminated by ',' from myTable;

Change password for non-root user :

    > use mysql
    > update user set password=PASSWORD("MyPassword")  where User='MyUser';
    > commit;
    > flush privileges;
    > exit

Change root password :

    # Stop MySQL and start it in safe mode
    /usr/bin/safe_mysqld --skip-grant-tables&
    /usr/bin/mysql
    > use mysql;
    > update user set password = password('.......') where user = 'root' and
host='localhost';
    > flush privileges;
    # Restart MySQL

Show MySQL engine for a database :

    > select table_name,engine from information_schema.tables where table_schema='DB_NAME';

Show DB Size :

    > SELECT table_schema "Data Base Name",
    sum( data_length + index_length ) / 1024 /
    1024 "Data Base Size in MB",
    sum( data_free )/ 1024 / 1024 "Free Space in MB"
    FROM information_schema.TABLES
    GROUP BY table_schema ;

Show tables size :

    > SELECT CONCAT(table_schema, '.', table_name),
    CONCAT(ROUND(table_rows / 1000000, 2), 'M')                                    rows,
    CONCAT(ROUND(data_length / ( 1024 * 1024 * 1024 ), 2), 'G')                    DATA,
    CONCAT(ROUND(index_length / ( 1024 * 1024 * 1024 ), 2), 'G')                   idx,
    CONCAT(ROUND(( data_length + index_length ) / ( 1024 * 1024 * 1024 ), 2), 'G') total_size,
    ROUND(index_length / data_length, 2)                                           idxfrac
    FROM   information_schema.TABLES
    ORDER  BY data_length + index_length DESC
    LIMIT  10;

Repair MySQL
------------

Check MyISAM tables :

    > CHECK TABLE;

Repair MyISAM tables :

    > REPAIR TABLE;

Optimize MyISAM table (cold operation, need to have twice space disk of the table on fs) :

    > OPTIMIZE TABLE;

Analyse MyISAM table :

    > ANALYZE TABLE;

Backup / Restore
----------------

Backup MySQL DB :

    mysqldump -u [username] –p[password] [opts] [database_name] [tables_names] > [dump_file.sql]

    opts :
    --databases [db_name1] [db_name2] [...] : list dbs to backup
    --all-databases : backup all dbs
    --no-data : backup only db structure without data
    --no-create-info : backup data only

Restore MySQL DB on an empty DB :

    mysql -u [username] –p[password] [database_name] < [dump_file.sql]

Restore MySQL DB on a existing DB :

    mysqlimport -u [username] –p[password] [database_name] < [dump_file.sql]

Replication
-----------

### On Master

Edit my.cnf

    server-id = 1
    binlog-do-db=my_db
    relay-log = /var/lib/mysql/mysql-relay-bin
    relay-log-index = /var/lib/mysql/mysql-relay-bin.index
    log-error = /var/lib/mysql/mysql.err
    master-info-file = /var/lib/mysql/mysql-master.info
    relay-log-info-file = /var/lib/mysql/mysql-relay-log.info
    log-bin = /var/lib/mysql/mysql-bin

Set privileges for replication

    \> GRANT REPLICATION SLAVE ON *.* TO 'slave_user'@'%' IDENTIFIED BY 'your_password';
    \> FLUSH PRIVILEGES;
    \> FLUSH TABLES WITH READ LOCK;
    \> SHOW MASTER STATUS;
    +------------------+----------+--------------+------------------+
    | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB |
    +------------------+----------+--------------+------------------+
    | mysql-bin.000003 | 11128001 | my_db        |                  |
    +------------------+----------+--------------+------------------+

### On Slave

Edit my.cnf

    server-id = 2
    master-host=192.168.1.1
    master-connect-retry=60
    master-user=slave_user
    master-password=yourpassword
    replicate-do-db=my_db
    relay-log = /var/lib/mysql/mysql-relay-bin
    relay-log-index = /var/lib/mysql/mysql-relay-bin.index
    log-error = /var/lib/mysql/mysql.err
    master-info-file = /var/lib/mysql/mysql-master.info
    relay-log-info-file = /var/lib/mysql/mysql-relay-log.info
    log-bin = /var/lib/mysql/mysql-bin

Import dump of the master db

Set slave replication

    \> slave stop;
    \> CHANGE MASTER TO MASTER_HOST='192.168.1.1', MASTER_USER='slave_user', MASTER_PASSWORD='yourpassword', MASTER_LOG_FILE='mysql-bin.000003', MASTER_LOG_POS=11128001;
    \> slave start;
    \> show slave status\G
    *************************** 1. row ***************************
        Slave_IO_State: Waiting for master to send event
          Master_Host: 192.168.1.1
          Master_User: slave_user
          Master_Port: 3306
          Connect_Retry: 60
          Master_Log_File: mysql-bin.000003
          Read_Master_Log_Pos: 12345100
          Relay_Log_File: mysql-relay-bin.000002
          Relay_Log_Pos: 11381900
          Relay_Master_Log_File: mysql-bin.000003
          Slave_IO_Running: Yes
          Slave_SQL_Running: Yes
          Replicate_Do_DB: my_db
