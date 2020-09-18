# Replica Sets in MongoDB
Replica Sets in MongoDB serve as a form of redundency. And in the even that one of the MongoDB instances fails, the other instances in the same cluster can take over. Note that Sharding and Replica Sets are **not** the same, though Sharding may be used atop Replica Sets. More information regarding Sharding can be found the MongoDB Sharding (dummy link). 

# Docker-Compose
Replica Sets are a group of MongoDB instances/nodes working together to provide a form of data redundency, and thus multiple docker instances are required. While this can be done using multiple `docker run` commands, it is not practical as the number of arguments and options that are needed per container is much more than a simple Docker container. It is also no longer viable to manually key in long command lines with larger Replica Sets as increasing the number of nodes in the Replica Set would increase the number of commands exponentially, hence it is advised to use `docker-compose` when deploying Replica Sets

## Converting a standard `docker run` command into a docker compose file
If you are not familiar with the docker compose file syntax, [this](https://www.composerize.com/) is a very useful tool to convert standard `docker-run` commands into docker compose files. 

# Procedure
## Pre-requisites 
## Cloning this repository

`git clone https://github.com/CalvinLoke/LearningWithDocker`

## Running the Docker nodes
Make sure you are in the correct directory of the cloned repository

`docker-compose -f MongoDB-in-Docker/docker-compose.yml up`

Modify the `docker-compose` command with `-d` if you need it to run in `detached` mode. If not, you will need to access the containers from another terminal instance. 

## Verify that the Docker nodes are running 
`docker ps`

## Login to one of the Docker instances
Any of the 3 containers will do, but the first node is preferred. 

`docker exec -it monogoNode1 mongo`

## Copy and paste the following configuration settings
```
config = {
 _id: "rs0",
 members:[
  {_id:0, "host": "mongoNode1:27017"}, 
  {_id:1, "host": "mongoNode2:27018"},  
  {_id:2, "host": "mongoNode3:27019"},  
 ]
};
```

Modify the node names, port numbers and or Repica Set name if needed. Use [JSCompress](https://jscompress.com/) if you need. The following is the compressed version:
`config={_id:"rs0",members:[{_id:0,host:"mongoNode1:27017"},{_id:1,host:"mongoNode2:27018"},{_id:2,host:"mongoNode3:27019"}]};`

## Run the Replica Set configuration command
`rs.initiate(config)`

## Verify that the Replica Set is up and running 
`rs.status()`

## Verify the replication 
In order to veify that the Replica Set is working, we would need to input some dummy/sample data. 

`show dbs`

Note that this command can only be run on the **Primary** node. Check that the current node you are in by identifying the prompt. It should be something along the lines of:

`<Replica Set Name>:PRIMARY> ` or `rs0:PRIMARY>` in the case of this example

### Add a test database to the current container 
`use <test DB name>` or `use testDB` to automatically create a new database with the desired name and set as the current active database to be used. 

### Add a collection 
`db.createCollection('<collectionName>') or db.createCollection('sampleCollection')` to add a collection to the database

### Add some sample data
`db.sampleCollection.insert({ "address0" : "dummyData0" })`

### Verify the data has been added
`db.sampleCollection.find()` 

Note that this command returns everything in the database. 

### Check secondary nodes
Logout of the current container either by using `Ctrl + C` or simply by typing `exit`. Login to the secondary node using a similar command:

`docker exec -it monogoNode2 mongo`

Before we can read any data from the secondary nodes, we must first do the following:

`rs.secondaryOk()` or `rs.slaveOk()`. But try not to use the second one as newer versions of MongoDB will not support it. 

Verify that the database can be found on the secondary node using:

`show dbs`

And further verify that the data is present on the secondary node by:

`db.sampleCollection.find()`

If the data can be found on the secondary node, congratulations, the Replica Set is up and running! 

## Shutting down the Replica Set
To shut down the Replica Set, simply stop the docker instances either by using `Ctrl-C` (if the docker containers are not running in detached mode) or by stopping the docker containers using the `docker container stop` command. 

Note that when you restart the containers, you will not need to reconfigure the Replica Sets as the `docker-compose` file as configured the containers to have bind volumes, meaning that it will have persistent data and configuration settings will carry over. 

# Removing the Replica Set 
< Will be adding this in due time> 

