# Section 1
* Containers reduce complexity.
* 80/20: 80% time spent on maintainence and 20% on innovations.

# Section 2
* Docker starts vm on your mac and then runs containers on it.
* Installing Docker on Mac: Don't use Homebrew as it just installs Docker CLI. Docker CLI is just used to talk to a remote server and doesn't do any other thing. It does not have a docker server.
* Get `dmg` from docker site and setup like normal app. Make sure in Docker Desktop, `file sharing` has the directory where you'll store your code.
* Setup Bash Completion to ease your work.

# Section 3
* Engine also called the server. It runs in the background of your machine. On mac you call it a daemon. 
* CLI is talking to server and returning its value and client value as well.
* New cmd format: `docker <command> <sub-command>`
## Container vs VM
* Containers are **NOT** mini VMs.
* Container are processes and limited to what resources they can access.
* If you run mongo on your container and then go to your host machine and look at running processes. you will find mongo running from a host pov. this implies that container are just processes and not VMs.
## Container vs Image
* An Image is the application we want to run. It is the source code, binary and whatever we need to run our app.
* A Container is an instance of that image running as a process.
* You can have many containers running off the same image.
```bash
docker container run --publish 80:80 nginx
```
* Downloads the latest version (nginx:latest by default)
* Creates new container based on that image and prepares to start
* Gives it a virtual IP on a private network inside docker engine
* Opens up port 80 on host and forwards to port 80 in container
* Starts container by using the CMD in the image Dockerfile
* Docker port publishing format of `<public port>:<container port>`. 
* Port conflict errors occur when multiple services are running on the same published port on the same host, not in multiple containers. 
* Each container gets its own internal IP behind NAT, so the ports won't conflict across two container network interfaces.
## Getting a Shell inside container
```bash
docker container run -it
docker container exec -it
```
* No ssh needed as docker cli can help you gain access.
* `exec` runs an additional process on the container and it doesn't affect the root process at all. So if you exit the shell it won't close your container
## Docker Network
* When you start a container you are trying to connect to docker network that is running in the background.
* Each container connected to a private virtual network "bridge". All containers on a virtual network can talk to each other.
* Each virtual network routes through NAT firewall on host IP
* Best practice is to create a new virtual network for each app
* Docker uses container names as DNS names to comm with each other.
* Every new network gets a DNS resolution feature in it. So all the containers on it can be resolved using this DNS.
```bash
docker network create dns-rr

docker run -itd --network=dns-rr -network-alias=search elasticsearch:2

docker run -it --rm --net=dns-rr alpine nslookup search

docker run -it --rm --net=dns-rr centos curl -s search:9200
```
* Solution for DNS Round Robin Test

# Section 4: Container Images
## What's in an Image?
* App binaries and dependencies
* Metadata about the image data and how to run the image
* Not a complete OS. No kernel, kernel modules (e.g. drivers). Host provides the kernel.
## Image Creation and Storage
* Created Using a Dockerfile Or committing a containers changes back to an image.
* Usually use a Dockerfile recipe to create them.
* Images aren't ideal for persistent data
  * Mount a host file system path into container
  * Use `docker volume` to create storage for unique/persistent data
## Image and Layers
* Images are made up of file system changes and metadata
* Each layer is uniquely identified and only stored once on a host.
* A Docker image is built up from a series of layers. Each layer represents an instruction in the image’s Dockerfile.
* Detailed description of Image and Layers. **COPY ON WRITE**: [link](https://docs.docker.com/v17.09/engine/userguide/storagedriver/imagesandcontainers/)
* Each line in dockerfile is a layer and order matters. 
* It is read from a top-down manner.
* Try to ensure that things that change the most are placed on bottom of your dockerfile and least changed stuff is on top of the file.
* Docker caches your build steps. It generates a hash after every step in Dockerfile. If the file has not changed then it'll use the existing cached layer.

# Section 5: Docker Volumes
* Your goal is to design such that when you make a change you redeploy a container and don't update the older one.
* This is a design goal. it's called "immutable infra".
* In order to ensure this you have to keep unique data out of your container. 
* Persistant data: Volumes and Bind Mounts
* Docker has a UFS(Union File System).
* Creating named volumes to keep a track that which volume belongs to which container.
 ```bash
 docker container run .... -v mysql-db:/var/lib/mysql
 ```
* This will create a named vol called "mysql-db"
* `Bind Mounting`: Maps a host file or directory to a container file or directory. Can't use it in Dockerfile. Can be used in `docker run`(as it's on host we cannot use the Dockerfile). 
