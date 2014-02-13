MONGODB TRAINING
================

Table => Collection
Row => JSON Document
Index => Index
Join => Embedding & Linking
Partition => Shard
Partition Key => Shard Key

With MongoDB, every values of a row are returned each time

Data format: BSON (Binary JSON)

The full database stays in Virtual Memory (limitation in 32-bits OS: 2Go max for database)

DBpath:
 - Namespace file: `${database}.ns`
 - Datafiles: `${database}.0` (64Mo), `${database}.1` (128Mo), `${database}.2` (256Mo), ...

Datafiles size x2 for each new files

Documents of same collections are always in same datafile

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

Find:

		> db.scores.find() # show every documents of scores collection in current database
		> db.scores.find().pretty() # return every documents with "one value per line format"
		> db.scores.findOne() # return only the first document of the collection
		> db.scores.find({    })
		> db.scores.find(/N/) # use regexp: return every document with a value containing "N" 
		> db.scores.find( { score: {$gt: 90} } ) # return only documents where value "score">90
		> db.scores.find( { score: {$gt: 90} } ).count() # return number of documents where value "score">90
		> db.scores.find( {grade: 'B'} ) # return only documents where value "grade"="B"
		> db.scores.find( {grade: 'A'} ).explain()
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

Insert:

		> db.scores.insert( {'name': 'toto'}) # insert a document with value name="toto"

Update:

		> db.scores.update( {score: { $gt: 90}}, {$set: { grade:'A' }}, false, true) # update value "grade" where value "score">90
		> db.scores.update( {score: { $gt: 80 , $lt: 90}}, {$set: { grade:'B' }}, false, true) # update value "grade" where value "score"<90 and "score">80
		> db.collection.update( $query, $update, $options ) # options: upsert, multi
		> db.scores.update( {grade: 'B'}, {$unset: {grade: ""}}, false, true) # update value "grade" to <null> where value "grade"="B"

Drop:

		> db.foo.drop() # drop collection
--------------------------------------------------------
# indique le temps de la requete (Èquivalent plan d'execution?)
## REPLICA SET
3 nodes:
 - 1 primaire => recoit toutes les demandes d'Ècriture et replique vers secondaires
 - 2 secondaires => recoivent uniquement les demandes de consultation
 - heartbeat entre tous les nodes toutes les 2s
 
 ??? arbiter node ???
 
Le drivers MongoDB sur l'application cliente gere la repartition des read et write vers les diffÈrents nodes.
possibilitÈ de configurer des slaves :
 - hidden => read uniquement en spÈcifiant ce node. ce node n'est pas dans le pole de read
 - slaveDelay => replication avec un dÈlais (reprise rapide en cas de corruption)
 
replication des opÈrations (insert, update), pas de rÈplication des donnÈes (differentiel bits)
si un master devient inaccessible avant d'avoir rÈpliquÈ les modifications sur les slaves
quand il reviens en ligne, il ecrit ses modifications dans un rollback file qui peut etre jouÈ manuellement par les admin
1 seule instance mongo par serveur
Attention :
Un rÈplicat Set doit avoir au minimum 3 nodes
Pour utiliser 2 machines:
 - 1 srv with 1 node master + 1 node arbiter
 - 1 srv with 1 node secondary
 => TRES MAL
 => On perd tout si on perd la machine avec l'arbiter
 
REPLICA SET avec 3 nodes
 - OK si on perd 1 node
 - KO si on perd 2 nodes
 
REPLICA SET avec 4 nodes
 - OK si on perd 1 node
 - KO si on perd 2 nodes
 
