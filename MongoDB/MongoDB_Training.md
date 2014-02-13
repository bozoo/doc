MONGODB TRAINING
================

 - Table => Collection
 - Row => JSON Document
 - Index => Index
 - Join => Embedding & Linking
 - Partition => Shard
 - Partition Key => Shard Key

With MongoDB, every values of a row are returned each time

Data format: BSON (Binary JSON)

The full database stays in Virtual Memory (limitation in 32-bits OS: 2Go max for database)

DBpath:
 - Namespace file: `${database}.ns`
 - Datafiles: `${database}.0` (64Mo), `${database}.1` (128Mo), `${database}.2` (256Mo), ...

Datafiles size x2 for each new files

Documents of same collections are always in same datafile

`.dbshell`: history of mongodb commands

Commands
--------

### MongoDB Start/Stop

Start mongod: `mongod --dbpath /db --port 3000`

Connect to mongod: `mongo --port 3000`

### MongoDB Administration

		> show dbs # show databases
		> db.stats() # data size + index size = storage size
		> db.stats(1024*1024) # data size in Mo
		> db.serverStatus()
		> use $mydb # create db
		> show collections # show collections on the current database
		> db.$mydb.stats # select $mydb database instead of current db

Commands find & modify
----------------------

### Find

		> db.scores.find() # show every documents of scores collection in current database
		> db.scores.find().pretty() # return every documents with "one value per line format"
		> db.scores.findOne() # return only the first document of the collection
		> db.scores.find({    })
		> db.scores.find(/N/) # use regexp: return every document with a value containing "N"
		> db.scores.find( { score: {$gt: 90} } ) # return only documents where value "score">90
		> db.scores.find( { score: {$gt: 90} } ).count() # return number of documents where value "score">90
		> db.scores.find( {grade: 'B'} ) # return only documents where value "grade"="B"
		> db.scores.find( {grade: 'A'} ).explain() # return query plan
			{
	      "cursor" : "BasicCursor",
	      "isMultiKey" : false,
	      "n" : 561,
	      "nscannedObjects" : 3000,
	      "nscanned" : 3000,
	      "nscannedObjectsAllPlans" : 3000,
	      "nscannedAllPlans" : 3000,
	      "scanAndOrder" : false,
	      "indexOnly" : false,
	      "nYields" : 0,
	      "nChunkSkips" : 0,
	      "millis" : 2,
	      "indexBounds" : {
	      },
	      "server" : "localhost.localdomain:27017"
			}
    > db.tweets.distinct('source') # find all differentes values of a column

### Insert

		> db.scores.insert( {'name': 'toto'}) # insert a document with value name="toto"

### Update

		> db.scores.update( {score: { $gt: 90}}, {$set: { grade:'A' }}, false, true) # update value "grade" where value "score">90
		> db.scores.update( {score: { $gt: 80 , $lt: 90}}, {$set: { grade:'B' }}, false, true) # update value "grade" where value "score"<90 and "score">80
		> db.collection.update( $query, $update, $options ) # options: upsert, multi
		> db.scores.update( {grade: 'B'}, {$unset: {grade: ""}}, false, true) # update value "grade" to <null> where value "grade"="B"

### Drop

		> db.foo.drop() # drop collection

ReplicaSet
----------

3 nodes minimum always odd number to handle majority:
 - 1 primary (rw)
 - 2 secondaries (ro)
 - replication from primary to secondaries
 - heartbeat between all nodes every 2s

MongoDB driver in application handle reads and writes between the nodes

Secundary can be:
 - Arbiter: Secundary without data
 - Hidden: not in read pool, reads query need to be forced, used for backup nodes
 - slaveDelay: replication with delay, used for fast recovery in case of data corruption

Replication replicates operations (insert, update...), not datas (bytes)

If primary becomes unavailable before end of sync to secondary, modifications are written in rollback file when it comes back online. The rollback file can be played manually by mongo admins.

Only one mongodb per server!

Example:

1. Start mongodb in RS: `mongod --dbpath . --oplogSize 50 --port 30000 --replSet=REP`
1. Initiate RS: `> rs.initiate()`
1. Check Primary: `db.isMaster()`
1. Add nodes: `rs.add( 'localhost.localdomain:30001' )`

Write concern: Primary waits end of replications on X nodes before commit to application

Read concern:

 - nearest: read on mongodb with best response time (primary or secundary)

Admin Operation: always on secondaries before primary

Force node1 primary:

    cfg = rs.conf()
    cfg.members[0].priority = 1
    cfg.members[1].priority = 0
    cfg.members[2].priority = 0
    rs.reconfig(cfg)

Force node2 primary:

    cfg = rs.conf()
    cfg.members[2].priority = 1
    rs.reconfig(cfg)
    rs.stepDown() # primary becomes secondary

