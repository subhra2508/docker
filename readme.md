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
- create one-linear developer enviroment startups
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

### Swarm Mode : Built-In Orchestration

container Everywhere = new problems

- How do we automate container lifecycle ?
- How can we easily scale out/in/up/down ?
- how can we ensure our container are re-created if they fail?
- how can we  replace container without downtime (blue/green deploy)?
- how can we control/track where containers get started ?
- how can we create cross-node virtual networks?
- how can we ensure only trusted servers run our containers?
- how can we store secrets,keys,passwords and get them to the right container(and only that 
container)?


- swarm mode is a clustering solution built inside docker
- not related to swarm "classic" for pre-1.12 versions
- added in 1.12(summer 2016) via swarmkit toolkit
- enhanced in 1.13(january 2017) via stacks and screts
- not enabled by default, new commands once enabled
   - docker swarm
   - docker node
   - docker service
   - docker stack
   - docker secret

### Docker swarm
- first to check whether swarm is active or not run command -> docker info
- to active swarm -> docker swarm init
- so what happened when you run this
   - Lots of PKI and security automation
        - root signing certificate created for our swarm
        - certificate is issued for first manager node
        - join tokens are created
   - Raft database created to store root CA,configs and secrets
        - encrypted by default on disk(1.13+)
        - no need for another key/value system to hold orchestration/secrets
        - replicates logs amongst manager via mutual TLS in "control panel"

- docker node --help ->check what you can do with a node
- docker swarm --help -> other helps regarding swarm
- docker swarm init --advertise-addr <MANAGER-IP> -> to create a manager node 
- docker service create alpine ping 8.8.8.8 -> this create a service which ping to google dns
- docker service ls -> list out the services currently running with their replicas ex: 1/1
- docker service ps <service name> -> this gives us the container which are running in this service
- docker service update <ID> --replicas 3 ->Inorder to scale a service up with restart the previous ones

- docker container rm -f <container_name> -> to remove a container,as soon as we remove the container the docker swarm launch a another container to replace it , because we gave replicas of 3

### create a 3 node swarm cluster

- docker swarm init --adevertise-addr <manager-ip> -> to make this node manager / return a manager token to join other node into the network
- docker swarm join --token SWMTKN-1-3y1i626h6bxrsi751zao7nzn8izo78og20rxlttq12xr8dw4hn-b65mjctl8ytny9ruc2cobk1dm 192.168.0.28:2377
- docker swarm join-token manager -> inorder to join a worker as a manager 

- docker service create --replicas 3 alpine ping 8.8.8.8
### overlay multi-host networking
- --driver overlay -> when creating network (basically it will create a swarm wide virtual network bridge so we don't have to mess with the network config) 
- For container-to-container traffic inside a single swarm 
- optional IpSec (AES) inorder to incryption on network
- each service can be connected to multiple network (e.g front-end,backend)

- docker network create --driver overlay mydrupal -> create a virtual network bridge
- docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=mypass postgres -> to create postgres service

- docker service ps psql -> check the running node/details
- docker container logs psql.1.skdjfalskd
- docker service create --name drupal --network mydrupal -p 80:80 drupal
- docker service ls -> check all the services
- docker service ps mydrupal -> check it's running on a worker node
- if you are using docker play click on the port80 to show your webapp

### Routing mesh
- now if you go to any of the ip address you show the drupal is running
- but we know that the drupal service is only running on node-3
- this magic is due to the routing mesh
	- routes ingress(incoming packets for a service to proper task
	- spans all nodes in swarm
	- uses ipvs from linux kernal
	- load balance swarm services across their tasks
	- basically it scaning all the nodes and send it to the node
- two ways this works:
- when the frontend talking to backend it's not directly talking to backend , it's talking to the virtual network in (swarm particulary)
- so when you are in a same virtual network they are talking to virtual network and swarm automatically balances the load 
- when external traffic incoming to published port , it can hit any of the node published port it's listening ,then it will reroute the traffic to exact node 
- we didn't have to enable this , it's all by default swarm config

- This is stateless load balancing
- This Load balancer is operate at OSI (layer3 (TCP) ,not layer 4 DNS
- If you want to run a website at a same port,then you have a problem:
	- To solve this issue we can use :
	- Nginx or HAProxy LB proxy, or:
- Docker enterprise edition have that capabilities of web proxy,

	

### let's see routing mesh in action
- docker service create --name search --replicas 3 -p 9200:9200 elasticsearch:2
 
- docker service ps search -> check it will distrubting load across all nodes

### stacks: production grade compose :

- In 1.13 Docker adds a new layer  of abstraction to swarm called stacks
- stacks accept compose files as their declarative definition for services,networks,and volumes
- we use docker stack deploy rather then docker service create
- stacks manages all those objects for use,including overlay network per stack.Adds stack name to start of their name
- New deploy : key in compose file. can't do build
- compose now ignores deploy ,swarm ignores build

### Secrets Storage :
- Easiest "secure" solution for storing secrets in swarm 
- What is a Secret?
   - username and passwords
   - TLS certificates and keys
   - SSH keys
   - any data you would prefer not be "on front page of news"
- As of docker 1.13.0 Swarm Raft DB is encrypted on disk
- Only stored on disk on Manger nodes
- Default is Manager and Workers "control plane" is TLS+Mutual AUth
- Secrets are first stored in Swarm, then assigned to a Service
- Only containers in assigned Service can see them
- They look like files in container but are actually in-memory fs
- ./run/secrets/<secret_name> or
- /run/secrets/<secret_alias>
- Local docker-compose can use file based secrets,but not secure

### Swarm App Lifecycle :
- Single set of compose files for:
- Local docker-compose up development environment
- Remote docker-compose up CI environment
- Remote docker stack deploy production environment
- Note:docker-compose -f a.yml -f b.yml config mostly works
- Note:Compose extends:(may be not work with stack)

- Swarm updates examples:
  - Just update the image used to a newer version
  - docker service update --image myapp:1.2.1 <servicename>
- Adding an environment variable and remove a port
  - docker service update --env-add NODE_ENV=production --publish-rm 8080
- Change number of replicas of two services
  - docker service scale web=8 api=6
- Swarm updates in stack files
- same command,just edit the YAML file,then
- docker stack deploy -c file.yml <stackname>
- Docker healthcheck commands:
  - It expects exit 0 (ok) or exit 1 (error)
  - Three container states:starting,healthy,unhealthy
  






### Learning >>>
- what is kubernates and how we use it with docker 