# Demarre un node dans le replica set:
mongod --dbpath . --oplogSize 50 --port 30000 --replSet=REP # oplogSize : taille des logs
# initie le replica set
> rs.initiate()
{
        "info2" : "no configuration explicitly specified -- making one",
        "me" : "localhost.localdomain:30000",
        "info" : "Config now saved locally.  Should come online in about a minute.",
        "ok" : 1
}
# Pour connaitre qui est le master
REP:PRIMARY> db.isMaster()
{
        "setName" : "REP",
        "ismaster" : true,
        "secondary" : false,
        "hosts" : [
                "localhost.localdomain:30000"
        ],
        "primary" : "localhost.localdomain:30000",
        "me" : "localhost.localdomain:30000",
        "maxBsonObjectSize" : 16777216,
        "maxMessageSizeBytes" : 48000000,
        "localTime" : ISODate("2013-06-27T13:29:14.121Z"),
        "ok" : 1
}
# pour ajouter des nodes
REP:PRIMARY> rs.add( 'localhost.localdomain:30001' )
{ "ok" : 1 }
REP:PRIMARY> rs.add( 'localhost.localdomain:30002' )
{ "ok" : 1 }
# possibilitÈ de configurer mongo pour attendre la replication sur x nodes pour envoyer le commit ‡ l'application
# Preference pour Read
nearest => read se fait sur le mongo avec le meilleur temps de rÈponse qu'il soit primaire ou secondaire
# a utiliser pour AB
# Maintenance
toujours faire la maintenance sur les secondary en premier
puis faire le primary en dernier
# Replica Set 3 Nodes 2 DC
DC 1 : P + S
DC 2 : S
si on perd le DC2 : tout est OK
si on perd le DC1 : on peut toujours lire mais on ne peut plus rien Ècrire => est-ce possible pour AXA Banque???
# pour forcer node 1 primaire:
cfg = rs.conf()
cfg.members[0].priority = 1
cfg.members[1].priority = 0
cfg.members[2].priority = 0
rs.reconfig(cfg)
# pour reforcer node 2 primaire:
cfg = rs.conf()
cfg.members[2].priority = 1
rs.reconfig(cfg)
rs.stepDown() # Met Hors service le node
SHARD = Partitionning
Un Shard peut-etre sur un seul node ou sur un replica set
MongoS gËre le loadbalancing vers les nodes
Chunk = bloc de 64Mo de datas dans le shard
Dans un shard, toutes les connexions ‡ la db se dÈroulent sur le mongoS (y comprit les connexions applicatives). aucune connexions en direct sur les nodes du shared
Le config server contient l'index des chunks du shard
On ne peut pas avoir plus de shard que de chunk
Shard nÈcessite
1 mongos
3 config server
1 replica set de 3 mongodb
Les MongoS et ConfigServer peuvent-Ítre mutualisÈs pour plusieurs instances mongodb
SHARD KEY = Partitionnement Key
ex:
3 Config srv avec sur chaque:
 - 1 process cfg srv -> Shard Instance Mongod 1
 - 1 process cfg srv -> Shard Instance Mongod 2
 - 1 process cfg srv -> Shard Instance Mongod 3
1 MongoS srv avec :
 - 1 process mongos -> Shard Instance Mongod 1
 - 1 process mongos -> Shard Instance Mongod 2
 - 1 process mongos -> Shard Instance Mongod 3
3 Shard (Replica Set de 3 machines) : un pour chaque instance
 
