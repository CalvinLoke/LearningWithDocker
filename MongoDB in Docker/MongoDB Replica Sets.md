# Replica Sets in MongoDB
Replica Sets in MongoDB serve as a form of redundency. And in the even that one of the MongoDB instances fails, the other instances in the same cluster can take over. Note that Sharding and Replica Sets are **not** the same, though Sharding may be used atop Replica Sets. More information regarding Sharding can be found the MongoDB Sharding (dummy link). 

# Docker-Compose
Replica Sets are a group of MongoDB instances/nodes working together to provide a form of data redundency, and thus multiple docker instances are required. While this can be done using multiple `docker run` commands, it is not practical as the number of arguments and options that are needed per container is much more than a simple Docker container. It is also no longer viable to manually key in long command lines with larger Replica Sets as increasing the number of nodes in the Replica Set would increase the number of commands exponentially, hence it is advised to use `docker-compose` when deploying Replica Sets

## Converting a standard `docker run` command into a docker compose file
If you are not familiar with the docker compose file syntax, [this](https://www.composerize.com/) is a very useful tool to convert standard `docker-run` commands into docker compose files. 

# Procedure
## Cloning this repository

`git clone https://github.com/CalvinLoke/LearningWithDocker`

## Running the Docker nodes

Make sure you are in the correct directory of the cloned repository

`docker-compose -f MongoDB in Docker/docker-compose.yml up`