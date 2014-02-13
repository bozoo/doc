MONGODB
=======

Stop Mongos: `mongos> db.shutdownServer()`

Authentication
--------------

 - with Standalone: add `auth = true` in mongod.conf
 - with ReplicaSet or Shard: add `keyFile = /path/to/keyFile` in mongod.conf

Generate keyFile: `openssl rand -base64 753 > keyfile`

Create Admin User:

```javascript
mongo>use admin
mongo>db.addUser( { user: "<username>",
                    pwd: "<password>",
                    roles: [ "userAdminAnyDatabase" ]
                  }
                )
```

Localhost Authentication:

If there are no users for the admin database, you can connect with full administrative access via the localhost interface. This approach is useful, for example, if you want to run mongod or mongos with authentication before creating your first user.

To authenticate via localhost, connect to the mongod or mongos from a client running on the same system. Your connection will have full administrative access.

To disable the localhost bypass: add `setParameter enableLocalhostAuthBypass=0` in mongod.conf

Shard specific:

When we are on a shard, User admin must be created in mongos and in mongodb!!!
