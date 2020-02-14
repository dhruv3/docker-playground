# Docker images deeper dive
[Source](https://training.play-with-docker.com/docker-images/)

* **AIM:** Create an image from a container.
## Image creation from a container
```bash
docker container commit CONTAINER_ID

docker image tag IMAGE_ID ourfiglet

docker container run ourfiglet figlet hello
```
* Run the following command, using the ID, in order to commit the container and create an image out of it.
* This approach is not the recommended one as it is not very portable.

## Image creation using a Dockerfile
* Basically, in this method, it just uses a base image that embeds `alpine` and a `Node.js runtime` so we do not have to install it ourself. 

## ENTRYPOINT vs COMMAND
```docker
FROM alpine
ENTRYPOINT ["ping"]
CMD ["localhost"]
```
* Here, we define the **ping** command as the ENTRYPOINT and the **localhost** as the CMD, the command that will be ran by default is the concatenation of ENTRYPOINT and CMD: **ping localhost**.
* If you run this Dockerfile without params it will output the result of **ping localhost**. 
```bash
docker container run ping:v0.1 8.8.8.8
```
* You can override **localhost** by adding a param while running the image.

## Filesystem exploration
* [/var Dir](http://www.linfo.org/var.html)
* [/var Dir doubt](https://askubuntu.com/questions/574609/why-do-i-not-see-my-bin-var-etc-directories-in-my-root-partition)
* `/var/lib/docker/overlay2` folder where the image and container layers are stored.

# Docker Volumes
[Source](https://training.play-with-docker.com/docker-volumes/)
## Data persistency without a volume?
```bash
docker container run --name c1 -ti alpine sh
mkdir /data && cd /data && touch hello.txt
exit
docker container inspect c1
ls /var/lib/docker/overlay2/[YOUR_ID]/diff/data
docker container rm c1
```
* We created a data folder and placed hello.txt file inside. Then removed container and checked if the data folder still exits(It wasn't there).
## Defining a volume in a Dockerfile
