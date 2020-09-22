# Bitnami-MongoDB
An alternative to using the original MongoDB image is to use the BitNami image instead. While there are many reasons to use the BitNami image, I personally use it as it is very straightforward and integrates sharding and replica-sets into a single image. It streamlines the process and removes a lot of the unncessary set up that is needed using the vanilla MongoDB image. 

## Pulling the Bitnami MongoDB image 
`docker pull bitnami/mongodb-sharded`

## Cloning the repository 
`https://github.com/bitnami/bitnami-docker-mongodb-sharded`

After cloning, change the active directory to the cloned repo. 

# Running a MongoDB sharded cluster (1 shard only)
To begin sharding, simply use the command `docker-compose up` to run the default docker-compose file. This file contains the necesssary environment variables and configurations to set up the sharded cluster and replica sets. No logging into individual clusters to configure clusters required. 

## Verify that the 3 containers are running 
`docker ps`

## Access to the mongos instance
`docker exec -it <container name> bash`

Note that the container name for the mongos instance may be different, hence it is best to check using the `docker ps` command. 

## Authentication 
To authenticate into the mongos instance use the following command:

`mongo admin --username root --password123

By default, the included `docker-compose` file in the cloned repo has set up authentication. If you would like, you could modify the file to remove said authentication by removing the environment variable 

`MONGODB_ROOT_PASSWORD=password123`

## Verify that you have root access
`show dbs`

This command will list out the different databases that are currently available. If it returns nothing, verify that you have authenticated into the server using the previous step or use 

`use admin`

Followed by

`db.auth( root, password123 )`

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

`sh.shardCollection('testDB.testCollection0', {"field0" : "hashed"} )`

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