# CONFIG SHARD
# 1. config server # port par defaut 27019
mongod --configsvr --dbpath /data/cfg
# 2. mongos: # port par defaut 27017
mongos --configdb mongo:27019
# 3. mongod
mongod --port 40000 --dbpath .
# 4. mongos
[root@mongo ~]# mongo
MongoDB shell version: 2.4.4
connecting to: test
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
mongos> use config
switched to db config
mongos> show collections
changelog
chunks
databases
lockpings
locks
mongos
settings
shards
system.indexes
version
mongos> db.shards.find()
{ "_id" : "shard0000", "host" : "mongo:40000" }
mongos> db.mongos.find()
{ "_id" : "mongo:27017", "mongoVersion" : "2.4.4", "ping" : ISODate("2013-06-28T09:01:15.743Z"), "up" : 895, "waiting" : true }
mongos> db.databases.find()
{ "_id" : "admin", "partitioned" : false, "primary" : "config" }
{ "_id" : "test", "partitioned" : false, "primary" : "shard0000" }
')
# 4. Import Datas sur le mongos
root@mongo Data]# mongoimport -c tweets -d twitter twitter.json
# 5. ACTIVER LE SHARDING
mongos> sh.enableSharding('twitter')
{ "ok" : 1 }
# 6. CrÈer un index
mongos> db.tweets.ensureIndex( {'user.screen_name':1, 'id': -1})
# 7. CrÈer la Shard Key sur l'index
mongos> sh.shardCollection( 'twitter.tweets', { 'user.screen_name':1,'id':1})
{ "collectionsharded" : "twitter.tweets", "ok" : 1 }
# pour voir les chunks
use config
db.chunks.find().pretty()
# 8. dÈmarrer un 2 Ëme shard
mongod --dbpath . --port 40001 --replSet=SHARD2 --oplogSize 50
mongo --port 40001
rs.initiate()
mongos> sh.addShard('SHARD2/mongo:40001')
{ "shardAdded" : "SHARD2", "ok" : 1 }
mongos> use config
mongos> db.chunks.find().pretty()
{
        "_id" : "twitter.tweets-user.screen_name_MinKeyid_MinKey",
        "lastmod" : {
                "t" : 2,
                "i" : 0
        },
        "lastmodEpoch" : ObjectId("51cd5a963ebee847b21efaf5"),
        "ns" : "twitter.tweets",
        "min" : {
                "user.screen_name" : { "$minKey" : 1 },
                "id" : { "$minKey" : 1 }
        },
        "max" : {
                "user.screen_name" : "Sickaaa",
                "id" : NumberLong("22823834700")
        },
        "shard" : "SHARD2"
}
{
        "_id" : "twitter.tweets-user.screen_name_\"Sickaaa\"id_22823834700",
        "lastmod" : {
                "t" : 2,
                "i" : 1
        },
        "lastmodEpoch" : ObjectId("51cd5a963ebee847b21efaf5"),
        "ns" : "twitter.tweets",
        "min" : {
                "user.screen_name" : "Sickaaa",
                "id" : NumberLong("22823834700")
        },
        "max" : {
                "user.screen_name" : "lizloopez",
                "id" : NumberLong("22825677300")
        },
        "shard" : "shard0000"
}
{
        "_id" : "twitter.tweets-user.screen_name_\"lizloopez\"id_22825677300",
        "lastmod" : {
                "t" : 1,
                "i" : 2
        },
        "lastmodEpoch" : ObjectId("51cd5a963ebee847b21efaf5"),
        "ns" : "twitter.tweets",
        "min" : {
                "user.screen_name" : "lizloopez",
                "id" : NumberLong("22825677300")
        },
        "max" : {
                "user.screen_name" : { "$maxKey" : 1 },
                "id" : { "$maxKey" : 1 }
        },
        "shard" : "shard0000"
}
Supervision / alertes:
echo "db.serverStatus()" | mongo
echo "sh.status()" | mongo
mongostat --discover
MIB SNMP
MMS mms.10gen.com (hosted or private service)
nagios plugins mongodb
# Trouver toutes les valeurs diffÈrentes d'une colonne
db.tweets.distinct('source')
# BACKUP
mongos => inutile
config server => A SAUVEGARDER
mongod => A SAUVEGARDER
replica set => SAUVEGARDER une machine du rs
shard => SAUVEGARDER Une machine du shared
MONGOD
 - copy /data/db with db stopped
 - mongodump : lock the system (peut-etre lancÈ avec mongodb dÈmarrÈ ou arrÈtÈ)
 - mongoexport : lock & export to json file
 - snapshot fs : lock db pas forcÈment nÈcessaire mais recommendÈ (configuration hardware (fs type, raid, cpu doivent Ítre identique sur la machine sur laquelle on restore)
 
lockdb:
 mongo --eval "db.fslock(true)"
 mongo --eval "db.fslock(false)"
REPLICA SET
1.
force replication on secundary
then offline secondary and dump
2.
step down primary
bring down the node
backup it
bring it online
3.
Have a dedicated slave node used only for backup
bring it online & force sync when it's backup time and put it offline the rest of the time
le oplog du primaire doit contenir tous les archives logs entre les 2 backups
oplog = archive log rotatif
4. Incremental backup
4. Incremental backup
J1 : mongodump ==> full
J2 : mongodump --oplog ==> sauvegarde le oplog (delta)
SHARD
config server :
- stopbalancer()
 and backup like other mongodb
listofShard.forEach(function(x))
if(x.replicaSet()==true)
backupReplicaSet
else
backup(x)
enablebalancer()
config server modification by:
balancing
split
add/remove shard
.dbshell contient toutes les commandes passÈes dans mongo
kchodorrow.com : site aide mongodb
# ADMINISTRATION TASKS
compact
repair
### MONGO DB BACKUP PLAN
# MONGO SINGLE NODE
# mongod backup
$ mongodump --port 40000 --directoryperdb -v -o /backup/single
# mongod oplog backup
$ mongodump --port 40000 --oplog -o /backup/single.log
# MONGO SHARD
# mongos stop balancer
$ mongo
mongos> sh.stopBalancer()
# check mongos balancer
$ mongo
mongos> sh.getBalancerState()
# config server backup
$ mongodump --port 27019 --directoryperdb -v -o /backup/cfgsvr
# config server backup oplog
$ mongodump --port 27019 --oplog -o /backup/cfgsvr.log
# shard force replication from primary to secundary
???
# shard node3 backup
$ sh.g               --port 50003 --directoryperdb -v -o /backup/shard
# shard node3 backup oplog
$ mongodump --port 50003 --oplog -o /backup/shard.log
# mongos enable balancer
$ mongo
mongos> sh.startBalancer()
# check mongos balancer
$ mongo
mongos> sh.getBalancerState()