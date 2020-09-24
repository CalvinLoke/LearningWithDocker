# Bitnami-MongoDB
An alternative to using the original MongoDB image is to use the BitNami image instead. While there are many reasons to use the BitNami image, I personally use it as it is very straightforward and integrates sharding and replica-sets into a single image. It streamlines the process and removes a lot of the unncessary set up that is needed using the vanilla MongoDB image. 

## Pulling the Bitnami MongoDB image 
`docker pull bitnami/mongodb-sharded`

## Cloning the repository 
`https://github.com/bitnami/bitnami-docker-mongodb-sharded`

## Docker compose file raw dumps
Alternatively, here is the `docker-compose` file for singe-shard, no replica-set deployment:

### Single-shard, no-replica-set deployment

```
version: '2'

services:
  mongodb-sharded:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-sharded
      - MONGODB_SHARDING_MODE=mongos
      - MONGODB_CFG_PRIMARY_HOST=mongodb-cfg
      - MONGODB_CFG_REPLICA_SET_NAME=cfgreplicaset
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_ROOT_PASSWORD=password123
    ports:
      - "27017:27017"

  mongodb-shard0:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard0
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_MONGOS_HOST=mongodb-sharded
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=shard0
    volumes:
      - 'shard0_data:/bitnami'

  mongodb-cfg:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-cfg
      - MONGODB_SHARDING_MODE=configsvr
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=cfgreplicaset
    volumes:
      - 'cfg_data:/bitnami'

volumes:
  shard0_data:
    driver: local
  cfg_data:
    driver: local
    
```

### Multi-shard no replication

```
version: '3'

services:
  mongodb-sharded:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-sharded
      - MONGODB_SHARDING_MODE=mongos
      - MONGODB_CFG_PRIMARY_HOST=mongodb-cfg
      - MONGODB_CFG_REPLICA_SET_NAME=cfgreplicaset
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_ROOT_PASSWORD=password123
    ports:
      - "27017:27017"

  mongodb-shard0:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard0
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_MONGOS_HOST=mongodb-sharded
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=shard0   

  mongodb-shard1:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard1
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_MONGOS_HOST=mongodb-sharded
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=shard1
      
  mongodb-shard2:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard2
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_MONGOS_HOST=mongodb-sharded
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=shard2      

      
  mongodb-cfg:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-cfg
      - MONGODB_SHARDING_MODE=configsvr
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=cfgreplicaset
```

### Multi-shard (3) with replication (1P 2S, for config and shards)

```
version: '3'

services:
  mongodb-sharded:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-sharded
      - MONGODB_SHARDING_MODE=mongos
      - MONGODB_CFG_PRIMARY_HOST=mongodb-cfg-primary
      - MONGODB_CFG_REPLICA_SET_NAME=cfgreplicaset
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_ROOT_PASSWORD=password123
    ports:
      - "27017:27017"

  mongodb-shard0-primary:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard0-primary
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_MONGOS_HOST=mongodb-sharded
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=shard0   

  mongodb-shard0-secondary0:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    depends_on:
      - mongodb-shard0-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard0-secondary0
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-shard0-primary
      - MONGODB_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_REPLICA_SET_NAME=shard0      
      
  mongodb-shard0-secondary1:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    depends_on:
      - mongodb-shard0-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard0-secondary1
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-shard0-primary
      - MONGODB_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_REPLICA_SET_NAME=shard0         
      
  mongodb-shard1-primary:  
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard1-primary
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_MONGOS_HOST=mongodb-sharded
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=shard1   

  mongodb-shard1-secondary0:

    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    depends_on:
      - mongodb-shard0-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard1-secondary0
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-shard1-primary
      - MONGODB_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_REPLICA_SET_NAME=shard1      
      
  mongodb-shard1-secondary1:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    depends_on:
      - mongodb-shard0-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard1-secondary1
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-shard1-primary
      - MONGODB_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_REPLICA_SET_NAME=shard1     
      
  mongodb-shard2-primary:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard2-primary
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_MONGOS_HOST=mongodb-sharded
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=shard2   

  mongodb-shard2-secondary0:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    depends_on:
      - mongodb-shard0-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard2-secondary0
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-shard2-primary
      - MONGODB_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_REPLICA_SET_NAME=shard2      
      
  mongodb-shard2-secondary1:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    depends_on:
      - mongodb-shard0-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-shard2-secondary1
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-shard2-primary
      - MONGODB_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_SHARDING_MODE=shardsvr
      - MONGODB_REPLICA_SET_NAME=shard2         
          
  mongodb-cfg-primary:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-cfg-primary
      - MONGODB_SHARDING_MODE=configsvr
      - MONGODB_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=cfgreplicaset
      
  mongodb-cfg-secondary0:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    depends_on:
      - mongodb-cfg-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-cfg-secondary0
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_PRIMARY_HOST=mongodb-cfg-primary
      - MONGODB_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=cfgreplicaset
      - MONGODB_SHARDING_MODE=configsvr  
      
  mongodb-cfg-secondary1:
    image: 'docker.io/bitnami/mongodb-sharded:4.4-debian-10'
    depends_on:
      - mongodb-cfg-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-cfg-secondary1
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_PRIMARY_HOST=mongodb-cfg-primary
      - MONGODB_PRIMARY_ROOT_PASSWORD=password123
      - MONGODB_REPLICA_SET_KEY=replicasetkey123
      - MONGODB_REPLICA_SET_NAME=cfgreplicaset
      - MONGODB_SHARDING_MODE=configsvr      
```

