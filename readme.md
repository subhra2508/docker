# Getting Started With Docker

### docker commands
- docker images - (list out all the images present)
- docker ps - (list out all the running container)
- docker run - (start the image)
- attach   -     Attach local standard input, output, and error streams to a running container
- build    -     Build an image from a Dockerfile
- commit   -    Create a new image from a container's changes
- cp       -     Copy files/folders between a container and the local filesystem
- create   -     Create a new container
- diff     -     Inspect changes to files or directories on a container's filesystem
- events   -    Get real time events from the server
- exec     -    Run a command in a running container (docker exec -it image_id /bin/bash)
- export   -    Export a container's filesystem as a tar archive
- history  -    Show the history of an image
- images   -   List images
- import   -   Import the contents from a tarball to create a filesystem image
- info     -    Display system-wide information
- inspect  -   Return low-level information on Docker objects
- kill     -   Kill one or more running containers
- load     -   Load an image from a tar archive or STDIN
- login    -    Log in to a Docker registry
- logout   -    Log out from a Docker registry
- logs     -    Fetch the logs of a container
- pause    -   Pause all processes within one or more containers
- port     -    List port mappings or a specific mapping for the container
- ps       -     List containers
- pull     -    Pull an image or a repository from a registry
- push     -    Push an image or a repository to a registry
- rename   -    Rename a container
- restart  -   Restart one or more containers
- rm       -   Remove one or more containers
- rmi      -   Remove one or more images
- run      -   Run a command in a new container
- save     -   Save one or more images to a tar archive (streamed to STDOUT by default)
- search   -   Search the Docker Hub for images
- start    -   Start one or more stopped containers
- stats    -   Display a live stream of container(s) resource usage statistics
- stop     -   Stop one or more running containers
- tag      -   Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
- top      -   Display the running processes of a container
- unpause  -   Unpause all processes within one or more containers
- update   -   Update configuration of one or more containers
- version  -  Show the Docker version information
- wait     -   Block until one or more containers stop, then print their exit codes

- docker -d(for detached mode) -p(port no)yourport:dockerport --name(name your container) 

next runing an apache webserver
sudo docker container run -d --name webserver -p 8080:80  httpd

- docker container top - process list in one container

- docker container inspect - details of one container config

- docker container stats - performance stats for all containers

- docker container run -it - start new container interactively

- docker container exec -it - run additional command in existing container

- alpine is ideal for container images its less in size about 4mb

# DOCKER NETWORKS: CONCEPT

- Each container connected to a private virtual network "bridge"
- Each virtual network routes through NAT firewall on host ip
- All containers on a virtual network can talk to each other without -p

- Best practice is to create a new virtual network for each app:
	- network "my_web_app" for mysql and php/apache containers
	- network "my_api" for mongo and nodejs containers

- "Batteries included,But Removable"
	- Defaults work well in many cases,but easy to swap out parts to customize it

- make new virtula networks

- Attach containers to more then one virtual network(or none)

- skip virtual network and use host ip(--net=host)
- use different Docker network drivers to gain new abilites

- docker container inspect --format '{{.NetworkSettings.IPAddress }}' webhost

# Docker Networks: CLI management

- show networks - docker network ls
	--network bridge - Default docker virtual network, which is NAT'ed behind host ip
	--network host - it gains performance by skipping virtual networks but sacrifices
	  security of container model
	--removes eth0 and only leaves you with localhost interface in container
	--network driver Built-in or 3rd party extension that give you virtual network 
	  features	
	
- inspect a network - docker network inspect
- create a network - docker network create --driver
- attach a network to container - docker network connect
- detach a network from container - docker network disconnect

- docker network connect - Dynamically creates a NIC in a container on an existing virtual network.

# Docker Networks:DNS
- Understand how DNS work is the key to easy inter-container comms
- see how it works by default with custom networks
- Learn how to use --link to enable DNS on default bridge network
- "Docker DNS" Docker daemon has a built-in DNS server that container use by default

### Foget IP's
- static ip's and using ip's for talking to containers is an anti-pattern.Do your best to avoid it.

