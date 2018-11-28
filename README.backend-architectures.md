- Functionality to implement

Build a CRUD of user on top of a data distributted architecture. The public interface of the system it's a based on a RESTFull Microservices API:

GET /api/user/{userID} 			(read an user)
POST /api/user/ 				(create an user)
PUT /api/user/ 					(update an user)
DELETE /api/user/{userID} 		(delete an user)

Packaging / Orchestration engine:

Docker - Docker Compose


Goal architectures: (select and implement one)

Distributed Database Architecture
Event Sourcing + CQRS Architecture



Distributed Database Architecture (Details)

The system it's built using 2 type of nodejs processes:

- Gateway process:					[GW_N: 1 (default number of replicas that need to be running)]

Hosts the RESTful API and communicates with the DB processes) 	

- DB processes						[DB_N: 3 (default number of replicas that need to be running)]

Each process stores one or multiple shard/partition of the database inside of a RocksDB table.

On every request the Gateway needs to communicate with the DB processes in order to obtain and or modify the data stored in one or multiple shards.

The internal (Gateway <=> DBs) communication protocol it's up to you choice (HTTP, TCP, Websockets, ...), keep in mind the 


- Requirements

Liveness checks:

- The system is able to respond correctly and in less than 250ms to all CRUD requests

Safety checks

- The system is able to support the downtime of 1 DB process without any problem to the service
- The system is able to recover a failed DB process after a successfull restart

Performance / Complexity

- The cyclomatic complexity of each operation should not increase with DB_N, should be constant K or at least <= ( DB_N / 2 ) + 1 (Quorum size)
- The DB space complexity should be keep <= 2 times the total size of the data stored

Extra points:
No extra points available for this architecture



Event Sourcing + CQRS Architecture

The system it's built using 2 type of nodejs processes:

- Gateway processes					[GW_N: 1 (default number of replicas that need to be running)]

Hosts the RESTful API and emits events for every CUD operation. Event should be inmutable and stored on MongoDB or Kafka (topic/collection: Events). READ operations are forwarded to the corresponding Aggregator process.

When an Aggregator is down the Gateway can relay MongoDB to support READ request directed to his specific partition of the Users.


- Aggregator processes 				[AGG_N: 3 (default number of replicas that need to be running)]

Collect the CUD events and applies the changes to the memory stored representation of the Users entity (the state of the application). 

[Extra] Every 100 events, the changes applied to the entities on memory are persisted to an Users mongodb collection, this process is known as snapshot saving.

[Extra] On the startup an aggregator should be able to read his partition of the Users collection and apply all the non processed Events in order to be back in service, this process is known as snapshot restore.

Apart from computing state aggregations the Aggregators offers a services (using the desired protocol HTTP, TCP, websockets, oplog DB communication, Kafka messages) to respond READ request from the Gateway.


Safety checks

- The system is able to recover a failed Aggregator process after a successfull restart and Events reprocesing

Performance / Complexity

- The cyclomatic complexity of each operation should not increase with DB_N, should be constant K or at least <= ( DB_N / 2 ) + 1 (Quorum size)
- The DB space complexity should be keep equal to the total size of the data stored

Extra points:
- Snapshot saving: Only the modified entities should be persisted every 100 processed Events to the Users collection.
- Snapshot restore: Aggregators can be back in service just after recovering his state from the MongoDB Users collection (partition)
- The system is able to support the downtime of multiple aggregators processes without any problem to the service (outdated entities are accepted during downtime, downtime partial inconsistency). Failsafe mechanism.