Shard
-----

 - Sharding = Partitionning
 - Shard Key = Partitionnement Key

A shard can be a single node or replicaset

Mongos handle loadbalancing to shard

Chunk: 64Mo data block in shard

Every connection to the DB use mongos (no direct connections to nodes)

Configsrv stores index of the chunks

Shard minimal configuration:

 - 1 mongos
 - 3 configsrv
 - 1 rs of 3 mongodb
 - mongos and configsrv can be mutualize for many mongodb

Example for 3 shards:

 - 3 configsrv with:
   - 1 process cfg srv -> Shard Instance Mongod 1
   - 1 process cfg srv -> Shard Instance Mongod 2
   - 1 process cfg srv -> Shard Instance Mongod 3
 - 1 mongos with:
   - 1 process mongos -> Shard Instance Mongod 1
   - 1 process mongos -> Shard Instance Mongod 2
   - 1 process mongos -> Shard Instance Mongod 3
 - 3 shards (replicasets)

Configure shard:

1. Start configsrv: `mongod --configsvr --dbpath /data/cfg`
1. Start mongos: `mongos --configdb mongo:27019`
1. Start mongod: `mongod --port 40000 --dbpath .`
1. Configure shard:

    ```
    # mongo
    mongos> sh.addShard('mongo:40000')
    { "shardAdded" : "shard0000", "ok" : 1 }
    mongos> db.printShardingStatus()
    --- Sharding Status ---
      sharding version: {
            "_id" : 1,
            "version" : 3,
            "minCompatibleVersion" : 3,
            "currentVersion" : 4,
            "clusterId" : ObjectId("51cd4d5c3ebee847b21ef8be")
    }
      shards:
            {  "_id" : "shard0000",  "host" : "mongo:40000" }
      databases:
            {  "_id" : "admin",  "partitioned" : false,  "primary" : "config" }
            {  "_id" : "test",  "partitioned" : false,  "primary" : "shard0000" }
    ```

1. Check configuration:

    ```
    mongos> use config
    mongos> db.shards.find()
    { "_id" : "shard0000", "host" : "mongo:40000" }
    mongos> db.mongos.find()
    { "_id" : "mongo:27017", "mongoVersion" : "2.4.4", "ping" : ISODate("2013-06-28T09:01:15.743Z"), "up" : 895, "waiting" : true }
    mongos> db.databases.find()
    { "_id" : "admin", "partitioned" : false, "primary" : "config" }
    { "_id" : "test", "partitioned" : false, "primary" : "shard0000" }
    ')
    ```

1. Import datas: `mongoimport -c tweets -d twitter twitter.json`
1. Activate sharding: `sh.enableSharding('twitter')`
1. Create index: `db.tweets.ensureIndex( {'user.screen_name':1, 'id': -1})`
1. Create Shard Key on index: `sh.shardCollection( 'twitter.tweets', { 'user.screen_name':1,'id':1})`
1. Check chunks:

    ```
    use config
    db.chunks.find().pretty()
    ```

1. Start 2nd shard:

    ```
    mongod --dbpath . --port 40001 --replSet=SHARD2 --oplogSize 50
    mongo --port 40001
    mongos> rs.initiate()
    mongos> sh.addShard('SHARD2/mongo:40001')
    mongos> use config
    mongos> db.chunks.find().pretty()
    ```

Monitoring
----------

 - With MongoDB commands:

```
echo "db.serverStatus()" | mongo
echo "sh.status()" | mongo
mongostat --discover
```

 - With SNMP MIB
 - With MMS (hosted or private)
 - With Nagios MongoDB plugin

Backup
------

What do we need to backup:
 - mongos: no need to backup
 - configsrv: to backup
 - mongod: to backup
 - replicaset: backup one node of RS
 - shard: backup one node of the shard

Backup commands:
 - copy /data/db with db stopped
 - mongodump: lock the system (db can be online or offline)
 - mongoexport: lock & export to json file
 - snapshot LVM: lock not necessary but better

Lock DB:

    mongo --eval "db.fslock(true)"
    mongo --eval "db.fslock(false)"

Backup ReplicaSet:

1. Force replication on secundary, then offline secondary and dump
1. Step down primary, bring down the node, backup it and bring it online
1. Have a dedicated slave node used only for backup, bring it online, force sync when it's backup time and put it offline the rest of the time
2.
WARNING: oplog of primary need to have all logs between 2 backups (oplog: circular archive log)

Incremental backup:

1. Day 1: mongodump: full
1. Day 2: mongodump --oplog: backup oplog (delta)

Backup Shard:

1. Stop Balancing: `stopBalancer()`
1. Backup 1 configsrv
1. Backup 1 node of each shard
1. Start Balancing

Administration Tasks
--------------------

 - `compact`
 - `repair`