### what's In An Image(And what isn't):
- app binaries and dependencies(becoz the host os provides the kernal and all)
- metadata about the image data and how to run the image
- Official definition:"an image is an ordered collection of root filesystem changes and the corresponding execution parameters for use within a container runtime."
- Not a complete os.No kernal,kernal modules (e.g drivers)
- docker history nginx => gives all the detailed history of that images and it's layers

- docker image inspect => return json metadata about the image(basically how images is expect to be run)

### important
- images are made up of file system changes and metadata
- Each layer is uniquely identified and only stored once on a host
- This save storage space on host and transfer time on push/pull
- A container is just a single read/write layer on top of image
- docker image history and inspect commands can teach use

### notes
- image layer
- union file system
- history and inspect commands
- copy on write

### image tagging and pushing to docker hub

- all about image tags
- How to upload to docker hub

- Usage:  docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
- docker image tag nginx subhrajyoti/nginx
- docker image tag nginx subhrajyoti/nginx subhrajyoti/nginx:testing ->additional tag
- docker image push
- docker login - login for hub (it's store a config file in local machine)
- docker logout - logout for hub


### The Dockerfile basics
- 

### container lifetime and persistent data :

- container are usually immutable and ephemeral
- "immutable infrastructure": only re-deploy container,never change
- This is ideal scenario,but what about databases, or unique data ?
- Docker give us features to ensure these "separation of concern"
- this is know as "persistent data"
- two ways : volumes and Bind mounts
- volumes: make special location outside of container ufs
- Bind Mounts : link container path to host path
- Named volumes:
	- friendly way to assign vols to containers
	- named volumes start with -v tag with name and semicolon
	- docker container run -d --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=True -v mysql-db:/var/lib/mysql mysql
	- docker volume ls : list out all the volumes
	- docker volume mysql-db inspect : inspect the volume
- docker volume Create:
	- create volume ahead of time (not necessary for local development)

### Persistent Data:Bind Mounting

- maps a host file or directory to a container file or directory
- Basically just two locations pointing to the same files
- Again,it skips UFS, and host files overwrite any in container
- bind mount are host specific so you can't use these in dockerfile , you can only use it in
runtime ,container run
- paths:
	- ... run -v /Users/bret/stuff:/path/container (linux/mac)
	- ... run -v //c/Users/bret/stuff:/path/container (windows)
	

### Docker compose 
- configure relationships between containers
- save our docker container run settings in easy-to-read file
- create one-liner developer enviroment startups
- It comprised of 2 separate but related things:
- 1.YAML-formatted file that describes our solution options for:
   - containers
   - networks
   - volumes
- 2. A CLI tool docker-compose used for local dev/test automation with those YAML files
#### docker-compose.yml
- compose YAML format has it's own versions:1,2,2.1,3,3.1
- YAML file can be used with docker-compose command for local docker automation or...
- with docker directly in production with swarm (as of v1.13)
- docker-compose --help
- docker-compose.yml is default filename,but any filename can be used with docker-compose -f

```yml
version: '3.1'  # if no version is specified then v1 is assumed. Recommend v2 minimum

services:  # containers. same as docker run
  servicename: # a friendly name. this is also DNS name inside network
    image: # Optional if you use build:
    command: # Optional, replace the default CMD specified by the image
    environment: # Optional, same as -e in docker run
    volumes: # Optional, same as -v in docker run
  servicename2:

volumes: # Optional, same as docker volume create

networks: # Optional, same as docker network create
```
#### docker-compose cli
- cli tool come with docker for windows/mac,but separate download for linux
- not a production-grade tool but ideal for local development and test
- two most common commands are
  - docker-compose up
  - docker-compose down
  
#### using compose to Build
- compose can also build your custom images
- will build them with docker-compose up if not found in cache
- also rebuild with docker-compose build
- Great for complex builds that have lots of vars or build args



### Learning >>>

- How to start using docker
- why we use docker
- how to dockerize django app
- how to dockerize any app
- what are the benefits and disadvantage of docker
- what is kubernates and how we use it with docker 
- what is nginx- its a webserver,load balancer,http cache,reverse proxy
