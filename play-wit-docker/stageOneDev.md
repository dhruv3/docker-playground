# Docker for Developers Stage 1

## Docker for Beginners - Linux

### Task 1: Run some simple Docker containers
* Three main ways to use containers:
  * **To run a single task**: This could be a shell script or a custom app.
  * **Interactively**: This connects you to the container similar to the way you SSH into a remote server.
  * **In the background**: For long-running services like websites and databases.
#### Run a single task in an Alpine Linux container
* In this step we’re going to start a new container and tell it to run the `hostname` command.
```bash
docker container run alpine hostname
```
* Docker keeps a container running as long as the process it started inside the container is still running. In this case the `hostname` process exits as soon as the output is written. This means the container stops. However, Docker doesn’t delete resources by default, so the container still exists in the `Exited` state.
* List all containers
```bash
docker container ls --all
```
* So you can run a container and it will execute whatever script is present in it. Therefore, you don't need to worry about actual script or config information.

#### Run an interactive Ubuntu container
* Run a Docker container and access its shell.
```bash
docker container run --interactive --tty --rm ubuntu bash
```
* `--rm` tells Docker to go ahead and remove the container when it’s done executing.
* We’re also telling the container to run `bash` as its main process (PID 1).
* `ls /` will list the contents of the root director in the container, `ps aux` will show running processes in the container, `cat /etc/issue` will show which Linux distro the container is running.
* The distribution of Linux inside the container does not need to match the distribution of Linux running on the Docker host.
* Interactive containers are useful when you are putting together your own image. You can run a container and verify all the steps you need to deploy your app, and capture them in a Dockerfile.

#### Run a background MySQL container
* Run a new MySQL container with the following command.
```bash
 docker container run \
 --detach \
 --name mydb \
 -e MYSQL_ROOT_PASSWORD=my-secret-pw \
 mysql:latest
```
* --detach will run the container in the background.
* --name will name it mydb.
* -e will use an environment variable to specify the root password (NOTE: This should never be done in production).
* This shows the logs from the MySQL Docker container.
```bash
docker container logs mydb
```
* Processes running inside the container.
```bash
docker container top mydb
```
* `docker container exec` allows you to run a command inside a container.
```bash
 docker exec -it mydb \
 mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
 ```
 * You can also use `docker container exec` to connect to a new shell process inside an already-running container. Executing the command below will give you an interactive shell (`sh`) inside your MySQL container.
 ```bash
 docker exec -it mydb sh
 ```
 
