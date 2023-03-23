# Steps
1. **Install MongoDB**: install with network service option and follow the default options during installation.
2. **Create data folders for database instances**: create 3 different folders for three members of the cluster, like the following:
- path: `C:\Program Files\MongoDB\Server\6.0\rs0` containing the following directories and files:
```
    `instance0` -> is the folder of the first MongoDB instance.
        `data` -> is the place where data of this MongoDB instance is stored.
        `log` -> is the place where log files are stored.
        `mongod.cfg` -> is the config file for the MongoDB instance in this cluster.
    `instance1`
        `data`
        `log`
        `mongod.cfg`
    `instance2`
        `data`
        `log`
        `mongod.cfg`
```
Then we have three different databases that will be synchronized with each other by MongoDB.

The same directory has been created in this tutorial.

3. **Stop the MongoDB service**: open Services from Start menu and stop the MongoDB service. Or you can run the following command:
```sh
net stop MongoDB
```
4. **Create Services**: create one service for each MongoDB instance by running the following commands one by one:
```sh
mongod --config "C:\Program Files\MongoDB\Server\6.0\rs0\instance0\mongod.cfg" --install
mongod --config "C:\Program Files\MongoDB\Server\6.0\rs0\instance1\mongod.cfg" --install
mongod --config "C:\Program Files\MongoDB\Server\6.0\rs0\instance2\mongod.cfg" --install
```
5. **Add MongoDB to firewall**: add mongod.exe and mongosh.exe to pass the firewall. I am not sure if this step is required.
6. **Start MongoDB service**:
```sh
net start MongoDB
```
7. **Start Service**: start MongoDB service for this replica set by running the following command:
```
mongod.exe --config "C:\Program Files\MongoDB\Server\6.0\rs0\instance0\mongod.cfg"
```
Hint: We should start this service when the computer is starting. We are not able to connect to the replica set without running one of the replica set services.
8. **Start MongoShell**: run the following command:
```sh
mongosh  mongodb://127.0.0.1:37017/?replicaSet=rs0"&"directConnection=true
```
9. **Create Replica Set CFG**: we need a cfg object to create the replica set:
```javascript
let cfg = {
    "_id": "rs0",
    "version": 1,
    "members": [
      {
        "_id": 0,
        "host": "localhost:37017",
        "priority": 2
      },
      {
        "_id": 1,
        "host": "localhost:37018",
        "priority": 0
      },
      {
        "_id": 2,
        "host": "localhost:37019",
        "priority": 0
      }
    ]
  };
```
Then run the following command in Mongo shell:
```javascript
rs.initiate(cfg);
```
or if it is already instantiated, then run the following command:
```javascript
rs.reconfig(cfg);
```
if you get `ok: 1`, it means everything is correct and the replica set is created and you can connect to the replica set via Compass with the following connection string:
```
mongodb://localhost:37017,localhost:37018,localhost:37019/?replicaSet=rs0
```
