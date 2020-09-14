# DockerHelpsheet
Serves as a personal document for docker and the different commands used. 

# Preface and prequisites
At the time of writing this document, I am still a very inexperienced user of Docker and lack comprehensive understanding of containers. Hence, do take this documentation with a grain of salt. 

## Prerequisites
It is assumed that the reader is aware of what Docker is and have installed Docker into their system. The commands utilized here are for a Command Line Interface (CLI) based environments, as Docker is Linux based. 

# Commands
This section will contain all the necessary commands that are required to utilize Docker effectively. They seek to create, destroy and manage the different components required to operate Docker, its containers and imagees. 

## Docker run command
Docker run is one of the most important docker commands to use as it **creates** the docker container from a container image.

`docker run` 

The Docker run command has many different arguments that will affect the container's behaviour. The most basic docker docker build command is 

`docker run <image name>`

This will simply create a container based on a container image. Note that if the image is not found locally, Docker will attempt to search for the image on Docker.hub and will download it. 

The general format of the `docker run` command is as follows:

`docker run [options] <image name> [command] [ARG...]`

The `[options]` field will directly affect the behaviour of the container's operation. The order at which the different arguments that are input *does not* matter. 

While the Docker documentation lists out a wide variety of different arugments for the options field, here are the few common ones that are used:

| Command | What it does |
|---------|--------------|
| -d      | Runs the container in detached mode |
| -it     | Runs the container in interactive mode |
| --rm    | Removes container when it exits |
| -p      | Publishes port(s) to the host |
| --expose | Exposes port(s), similar to the publish option |
| -- name | Assigns a name to the container |

### Detached and interactive
When running in `detached` mode,the container will run in the background, and will not display any input or output. 

Interactive. 

### Port publishing
The run command when used with the port publishing option will publish the port to the host, thus making the container/service available to the outside world. Different services will utilize different ports, and thus one should check the ports utilized by the service that they are deploying. 

The simplest way of publishing or exposing the port is as follows:

`docker run -p 8080:80` 

This will map port 80 in the container to port 8080 on the Docker host. Note that the default protocol used will be **TCP**. To use UDP instead, simply modify the command to be:

`docker run -p 8080:80/udp`

If there is a need to specify the host IP address (eg 192.168.20) modify the command to:

`docker run -p 192.168.20:8080:80`

Conversely, append the `/udp` if UDP is preferred

`docker run -p 192.168.20:8080:80/udp`

If you would like to read in-depth about the `docker run`command, I would highly recommend you to read the [document](https://docs.docker.com/engine/reference/commandline/run/).

## Docker pull command
A fresh installation of Docker will lack any images. One can either build an image from scratch, or they can pull an image from a repository. Docker Hub is such a repository, and you can check the variety of images available [here](https://hub.docker.com/).

To pull an image from DockerHub, simply use the following command:

`pull <image name>`

This will simply pull the latest image from the repository. If you would like to pull a specific version, modify the command as follows:

`pull <image name>:<version number>`

This command is not neccessary, and as mentioned earlier, if you are trying to run a container from an image that does not exist locally, Docker will automatically pull the image from the repository. 

# Common commands to manage images and containers
This section will contain the different commands that are useful to manage your images and containers. Simply copy and paste it into your CLI. 

## Removing stopped containers
`$ docker rm $(docker ps -a -q)`