## To add
Persistent data, through the use of bind volumes. As I was creating these docker compose files for purely testing purposes, I omitted a bind volume. Do add if you would like to deploy this for longer periods of times and would like to have persistent data. 

Improve container names. As of now, I have left the container names as their default from the original docker-compose file that I referenced from Bitnami's repository. They are unncessarily long and make it difficult to identify in situtaitons whereby the CLI has limited working space. Do change it if you would prefer shorter container names in Docker.

Incorporate into Docker Swarm/Kubernetes: While it works excellently on a single host machine, it does not fair very well practically. Each memeber of the replica set should ideally have their own host to ensure redundency in the event of host failure. Hence, a container orchestration tool like Kubernetes or Docker Swarm would be highly recommended. 

~~After cloning, change the active directory to the cloned repo~~. 

It is highly recommended that you create your own docker-compose file based off either the ones that I have included in this document or from Bitnami's repository. This thus allows you to tune the parameters if needed. 

If you are doing this, ensure that you save the docker-compose file in a `.yml` format. 

# Running a MongoDB sharded cluster (1 shard only)
To begin sharding, simply use the command `docker-compose up` to run the default docker-compose file. This file contains the necesssary environment variables and configurations to set up the sharded cluster and replica sets. No logging into individual clusters to configure clusters required. 

## Verify that the 3 containers are running 
`docker ps`

## Access to the mongos instance
`docker exec -it <container name> bash`

Note that the container name for the mongos instance may be different, hence it is best to check using the `docker ps` command. 

## Authentication 
To authenticate into the mongos instance use the following command:

`mongo admin --username root --password123`

By default, the included `docker-compose` file in the cloned repo has set up authentication. If you would like, you could modify the file to remove said authentication by removing the environment variable 

`MONGODB_ROOT_PASSWORD=password123`

## Verify that you have root access
`show dbs`

This command will list out the different databases that are currently available. If it returns nothing, verify that you have authenticated into the server using the previous step or use 

`use admin`

Followed by

`db.auth( "root" , "password123" )`

## Create a test database
`use testDB`

## Create a test collection
`db.createCollection("testCollection0")`

For learning purposes, you might wish to create a second collection as a proof of concept. 

`db.createCollection("testCollection1")`

## Enable sharding for the database
`sh.enableSharding('testDB')`

## Shard the test collection
Depends on your current deployment, the sharding process will differ depending on whether an existing collection *already has data in it* or is an *empty collection that does not have any data in it*. So do check carefully before proceeding. 

### Sharding an empty/new collection
`sh.shardCollection('testDB.testCollection0', {"field0" : "hashed"} )`

Note that the parameters `{"field0" : "hashed"}` will be the field used as the shard key. It can either be `hashed` `1` for hashed and range based sharding respectively. For more information regarding the type of shard key used, please refer to [this](https://docs.mongodb.com/manual/reference/command/shardCollection/#dbcmd.shardCollection). 

It is important that if you wish to populate this collection afterwards, `field0` should be one of the fields for the data that you are going to import. For example, if the resulting collection is going to be `books` and you would wish to shard the collection based the `title`, replace `field0` with `title` instead. 

Once this has been done, proceed to import data. This can be done using a for-loop or using MongoDB Compass. 

### Sharding an existing collection
Import some sample/dummy data if needed into `testCollection1` for this demonstration purpose. 

Then create an index for the existing collection

`db.testCollection1.createIndex({ "field0":"hashed" })`

`field0` should be one the fields of the collection. It is recommended that you chooose something like the name or title of the entires as the index. 

Once this is done, proceed to use 

`sh.shardCollection('testDB.testCollection1', {"field0" : "hashed"} )`

With modifications to `field0` and `hashed` if required. 

## Checking the shard distribution (really only works well if you have more than 1 shard in your shard cluster)
`db.testCollection0.getShardDistribution()`

If you have created the second collection, you could repeat this command:
`db.testCollection1.getShardDistribution()`

The second command should yield a message that says that the collection is not sharded. Hence, this serves as a proof of concept that sharding can be done to shard *individual collections* as opposed to the entire database. 

## Further verify that that sharded cluster works
You could easily test if the sharded cluster works by adding sample or dummy data. This can be done many ways, either through a for-loop or using [MongoDB Compass](https://www.mongodb.com/products/compass). 

# Running a MongoDB sharded cluster (Multiple shards)
<I am still in the process of creating a multiple shard cluster, will be updating this when I am able to successfully deploy the cluster>

# References
This document was written using multiple references. Here are the ones that are of note:

[Original GitHub Repository for Bitnami's MongoDB Sharded Cluster image](https://github.com/bitnami/bitnami-docker-mongodb-sharded)

[Testing and debugging the sharded cluster, steps based off Just me and Opensource](https://www.youtube.com/watch?v=Rwg26U0Zs1o)

[MongoDB Documentation on Sharding](https://docs.mongodb.com/manual/reference/command/nav-sharding/)

If there is any lapse in my referencing, do inform me. I would be happy to modify the referencing material. 




