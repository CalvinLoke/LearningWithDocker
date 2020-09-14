# Preface
While MongoDB is not directly related to Docker, it can be run as a service/container in a Docker environment. Hence, basic understanding of Docker and the different commands are recommended. 

# Setting up Mongo
## Installing MongoDB in Docker
The image for MongoDB exists on DockerHub, and can be pulled from the repository using the following command: 

`docker pull mongo` 

It will pull the latest version by default, and if there is a need to pull an alternative or a different version, modify the command as follows:

`docker pull mongo:<version number>` 

To ensure that mongo has been successfully installed into your system, verify by checking if `mongo` exists in `docker images`. 

