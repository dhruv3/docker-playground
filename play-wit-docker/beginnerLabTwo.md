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
