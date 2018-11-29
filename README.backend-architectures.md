# Functionality to implement

Build microservices able to provide CRUD (CREATE, READ, UPDATE, DELETE) functionalities for an User entity on top of a data distributted architecture. The public interface of the system it's a based on a RESTFul Microservices API conformed by:

```
GET /api/user/{userID} 			(READs an user)

POST /api/user/ 			(CREATEs an user)

PUT /api/user/ 				(UPDATEs an user) 

DELETE /api/user/{userID} 		(DELETEs an user)
```

*NOTE:* the API allows partial updates, using a non-complete Entity schema

### User entity schema
```
{
    id: '13AE742', // an UUID string
    name: 'John Doe',
    email: 'john.doe@gmail.com',
    group: 3
}
```
Only schema validations are required, you don't need to check each of the fields.

## Packaging / Orchestration engine

Docker - [Docker Compose](https://docs.docker.com/compose/)

Ensure to provide an easy way to test your solution by using 2 Dockerfiles (Gateway and DB/Aggregator) and a docker-compose.yaml file where to coordinate the startup and default configuration of the processes.

Solutions should be provided as a link to an open source repository (github, bitbucket or gitlab are the accepted choices). Add a README.md file for additional explanations / documentation.

## Goal architectures: (select and implement one)

- Distributed Database Architecture

- [Event Sourcing](https://martinfowler.com/eaaDev/EventSourcing.html) + [CQRS Architecture](https://martinfowler.com/bliki/CQRS.html)



## Distributed Database Architecture (Details)

![](/Distributed%20Database%20Architecture.png)

The system it's built using 2 type of nodejs processes:

### Gateway process

`container name: gateway`

*(number of containers GW_N=1 )*

Hosts the RESTful API and communicates with the DB processes) 	

### DB processes
`container names: db-0, db-1, db-2`

*(number of containers  DB_N=3 )*

Each process stores one or multiple database shards/partitions of the complete Users data inside of their embeded [RocksDB](https://rocksdb.org/) tables.

On every API request the Gateway needs to communicate with the DB processes in order to obtain and or modify the data stored in one or multiple shards.

The internal (Gateway <=> DBs) communication protocol it's up to you choice (HTTP, TCP, Websockets, ...), keep in mind the selection of the protocol has a big effect on the complexity / scalability of the implementation.

### Requirements

#### Liveness checks:

- The system is able to respond correctly and in less than 250ms to all basic CRUD requests

#### Safety checks:

- The system is able to support the downtime of 1 DB process without any problem to the READ service
- The system is able to recover a failed DB process after a successfull restart 
( test this with: `docker-compose stop db-1; sleep 60; docker-compose start db-1;` )

#### Performance / Complexity

- The cyclomatic complexity of each operation should not increase with DB_N, should be constant K or at least <= ( DB_N / 2 ) + 1 (Quorum size)
- The DB space complexity should be keep <= 2 times the total size of the data stored

#### Extra points
- Implement a distributed COUNT query (GET /api/user/count) skipping duplicates
- Implement a distributed LIST query (GET /api/user?offset=100&limit=100) that supports pagination



### Event Sourcing + CQRS Architecture (Details)

![](/Event%20Sourcing-CQRS%20Architecture.png)

The system it's built using 2 type of nodejs processes:

### Gateway process	 
`container name: gateway`

*(number of containers  GW_N=1 )*

Hosts the RESTful API and emits events for every CUD operation. Events should be inmutable and stored on [MongoDB](https://www.mongodb.com/) or [Kafka](https://kafka.apache.org/) (topic/collection: Events). READ operations are forwarded to the corresponding Aggregator process.

After emiting the event the Gateway sends an *optimistic response back to the API client.*

**[Extra]** When an Aggregator is down the Gateway can rely on Users collection / topic to support READ request directed to his specific partition of the Users.


### Aggregator processes 
`container names: aggregator-0, ...`

*(number of containers  AGG_N=3)*

Collect the CUD events and applies the changes to the memory stored representation of the Users entity (the state of the application). 

Apart from computing state aggregations the Aggregators provide service to resolve the READ requests received by the Gateway. (use the desired communication protocol HTTP, TCP, websockets, oplog DB communication, [Kafka](https://kafka.apache.org/) *messages)*

**[Extra]** Every 100 events, the changes applied )othe entities on memory are persisted to an Users [MongoDB](https://www.mongodb.com/) collection or a [Kafka](https://kafka.apache.org/) compacted topic, *this process is known as snapshot saving.*

**[Extra]** On the startup an aggregator should be able to read his partition of the Users collection/topic and apply all the non processed Events in order to restore his state and be back in service, this process is known as snapshot restore.

### Requirements

#### Safety checks

- The system is able to recover a failed Aggregator process after a successfull restart and Events reprocesing
- The system do not miss any CUD operation even when all Aggregators are down

#### Performance / Complexity

- The cyclomatic complexity of each operation should not increase with DB_N, should be constant K or at least <= ( DB_N / 2 ) + 1 (Quorum size)
- The DB space complexity should be keep equal to the total size of the data stored

#### Extra points
- Snapshot saving: Only the modified entities should be persisted every 100 processed Events to the Users collection.
- Snapshot restore: Aggregators can be back in service just after recovering his state from the [MongoDB](https://www.mongodb.com/) Users collection (partition)
- The system is able to support the downtime of multiple aggregators processes without any problem to the service (READ of outdated entities are accepted during downtime, downtime partial inconsistency). Failsafe mechanism.

### General Code Requirements
- Clean and readable code
- Functional oriented code [no stateful objects are being used]
- Reduced call stack depth [max. of 3 nested function calls] 

### Additional considerations:
After finalizing the test you should have some ideas about:

- Advantages and problems of the implemented architecture
- Ways on how to scale dynamically each of the layers of the architecture
- Improvements that could be performed and their impact on the solution
- Use cases of the selected architecture

